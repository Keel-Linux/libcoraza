# libcoraza

Debian packaging of [libcoraza](https://github.com/corazawaf/libcoraza)
1.8.0, the C bindings of the [OWASP Coraza](https://github.com/corazawaf/coraza)
web application firewall engine, for Keel Linux on Debian trixie. Source
package `libcoraza`, binary packages `libcoraza1` and `libcoraza-dev`.

## Why Keel carries it

Keel Web runs Coraza inline in Nginx (handbook decision 0030), and
everything Keel installs is a `.deb` in the Keel repository (0039).
Step 5 of the first implementation of 0041 builds Coraza's packages:
this one, [libnginx-mod-http-coraza](https://github.com/Keel-Linux/libnginx-mod-http-coraza),
which loads `libcoraza.so.1` in each Nginx worker, and
[coreruleset](https://github.com/Keel-Linux/coreruleset).

## Debian status

No package of libcoraza or Coraza in Debian, and no ITP.

## Layout

[DEP-14](https://dep-team.pages.debian.net/deps/dep14/), as
git-buildpackage repositories on salsa: `upstream/latest` (upstream
tarballs, imported with `gbp import-orig`), `pristine-tar`, and
`keel/trixie` (this packaging, the default branch). Tags are
`upstream/<version>` and `keel/<debian-version>`.

The Go modules are a component tarball, `libcoraza_<version>.orig-vendor.tar.gz`,
unpacked as `vendor/` and kept in pristine-tar, so the build fetches
nothing. `debian/README.source` says how it is written and pins its
sha256.

## Building

On trixie, with trixie-backports enabled: the build needs
`golang-1.26-go` (trixie's Go is 1.24; libcoraza 1.8.0 requires 1.26).

```
sudo apt-get install git-buildpackage pristine-tar
gbp clone https://github.com/Keel-Linux/libcoraza.git
cd libcoraza
sudo apt-get build-dep ./
gbp buildpackage -us -uc
```

## Tests

At build time, upstream's C test (`tests/simple_get`) and `go test`. No
autopkgtest of its own: the library is tested in Nginx by the autopkgtest
of libnginx-mod-http-coraza, whose CI builds this repository's
`keel/trixie`. This repository's CI builds the package in a
`debian:trixie` container with trixie-backports and runs lintian, failing
on any error or warning.

## License

Apache-2.0, as upstream (`LICENSE`) and the packaging (`debian/copyright`).
