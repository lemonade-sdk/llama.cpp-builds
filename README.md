# llama.cpp-builds

<a href="https://github.com/aigdat/llamacpp-rocm/releases/latest" title="Download the latest release">
  <img src="https://img.shields.io/github/v/release/aigdat/llamacpp-rocm?logo=github&logoColor=white" alt="GitHub release (latest by date)" />
</a>
<a href="https://github.com/aigdat/llamacpp-rocm/releases/latest" title="View latest release date">
  <img src="https://img.shields.io/github/release-date/aigdat/llamacpp-rocm?logo=github&logoColor=white" alt="Latest release date" />
</a>
<a href="LICENSE" title="View license">
  <img src="https://img.shields.io/github/license/aigdat/llamacpp-rocm?logo=opensourceinitiative&logoColor=white&cacheBust=1)" alt="License" />
</a>
<a href="https://github.com/ROCm/ROCm" title="Powered by ROCm 7.0">
  <img src="https://img.shields.io/badge/ROCm-7.0-blue?logo=amd&logoColor=white" alt="ROCm 7.0" />
</a>
<a href="https://developer.nvidia.com/cuda-toolkit" title="Powered by CUDA 12.x">
  <img src="https://img.shields.io/badge/CUDA-12.x-76b900?logo=nvidia&logoColor=white" alt="CUDA 12.x" />
</a>
<a href="https://github.com/ggerganov/llama.cpp" title="Powered by llama.cpp">
  <img src="https://img.shields.io/badge/🦙Powered%20by-llama.cpp-blue?logo=llama&logoColor=white" alt="Powered by llama.cpp" />
</a>
<a href="#-supported-devices" title="Platform support">
  <img src="https://img.shields.io/badge/OS-Windows%20%7C%20Ubuntu-0078D6?logo=windows&logoColor=white" alt="Platform: Windows | Ubuntu" />
</a>


We provide nightly builds of **llama.cpp** with both **AMD ROCm™ 7** *and* **NVIDIA CUDA 12.x** acceleration — delivering the freshest, cutting-edge builds available for both major GPU vendors. Our automated pipeline targets seamless integration with [**🍋 Lemonade**](https://github.com/lemonade-sdk/lemonade) and similar AI applications requiring high-performance GPU inference.

> [!IMPORTANT]
> **Contribution & Support Notice**: While this project currently focuses on integrating llama.cpp with both ROCm and CUDA in a specific production context, our broader goal is to contribute meaningfully to the llama.cpp + GPU ecosystem. We're not set up to provide comprehensive technical support, but we welcome collaborations, idea exchanges, or contributions that help advance this space.

## 🎯 Supported Devices

### AMD ROCm™ targets

| Target | Architecture | Representative GPUs |
|---|---|---|
| **gfx1151** | STX Halo APU | Ryzen AI MAX+ Pro 395 |
| **gfx1150** | STX Point APU | Ryzen AI 300 |
| **gfx120X** | RDNA4 | RX 9070 XT/GRE/9070, RX 9060 XT/9060 |
| **gfx110X** | RDNA3 | PRO W7900/W7800/W7700/W7600, RX 7900 XTX/XT/GRE, RX 7800 XT, RX 7700 XT/7700, RX 7600 XT/7600, Radeon 780M/760M/740M iGPUs |
| **gfx103X** | RDNA2 | RX 6800 XT/6800, RX 6700 XT/6700, RX 6600 XT/6600, RX 6500 XT/6500 |

**ROCm builds include ROCm™ 7 runtime libraries built-in** — no separate ROCm installation required.

### NVIDIA CUDA targets

| Target | Architecture | Representative GPUs |
|---|---|---|
| **sm_75** | Turing | RTX 2060/2070/2080, T4, Quadro RTX |
| **sm_80** | Ampere (data center) | A100, A30 |
| **sm_86** | Ampere (consumer) | RTX 3060/3070/3080/3090, A10, A40, A5000, A6000 |
| **sm_89** | Ada Lovelace | RTX 4060/4070/4080/4090, L4, L40S |
| **sm_90** | Hopper | H100, H200 |
| **sm_100** | Blackwell (data center) | B100, B200, GB200 |
| **sm_120** | Blackwell (consumer) | RTX 5060/5070/5080/5090 |

**CUDA builds bundle the runtime libraries** (`libcudart`, `libcublas`, `libcublasLt`, `libcurand`).
The CUDA driver library (`libcuda.so` / `nvcuda.dll`) is **not** redistributable and must
be supplied by the system's NVIDIA driver (Linux: 525+).

## 🚀 Automated Builds

Two independent GitHub Actions workflows produce nightly builds:

| Workflow | Backend | OS | Schedule |
|---|---|---|---|
| [`build-llamacpp-rocm.yml`](.github/workflows/build-llamacpp-rocm.yml) | AMD ROCm 7 | Windows + Ubuntu | 13:00 UTC daily |
| [`build-llamacpp-cuda.yml`](.github/workflows/build-llamacpp-cuda.yml) | NVIDIA CUDA 12.x | Windows + Ubuntu | 15:00 UTC daily |

Both workflows can also be triggered manually from the **Actions** tab with overrides for the
GPU targets, runtime version, and llama.cpp commit. Both share a single sequential `b####`
release tag space, so each successful run produces a unique tagged release with assets named:

```
# ROCm
llama-bXXXX-ubuntu-rocm-<gfx_target>-x64.zip
llama-bXXXX-windows-rocm-<gfx_target>-x64.zip

# CUDA
llama-bXXXX-ubuntu-cuda-<sm_target>-x64.tar.xz
llama-bXXXX-windows-cuda-<sm_target>-x64.zip
```

### ROCm download matrix

| GPU Target | Ubuntu | Windows |
|-------------|--------|---------|
| **gfx110X** | [![Download Ubuntu gfx110X](https://img.shields.io/badge/Download-Ubuntu%20gfx110X-blue)](https://github.com/aigdat/llamacpp-rocm/releases/latest) | [![Download Windows gfx110X](https://img.shields.io/badge/Download-Windows%20gfx110X-green)](https://github.com/aigdat/llamacpp-rocm/releases/latest) |
| **gfx1150** | [![Download Ubuntu gfx1150](https://img.shields.io/badge/Download-Ubuntu%20gfx1150-blue)](https://github.com/aigdat/llamacpp-rocm/releases/latest) | [![Download Windows gfx1150](https://img.shields.io/badge/Download-Windows%20gfx1150-green)](https://github.com/aigdat/llamacpp-rocm/releases/latest) |
| **gfx1151** | [![Download Ubuntu gfx1151](https://img.shields.io/badge/Download-Ubuntu%20gfx1151-blue)](https://github.com/aigdat/llamacpp-rocm/releases/latest) | [![Download Windows gfx1151](https://img.shields.io/badge/Download-Windows%20gfx1151-green)](https://github.com/aigdat/llamacpp-rocm/releases/latest) |
| **gfx120X** | [![Download Ubuntu gfx120X](https://img.shields.io/badge/Download-Ubuntu%20gfx120X-blue)](https://github.com/aigdat/llamacpp-rocm/releases/latest) | [![Download Windows gfx120X](https://img.shields.io/badge/Download-Windows%20gfx120X-green)](https://github.com/aigdat/llamacpp-rocm/releases/latest) |
| **gfx103X** | [![Download Ubuntu gfx103X](https://img.shields.io/badge/Download-Ubuntu%20gfx103X-blue)](https://github.com/aigdat/llamacpp-rocm/releases/latest) | [![Download Windows gfx103X](https://img.shields.io/badge/Download-Windows%20gfx103X-green)](https://github.com/aigdat/llamacpp-rocm/releases/latest) |

> **⚡ Ready to Run**: All ROCm releases include complete ROCm™ 7 runtime libraries — just download and go.

> **Linux (gfx1150/APU):** OOM despite free VRAM? Add `ttm.pages_limit=12582912` (48 GB) to the kernel cmdline (e.g. GRUB), run `update-grub`, then reboot. See [TheRock FAQ](https://github.com/ROCm/TheRock/blob/main/docs/faq.md#gfx1151-strix-halo-specific-questions) for more.

### CUDA download matrix

CUDA archives use `.tar.xz` (Linux) and `.7z` (Windows). Pick the archive matching your GPU's
`sm_` target — see the table above. All recent releases are listed in the
[Releases](../../releases) tab.

**Linux** (`.tar.xz`):
```
llama-bXXXX-ubuntu-cuda-sm_75-x64.tar.xz   # Turing
llama-bXXXX-ubuntu-cuda-sm_80-x64.tar.xz   # Ampere data center
llama-bXXXX-ubuntu-cuda-sm_86-x64.tar.xz   # Ampere consumer
llama-bXXXX-ubuntu-cuda-sm_89-x64.tar.xz   # Ada Lovelace
llama-bXXXX-ubuntu-cuda-sm_90-x64.tar.xz   # Hopper
llama-bXXXX-ubuntu-cuda-sm_100-x64.tar.xz  # Blackwell data center
llama-bXXXX-ubuntu-cuda-sm_120-x64.tar.xz  # Blackwell consumer
```

**Windows** (`.zip`, same format as ROCm Windows releases):
```
llama-bXXXX-windows-cuda-sm_86-x64.zip
```

Extract and run (Linux):

```bash
mkdir llama-cuda && tar -xJf llama-bXXXX-ubuntu-cuda-sm_86-x64.tar.xz -C llama-cuda
cd llama-cuda
./llama-server --version
./llama-server --list-devices
./llama-server -m /path/to/model.gguf -ngl 99
```

`LD_LIBRARY_PATH` is unnecessary — RPATH is set to `$ORIGIN` so the bundled `libcudart`,
`libcublas`, `libcublasLt`, and `libcurand` are found automatically.

---

## 🧪 Quick Smoketest

To verify your download is working correctly:

1. **Download** the appropriate archive for your GPU from the [latest release](../../releases/latest)
2. **Extract** the archive to a directory
3. **Test** with any GGUF model from Hugging Face:

```bash
llama-server -m YOUR_GGUF_MODEL_PATH -ngl 99
```

> **💡 Tip**: Use `-ngl 99` to offload all layers to GPU for maximum acceleration.

> **🍋 Lemonade Integration**: You can also test these builds directly with [**Lemonade**](https://github.com/lemonade-sdk/lemonade) for a seamless AI application experience.

---

## ✅ Testing

### Automated CI smoke tests (no GPU required)

Each CUDA build is automatically smoke-tested on the GitHub-hosted runner using the
[`test-llamacpp-cuda-build`](.github/actions/test-llamacpp-cuda-build/action.yml) composite
action. The test verifies that:

- `llama-server` is present and runs `--version` successfully
- Required CUDA libraries (`libcublas`, `libcublasLt`, `libcurand`) are bundled
- RPATH contains `$ORIGIN` for portability
- `--list-devices` runs (the GPU enumeration is skipped on CPU-only runners)

ROCm builds run a tokenization smoke test on self-hosted Strix Halo / Strix Point runners via
the [`test-llamacpp-build`](.github/actions/test-llamacpp-build/action.yml) action, exercising
real GPU offload with `llama-cli -ngl 99`.

### Standalone test workflows

Two manual workflows can re-validate the assets of any past release:

- [`test-llamacpp-rocm.yml`](.github/workflows/test-llamacpp-rocm.yml) — downloads a ROCm
  release and runs `llama-cli` against a small GGUF model on self-hosted GPU runners
- [`test-llamacpp-cuda.yml`](.github/workflows/test-llamacpp-cuda.yml) — downloads a CUDA
  release and runs CPU-only smoke checks on a GitHub-hosted runner

### Local pytest smoke suite (CUDA)

```bash
pip install pytest

# CPU-only smoke checks against an extracted CUDA build:
LLAMACPP_ARTIFACT_DIR=./llama.cpp/build/bin pytest tests/

# Including GPU tests (requires NVIDIA GPU + driver):
LLAMACPP_ARTIFACT_DIR=./llama.cpp/build/bin pytest tests/ -m gpu
```

The [`tests/test_build_smoke_cuda.py`](tests/test_build_smoke_cuda.py) suite checks: binary
exists, required CUDA libs present, RPATH contains `$ORIGIN`, and (with `-m gpu`) verifies
`--version` exits 0 and `--list-devices` reports CUDA/NVIDIA.

### Local CUDA validation script

A standalone bash script for quick environment validation on a target machine:

```bash
./scripts/validate_cuda_setup.sh ./llama.cpp/build/bin/llama-server
```

This checks Ubuntu version, `nvidia-smi`, `nvcc`, the binary, `--version`, and
`--list-devices`, then prints a PASS/WARN/FAIL summary.

---

## 📦 Dependencies

### Core
- **[llama.cpp](https://github.com/ggerganov/llama.cpp)** — efficient cross-platform inference engine for GGUF models.

### ROCm builds
- **[ROCm SDK (TheRock)](https://github.com/ROCm/TheRock)** — AMD's open-source GPU compute platform.
- **[HIP](https://github.com/ROCm/HIP)** — portable GPU C++ API used by ROCm.

### CUDA builds
- **[CUDA Toolkit 12.x](https://developer.nvidia.com/cuda-toolkit)** — NVIDIA's GPU compute toolkit.
- **NVIDIA Driver 525+** — required at runtime; supplies `libcuda.so` / `nvcuda.dll`.

### Build tools (both)
- **[Visual Studio 2022 Build Tools](https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2022)** (Windows)
- **[CMake](https://cmake.org/)** ≥ 3.20 (CUDA) / 3.31.0 (ROCm Windows)
- **[Ninja](https://ninja-build.org/)**
- **[patchelf](https://github.com/NixOS/patchelf)** (Linux, for setting RPATH)

---

## 🏗️ Code and Artifact Structure

> [!NOTE]
> **Active Development**: This project is under active development. Code and artifact structure are subject to change as we continue to improve and expand functionality.

### Layout

```
.github/
  actions/
    test-llamacpp-build/        # ROCm smoke test (gfx_target, runs llama-cli on GPU runner)
    test-llamacpp-cuda-build/   # CUDA smoke test (sm_target, CPU-only)
  workflows/
    build-llamacpp-rocm.yml     # ROCm nightly build (Windows + Ubuntu)
    build-llamacpp-cuda.yml     # CUDA nightly build (Windows + Ubuntu)
    test-llamacpp-rocm.yml      # On-demand ROCm release validation (self-hosted GPU)
    test-llamacpp-cuda.yml      # On-demand CUDA release validation (CPU-only)
docs/
  manual_instructions.md        # Manual ROCm build steps
  manual_instructions_cuda.md   # Manual CUDA build steps
scripts/
  validate_cuda_setup.sh        # Local CUDA environment validator
tests/
  pytest.ini
  test_build_smoke_cuda.py      # CUDA artifact smoke tests
utils/
  gather_required_libs_rocm.py  # Discovers missing ROCm shared libs via runtime errors
  gather_required_libs_cuda.py  # Discovers required CUDA libs via ldd
```

The build process is primarily handled through GitHub Actions, with the repository serving as the source for automated compilation and packaging of llama.cpp with ROCm 7 and CUDA 12.x support.

---

## 📋 Manual Build Instructions

- **ROCm**: see [`docs/manual_instructions.md`](docs/manual_instructions.md)
- **CUDA**: see [`docs/manual_instructions_cuda.md`](docs/manual_instructions_cuda.md)

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
