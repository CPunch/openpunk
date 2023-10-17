---
title: "Go's Regex Repetition limit is a little sad"
date: 2023-10-16
author: CPunch
tags: ["Go", "Regex"]
---

Recently while working on a [passion project with a friend](https://github.com/ArmanHZ/go-detect-that-hash), it's a simple hash detector but written in Go. We were porting a large list of regular expressions from a python-based project since redoing work is annoying and a fool's errand. The project we were stealing regular expressions from was [this one](https://github.com/psypanda/hashID/blob/7e8473a823060e56d4b6090a98591e252bd9505e/hashid.py#L36). We ran into a strange issue where the Go regex engine would panic when we tried to compile a regex with a repetition limit of 2048 with this ominous error message:

```
panic: regexp: Compile(`^grub\.pbkdf2\.sha512\.[0-9]+\.([a-f0-9]{128,2048}\.|[0-9]+\.)?[a-f0-9]{128}$`): error parsing regexp: invalid repeat count: `{128,2048}`
```

This was a little strange, as the python regex engine had no issue compiling the same regex. After some digging, we found that the Go regex engine has a [hard-coded repetition limit of 1000](https://github.com/golang/go/issues/7252). This is a little sad, as it means that we can't use the same regular expressions as the python project without some modification. In the previous issue I linked, there is a 'simple' proposed solution that reads:

> For the benefit of anyone coming across this issue in the future, the trivial workaround
> is to split the quantified expression into repeated copies of the desired match. See
> http://play.golang.org/p/XjdKuTKx3t for an example for /x{n}/. /x{n,}/ as originally
> encountered is not much more complicated.


While this would work, I was also more inclined to find out *why* was there a repetition limit? And [over here](https://groups.google.com/g/golang-nuts/c/x4dbsyd0IFY/m/MKBgxU9YDQAJ) Rob Pike one of the original authors of Go tells us himself:

> It is to protect the regexp engine from overly expensive computations, as the
> repetition can introduce quadratic behavior in the compiler. The Go engine is
> concerned about pathological execution - not all engines have this property
> (see https://swtch.com/~rsc/regexp/regexp1.html) - and is being careful.

> The 1000 is arbitrary but - speaking just for myself - a repeat count as high
> as a thousand tells me that a regular expression is the wrong mechanism for
> this problem. A simple loop would be vastly more efficient
> (see https://commandcenter.blogspot.com/2011/08/regular-expressions-in-lexing-and.html).

> You are using a steamroller to press a shirt.

> -rob

God has spoken I guess.

## Our Solution

Following God's commandments, we simply rewrote the offending regular expressions as a 'simple loop' string matching functions. For example, the following regex:

```
^grub\.pbkdf2\.sha512\.[0-9]+\.([a-f0-9]{128,2048}\.|[0-9]+\.)?[a-f0-9]{128}$
```

became this matching function:

```go
func checkAlphaNumeric(input string) bool {
	for _, char := range input {
		if !((char >= '0' && char <= '9') || (char >= 'a' && char <= 'f')) {
			return false
		}
	}

	return true
}

func match(input string) bool {
  parts := strings.Split(input, ".")
  if len(parts) != 6 {
    return false
  }

  if parts[0] != "grub" || parts[1] != "pbkdf2" || parts[2] != "sha512" {
    return false
  }

  numPart := parts[3]
  if _, err := strconv.Atoi(numPart); err != nil {
    return false
  }

  saltPart := parts[4]
  if len(saltPart) != 128 && len(saltPart) != 2048 {
    return false
  }

  if !checkAlphaNumeric(saltPart) {
    return false
  }

  hashPart := parts[5]
  if len(hashPart) != 128 {
    return false
  }

  if !checkAlphaNumeric(hashPart) {
    return false
  }

  return true
}
```

This has the added benefit of being much more performant than the regex, as we can skip the regex engine. This is a much more efficient solution, and it also allows us to avoid the repetition limit issue entirely! I'm not sure if this is the best solution, but it works for us.