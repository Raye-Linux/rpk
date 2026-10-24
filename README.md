# rpk

The package manager of Raye Linux. One POSIX shell script, runs on
busybox.

Packages are tarballs laid out from `/`. A repo is a directory (or an
http URL) with the tarballs and an `INDEX` listing each one with its
version, sha256, dependencies and a short description. rpk checks the
sum, refuses to overwrite files that belong to another package, and
keeps a list of everything it unpacked, so removing or upgrading a
package leaves nothing behind.

```
rpk list                  what the repo has, * marks what's installed
rpk installed
rpk info dropbear
rpk install dropbear      dependencies come first
rpk upgrade               everything installed, up to the repo's version
rpk remove dropbear
rpk files dropbear
rpk owner /usr/bin/ssh
rpk index                 the repo's INDEX, after it's been checked
rpk -r /mnt install ...   work on a system mounted somewhere else
```

The repo comes from `REPO=` in `/etc/rpk.conf`, a directory or an
http(s) URL. `RPK_REPO` in the environment overrides it. With
`FALLBACK=` set as well, rpk uses that repo when `REPO` can't be
reached, which is how a mirror and a local copy go together. Installed
packages are tracked under `/var/db/rpk/<name>/` (`version`, `files`,
`depends`).

## Signatures

Every tarball is checked against the sha256 in the INDEX. The INDEX can
be signed too, with an ed25519 key, as `INDEX.sig` next to it:

```
openssl genpkey -algorithm ed25519 -out raye.key
openssl pkey -in raye.key -pubout -out raye.pub
openssl pkeyutl -sign -inkey raye.key -rawin -in INDEX -out INDEX.sig
```

Put `raye.pub` in `/etc/rpk/keys/` and rpk checks the signature every
time it reads an INDEX, and stops at one that isn't signed by any key
there. No keys, no check. `RPK_KEYS` points it at another directory.
The `snapshot` script in the packages repo does the signing for a whole
repo.

Packages are built from the recipes in
[Raye-Linux/packages](https://github.com/Raye-Linux/packages).

## Hooks

A few things are built from what several packages install: compiled
GSettings schemas, the MIME database. Packages that add to one of those
ship a script in `/usr/share/rpk/hooks/`, and after every install,
upgrade or removal rpk runs them all (chrooted, with `-r`). A hook just
regenerates its cache from what's there, so running one twice is fine.

## INDEX

One line per package, fields separated by `|`:

```
name|version-release|sha256|depends|description
dropbear|2026.94-1|e3b0c4...||small ssh server and client
```

The tarball for that line is `name-version-release.tar.xz`.

## License

MIT, see [LICENSE](LICENSE).
