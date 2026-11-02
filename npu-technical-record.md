# Ryzen AI NPU on Ubuntu: what the retained tests establish

If an ONNX model compiles but execution says that XRT is not installed, check the runtime selected by the failing process before replacing drivers. In this investigation, XRT was installed: a saved trace opened the system library, while a later configuration using separately extracted SDK XRT libraries completed the C++ inference call. Compilation, runtime initialization, and correct model output were separate questions.

This record supports the accompanying Ryzen AI NPU articles. It explains the retained results and how far they can be used, rather than offering a clean-install recipe.

## Recorded environment

| Component | Recorded configuration |
| --- | --- |
| Hardware | GMKtec EVO-X3, AMD Ryzen AI MAX+ 395 |
| OS | Ubuntu 26.04.1 LTS |
| Kernel | Initially 7.0.0-38; later records show 7.0.0-42-generic |
| Kernel driver | amdxdna, with device nodes already present |
| System XRT | 2.21.75 |
| Separately extracted SDK runtime | XRT 2.25.37 and XDNA userspace plugin |
| SDK | Ryzen AI SDK 1.8.0 |
| Successful API path | C++ calling the ONNX Runtime 1.27.0 C API |

These values describe observations at different points in the investigation, not a newly recreated reference machine. The execution user already belonged to `render`; adding that group was not the fix documented here.

## Tests, observations, and their limits

| Check | Retained result | What it establishes |
| --- | --- | --- |
| Device and process-limit checks | A 64 MiB mapping failed with `-11` under an approximately 8 MiB memlock limit. A root/unlimited check and later ordinary-user checks with 1 GiB limits passed. | Process limits matter to this path. The first comparison changed both uid and limit; it did not isolate one cause or establish a minimum required limit. |
| IRON samples | User-reported SAXPY and vector_reduce_max runs passed after resolving virtual-environment visibility of `pyxrt`. | Those sample paths worked historically. This does not establish ONNX throughput or a repeatable fresh wheel installation. |
| SDK Python 3.14 | The recorded import path encountered an executable-stack problem and then `_PyThreadState_UncheckedGet` remained unresolved. | Changing a version guard did not establish native-extension compatibility. A successful SDK Python 3.12 run is not evidenced either. |
| Initial C++ ONNX run | EP registration and session creation passed, but execution reported compile-only mode and `Run` failed on an unconstructed output tensor. | A session-creation PASS was not inference completion. |
| Failed-run file trace | Opening the system `libxrt_coreutil.so.2` succeeded. | The file was found; successful initialization or ABI compatibility does not follow from an open operation. |
| Later C++ run with separately selected XRT | Saved loader diagnostics show local coreutil, core, and XDNA plugin initialization. The application reported `Inference: PASS` and `Quicktest: PASS`; the runtime reported one NPU subgraph executing. | The historical API call completed with reported NPU work. This is not a CPU-reference comparison, an all-NPU execution guarantee, or a performance result. |

The later run changed XRT version, plugin selection, `XILINX_XRT`, and library-path precedence together, while leaving system XRT installed. That bundle is supported as a working configuration for this case. No retained one-variable experiment identifies a particular version, symbol, or environment variable as the sole cause. The successful record also contains lookup diagnostics; it must not be described as warning-free.

## A useful diagnostic distinction

For the same symptom, separate three checks:

1. Can the actual execution process access the device and inherit suitable resource limits?
2. Which runtime libraries and plugins does that process select, including libraries loaded after startup?
3. Does the inference call finish, and have its outputs been compared with a justified reference?

The [Linux resource-limit manual](https://man7.org/linux/man-pages/man2/getrlimit.2.html) explains soft and hard process limits. The [dynamic-loader manual](https://man7.org/linux/man-pages/man8/ld.so.8.html) explains library search. These describe operating-system mechanisms, not proof of this experiment's outcome. A loader trace invokes the application; it is not a harmless offline test and can execute real NPU work.

No new SDK installation, inference, device test, or performance measurement was run to prepare this page. The working application and original environments were not rerun during this evidence review. Full original logs are retained privately; this page gives a curated account of their finite observations.

## Five inputs identified later

Full local SHA-256 values were collected for exactly five retained containers on **2026-10-30, 15:31:44–15:37:20 UTC**. These fix a later collected-input baseline. They are locally calculated digests, not vendor signatures or vendor-published checksums.

| Retained container | Bytes | Locally computed SHA-256 |
| --- | ---: | --- |
| `llvm_aie-22.0.0.2026102001+4f5df13d-py3-none-manylinux_2_27_x86_64.manylinux_2_28_x86_64.whl` | 149617848 | `63c988f06bd7359737f881443b5166ade847efd1e5d27d57d8481ee406e62d41` |
| `mlir_aie-1.4.5.dev113+ge28f6dc-cp314-cp314-manylinux_2_35_x86_64.whl` | 265463510 | `7af231daa7b0f6be17c570674f8f038f8a868a99ae4356cb2692982ee282bb0a` |
| `onnxruntime.tar.gz` | 914703902 | `51a091ce47b87a1a5a0c7df9df5ad30fc05a9797758fdda7703f891d9ce8199f` |
| `llvm_aie-1.8.0-py3-none-linux_x86_64.whl` | 1474908981 | `35700efe0706324959f7b6f159250094d20157baed14345fb8d5bb2be0475308` |
| `ryzen_ai-1.8.0.tgz` | 7819506855 | `7532d511532dd8842995bc8dc2edd8a45fbf261312ea9abe8dd01b4d502ac98d` |

To compare a lawfully obtained local file, run `sha256sum YOUR_ARCHIVE` and `wc -c < YOUR_ARCHIVE`, replacing `YOUR_ARCHIVE` with its actual filename. Compare the complete digest and byte count. Do not treat a matching filename alone as a match. These examples were not run against new downloads while preparing this record.

A matching digest establishes byte identity with this later baseline only. It does **not** prove that the historical successful run used those exact bytes, authenticate their vendor, prove membership inside the outer archive, or show that installation, linking, inference, or numerical validation succeeded. No archive, wheel, model, or SDK binary is distributed with this page.

## Still unverified

- Independent clean-host installation, staging, linking, loading, and successful execution of the complete stack.
- Numerical agreement between the ONNX output and a CPU/reference implementation, with justified tolerances.
- Historical acquisition and successful-run identity of the five later-hashed containers, and selected inner/outer archive relationships.
- Fresh native acceptance of the IRON wheel combination. The retained LLVM-AIE container has a `py3-none` filename but internal `cp310` tags; matching selected metadata is not proof of complete installed-payload equivalence.
- A single isolated explanation of the SDK runtime's compile-only decision.

The practical result is a historical configuration that completed the C++ inference path by selecting SDK-specific XRT per process. It is enough to motivate checking actual runtime selection; it is insufficient to claim a generally reproducible installation or validated model predictions.

## Editorial source

Face B's applied source policy is Editorial Policy v2.0, revision `5161fc7479589f83a6035b9d8094002af48aec47`. The linked [public edition](editorial-policy-v2.0.md) anonymizes one personal attribution and is a redacted derivative, not an exact copy of that private source. Its UTF-8 text has SHA-256 `040840514510b2579ab79c060c1b1cf5c5287a07edeb00eb7ea14e5113b7188c`. This identifies B's source policy and the distinct public edition; it does not assert that ChatGPT loaded either when writing Face A.

Codex prepared this supporting record from the author's retained investigation records. It is a curated summary, not a public transcript or a newly executed test report.

---

Copyright © 2026 Ｉ－ｐｌｕｓｐｌｕｓ株式会社. The document text, to the extent owned and licensable by the company, is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Third-party SDKs, software, models, trademarks, and any material the company is not entitled to license are excluded.
