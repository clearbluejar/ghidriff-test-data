# ghidriff-test-data

A repo for [ghidriff](https://github.com/clearbluejar/ghidriff) test data

Used by the ghidriff workflow: [pytest-devcontainer.yml](https://github.com/clearbluejar/ghidriff/blob/main/.github/workflows/pytest-devcontainer.yml)

## zlib CVE-2022-37434 (arm64e)

`bins/libz.1.2.12.arm64e.dylib` and `bins/libz.1.2.13.arm64e.dylib` are zlib built for arm64e from the upstream
tags with Apple clang `-O2`. v1.2.13 fixes CVE-2022-37434 in `inflate()` (a bounds check on the gzip header extra
field). `build/libz-cve-2022-37434/build.sh` rebuilds them reproducibly; `SHA256SUMS` and `TOOLCHAIN` record the
expected hashes, the source commits and the compiler.
