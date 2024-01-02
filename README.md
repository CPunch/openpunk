# OpenPunk

This repository holds the frontend source for [openpunk.com](https://openpunk.com). `layouts/` holds my Go Template for the [hugo generator](https://gohugo.io), while the `content/` directory holds all of my writings (blog & journal) for the site.

In production the site is generated using the following flags
```
$ hugo --cleanDestinationDir --minify -d /var/www/openpunk.com -b https://openpunk.com
```

For details on how it's deployed to [openpunk.com](https://openpunk.com) on my end, checkout this [ansible-playbook](https://github.com/CPunch/openpunk-ansible).

## Usage

Run a local copy of the site using:
```
$ hugo server
```
Then visit the site at [http://localhost:1313/](http://localhost:1313/)
