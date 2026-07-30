# b6q / SM-F741B / F741BXXS4DZG3

Offline profile draft for Galaxy Z Flip6 on exact firmware **F741BXXS4DZG3**.

## Device identity (from handset)

| Field | Value |
|-------|--------|
| Model | SM-F741B |
| Device | b6q |
| Platform | pineapple / SM8650 |
| Android / SDK | 16 / 36 |
| One UI | 8.0 (`80500`) |
| Display build | `BP4A.251205.006.F741BXXS4DZG3` |
| Fingerprint | `samsung/b6qxxx/b6q:16/BP4A.251205.006/F741BXXS4DZG3:user/release-keys` |
| ABI / page | arm64-v8a / 4096 |
| `uname -r` | `6.1.145-android14-11-33418572-abF741BXXS4DZG3` |
| `uname -v` | `#1 SMP PREEMPT Tue Jul  7 02:11:15 UTC 2026` |
| Trace event id | **106** (`sched_blocked_reason`) |

Full `/proc/version`:

```text
Linux version 6.1.145-android14-11-33418572-abF741BXXS4DZG3 (build-user@build-host) (Android (10087095, +pgo, +bolt, +lto, -mlgo, based on r487747c) clang version 17.0.2 (https://android.googlesource.com/toolchain/llvm-project d9f89f4d16663d5012e5c09495f3b30ece3d2362), LLD 17.0.2) #1 SMP PREEMPT Tue Jul  7 02:11:15 UTC 2026
```

## Source artifacts

| Object | Notes |
|--------|--------|
| `boot.img` | ANDROID! v4; kernel Image 38005248 bytes |
| Kernel SHA-256 | `7FE851F3356E8D63D9AF356392831A22AA2A9443BA192A81F2835AD74DC2A075` |
| boot.img SHA-256 | `75AEC1E1F99DB14EAA2101FB3926E25DF788A8E8CADF322195427B5CD01BA1D8` |
| vendor_boot | VNDRBOOT v4, board `SRPXA09A004` |
| BL | `BL_F741BXXS4DZG3_...` (abl/xbl/uefi) |
| BTF | Embedded; extracted |
| vmlinux base | `0xffffffc008000000` (vmlinux-to-elf) |

## Why e3q does not work on this phone

Same GKI family (`6.1.145-android14-11`) and many identical symbols, but:

1. **Different linked Image** — ABI id `33418572` vs S24U `33419968`
2. **`nfnetlink_log` string** — this build `0x016a61f4`, e3q `0x016a61b8` (breaks KASLR leak math)
3. **P0 fingerprint table** — must come from this Image
4. Feed requires exact `uname -r` / `/proc/version`

## Status

| Step | Status |
|------|--------|
| On-device identity | Done |
| boot / vendor_boot / BL | Done |
| Symbol recovery | Done |
| BTF layouts | Done |
| P0 fingerprints | Done (32 rows, verified) |
| `target.h` | Draft written |
| KernelSU late-load KO / ksud | **Not built yet** |
| Payload compile / device test | **Not done** |

## Feed entry (draft)

```json
{
  "profileId": "b6q-F741BXXS4DZG3",
  "manufacturer": "samsung",
  "model": "SM-F741B",
  "device": "b6q",
  "kernelRelease": "6.1.145-android14-11-33418572-abF741BXXS4DZG3",
  "kernelVersion": "Linux version 6.1.145-android14-11-33418572-abF741BXXS4DZG3 (build-user@build-host) (Android (10087095, +pgo, +bolt, +lto, -mlgo, based on r487747c) clang version 17.0.2 (https://android.googlesource.com/toolchain/llvm-project d9f89f4d16663d5012e5c09495f3b30ece3d2362), LLD 17.0.2) #1 SMP PREEMPT Tue Jul  7 02:11:15 UTC 2026",
  "kernelBuildVersion": "#1 SMP PREEMPT Tue Jul  7 02:11:15 UTC 2026",
  "buildDisplay": "BP4A.251205.006.F741BXXS4DZG3",
  "buildFingerprint": "samsung/b6qxxx/b6q:16/BP4A.251205.006/F741BXXS4DZG3:user/release-keys",
  "sdk": 36,
  "abi": "arm64-v8a",
  "pageSize": 4096
}
```

## Next steps

1. Copy `target.h` + `p0_fingerprint.h` into a Root-My-Galaxy-Payloads tree as `src/targets/b6q-F741BXXS4DZG3/`.
2. Build release payload (`make TARGET=b6q-F741BXXS4DZG3 release`).
3. Build Samsung KDP KernelSU for vermagic  
   `6.1.145-android14-11-33418572-abF741BXXS4DZG3 SMP preempt mod_unload modversions aarch64`.
4. Hardware test (expect reboots until phys/`SKB_DATA_DELTA`/timing are validated).

## KernelSU note

Feed currently reuses `ksud-e3q-S928USQS6DZF2-kdp` (android14-6.1). That binary embeds
a KO with S928U vermagic and will **not** late-load cleanly on F741BXXS4DZG3
(`CONFIG_MODULE_FORCE_LOAD` is off). A Flip6-specific `ksud`/KO with vermagic
`6.1.145-android14-11-33418572-abF741BXXS4DZG3` is still required for full install.

Exploit artifact: `artifacts/b6q-F741BXXS4DZG3/cve-2026-43499-app.so` (104128 bytes).
