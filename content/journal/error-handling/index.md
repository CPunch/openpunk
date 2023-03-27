---
title: "Try/Catch in Modern C"
date: 2022-05-05
author: CPunch
repo: "https://github.com/CPunch/SError"
tags: ["C"]
---

Error handling in C has always been a pain. Most small projects have a simple return based error handling solution. For example,

```C
#include <stdlib.h>
#include <stdio.h>
#include <stdbool.h>

bool isEven(int i) {
    return i % 2 == 0;
}

int main() {
    int x = 3;
    if (!isEven(x)) {
        printf("[ERROR]: %d is not even!\n", x);
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

Our 'error' in this case is that our variable `x` is odd. While this solution works very well for smaller projects, this can create problems down the road. For example,

```C
#include <stdlib.h>
#include <stdio.h>
#include <stdbool.h>

bool isEven(int i) {
    return i % 2 == 0;
}

int *allocEvenInt(int i) {
    int *buf = (int*)malloc(sizeof(int));

    /* passed integer isn't even! */
    if (!isEven(i))
        return NULL;

    /* we failed to allocate! */
    if (buf == NULL)
        return NULL;

    *buf = i;
    return buf;
}

int main() {
    int *buf = allocEvenInt(3);

    if (buf == NULL) {
        printf("[ERROR]: Couldn't create even integer on heap!\n");
        return EXIT_FAILURE;
    }

    free(buf);
    return EXIT_SUCCESS;
}
```

Now we have a problem, when allocEvenInt() returns NULL, it *could* be because the integer wasn't even ***or*** it could be that malloc() failed. This ambiguity isn't very important for this simple program, but you can see how this could become a problem in the future for larger codebases. So what's the solution?

## longjmp/setjmp magic

Luckily for us, this issue has been solved a long time ago. longjmp/setjmp allows you to reset your stack, jumping back to a pre-error environment. Let's rewrite our second example using longjmp/setjmp.

```C
#include <stdlib.h>
#include <stdio.h>
#include <stdbool.h>
#include <setjmp.h>

enum {
    ERROR_MALLOC = 1,
    ERROR_NOTEVEN
};

jmp_buf errorHandler;

bool isEven(int i) {
    return i % 2 == 0;
}

int *allocEvenInt(int i) {
    int *buf = (int*)malloc(sizeof(int));

    /* passed integer isn't even! */
    if (!isEven(i))
        longjmp(errorHandler, ERROR_NOTEVEN);

    /* we failed to allocate! */
    if (buf == NULL)
        longjmp(errorHandler, ERROR_MALLOC);

    *buf = i;
    return buf;
}

int main() {
    int errCode;
    int *buf;

    if ((errCode = setjmp(errorHandler)) == 0) {
        buf = allocEvenInt(3);
    } else { /* an error was thrown! */
        printf("[ERROR]: ");

        switch (errCode) {
            case ERROR_MALLOC: printf("Malloc failed!\n"); break;
            case ERROR_NOTEVEN: printf("Integer is not even!\n"); break;
            default: break;
        }

        return EXIT_FAILURE;
    }

    free(buf);
    return EXIT_SUCCESS;
}
```

While this does work, we can definitely do better. Let's move the printf for error reporting into the function, and add some macro magic.

```C
#include <stdlib.h>
#include <stdio.h>
#include <stdbool.h>
#include <setjmp.h>

enum {
    ERROR_MALLOC = 1,
    ERROR_NOTEVEN
};

jmp_buf errorHandler;

#define __STRINGIZE(x) __STRINGIZE2(x)
#define __STRINGIZE2(x) #x
#define __LINE_STRING__ __STRINGIZE(__LINE__)

#define SIMPLE_ERROR(err, ...) do { \
    fprintf(stderr, __FILE__ ":" __LINE_STRING__  " [ERROR]: " __VA_ARGS__); \
    longjmp(errorHandler, err); \
} while(0);

bool isEven(int i) {
    return i % 2 == 0;
}

int *allocEvenInt(int i) {
    int *buf = (int*)malloc(sizeof(int));

    /* passed integer isn't even! */
    if (!isEven(i))
        SIMPLE_ERROR(ERROR_NOTEVEN, "%d is not even!\n", i);

    /* we failed to allocate! */
    if (buf == NULL)
        SIMPLE_ERROR(ERROR_MALLOC, "Malloc failed!\n");

    *buf = i;
    return buf;
}

int main() {
    int errCode;
    int *buf;

    if ((errCode = setjmp(errorHandler)) == 0) {
        buf = allocEvenInt(2);
    } else { /* an error was thrown! */
        /* we can do whatever with the error code here */
        return EXIT_FAILURE;
    }

    free(buf);
    return EXIT_SUCCESS;
}

```

Thats a lot better. I took this basic idea and wrote a [tiny macro header file](https://github.com/CPunch/SError) for using this, including try/catch blocks.

```C
// helloworld.c
#include <stdio.h>
#include "serror.h"

void try() {
    puts("lo ");
    SIMPLE_ERROR(1, "Wor");
    puts("never called\n");
}

int main() {
    int errCode;
    SIMPLE_TRY(errCode)
        puts("Hel");
        try();
    SIMPLE_CATCH
        puts("ld!");
    SIMPLE_TRYEND

    SIMPLE_ERROR(1, "\n");

    puts("never called\n");
    return 0;
}
```

{{< img out.png "600x q70 jpg" "Example output." >}}