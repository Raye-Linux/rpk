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
rpk -r /mnt install ...   work on a system mounted somewhere else
```

The repo comes from `REPO=` in `/etc/rpk.conf`. `RPK_REPO` in the
environment overrides it. Installed packages are tracked under
`/var/db/rpk/<name>/` (`version`, `files`, `depends`).

Packages are built from the recipes in
[Raye-Linux/packages](https://github.com/Raye-Linux/packages).

## INDEX

One line per package, fields separated by `|`:

```
name|version-release|sha256|depends|description
dropbear|2026.94-1|e3b0c4...||small ssh server and client
```

The tarball for that line is `name-version-release.tar.xz`.

## License

MIT, see [LICENSE](LICENSE).
