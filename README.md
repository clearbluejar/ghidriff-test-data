# ghidriff-test-data

A repo for [ghidriff](https://github.com/clearbluejar/ghidriff) test data

Used by the ghidriff workflow: [pytest-devcontainer.yml](https://github.com/clearbluejar/ghidriff/blob/main/.github/workflows/pytest-devcontainer.yml)

## zlib CVE-2022-37434 (arm64e)

`bins/libz.1.2.12.arm64e.dylib` and `bins/libz.1.2.13.arm64e.dylib` are zlib built for arm64e from the upstream
tags with Apple clang `-O2`. v1.2.13 fixes CVE-2022-37434 in `inflate()` (a bounds check on the gzip header extra
field). `build/libz-cve-2022-37434/build.sh` rebuilds them reproducibly; `SHA256SUMS` and `TOOLCHAIN` record the
expected hashes, the source commits and the compiler.

## iOS kexts with same-size fixes (arm64e)

Two iPhone13,3 kexts from iOS builds 24A446 and 24B119, extracted from each build's kernelcache. In both, the fix
leaves the function's size and reference count unchanged, so ghidriff's default triage does not diff the pair and
`--pcode-gate` is needed to surface it (`tests/test_gold_cases.py` in ghidriff).

| file | SHA-256 |
| --- | --- |
| `bins/com.apple.iokit.IOStorageFamily.iPhone13_3.24A446` | `4aa056753156eb344fa9700e3dca7e2c9b95e45fed76c0e22829126a166ff9b7` |
| `bins/com.apple.iokit.IOStorageFamily.iPhone13_3.24B119` | `a1e38f6d91cc36caf537d1653c0552c1a2fbf5eb163684d1b56cc53f5ba6341c` |
| `bins/com.apple.iokit.IOSurface.iPhone13_3.24A446` | `cc90ce7aae28dc853476554e3bd3f9e0d747a2265efdc224f75778d5dc3ca7bf` |
| `bins/com.apple.iokit.IOSurface.iPhone13_3.24B119` | `6b6d3d1f2795ace5620c80806ef4cd0d11101165e640d429526beafd5852d346` |

- CVE-2026-112554, IOStorageFamily: `FUN_fffffff009b770e0` (24A446) ↔ `FUN_fffffff009bb2d60` (24B119). A GPT
  partition-name scan bound changes from 72 to 36 UTF-16 units.
- CVE-2026-112477, IOSurface: `FUN_fffffff009bae76c` (24A446). A call loses an argument.

## iOS CoreMedia with a fix inside a switch case (arm64e)

The iPhone13,3 CoreMedia dylib from iOS builds 24A446 and 24B119, extracted from each build's dyld shared cache.

| file | SHA-256 |
| --- | --- |
| `bins/CoreMedia.iPhone13_3.24A446` | `f959c7178a64bdddd03695e2e786ca812ccd2827dc8dc2378e15e072ef3076b8` |
| `bins/CoreMedia.iPhone13_3.24B119` | `4da11f3fd8155e7d733151db2a9f98297dac7932a9cacab7e52645b0ec6e1c22` |

- CVE-2026-112688: `_hevcbridgeParseVdrDmDataPayloadExMetadatatBlocks` (24A446) ↔
  `_hevcbridgeParseVdrDmDataPayloadExMetadataBlocks` (24B119), 5,052 → 5,080 bytes; the new build adds three count
  checks. The checks sit in the cases of a relative jump table that Ghidra 12.0 and 12.2 don't recover, so without
  ghidriff's switch repair both bodies end at the switch (944 bytes) and match as identical.
