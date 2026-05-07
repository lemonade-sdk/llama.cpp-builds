# 🔧 Manual Build Instructions — CUDA

> **⚠️ Warning:** These instructions are provided as a reference only.
> The [GitHub Actions workflow](../.github/workflows/build-llamacpp-cuda.yml) is the
> authoritative build process for release artifacts. Builds produced manually may differ
> from official releases due to environment differences, library versions, or flags.
> Use these instructions for local development and debugging only.

---

## 🐧 Ubuntu Build Instructions

### Part 1 — Install Required Software

```bash
# Build tools
sudo apt update
sudo apt install -y cmake ninja-build git wget patchelf xz-utils

# CUDA Toolkit (choose one):
# Option A: Latest from Ubuntu package manager
sudo apt install -y nvidia-cuda-toolkit

# Option B: Specific version via NVIDIA network repo (recommended for reproducibility)
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt install -y cuda-toolkit-12-8

# Verify installation
nvcc --version
nvidia-smi
```

### Part 2 — Clone llama.cpp

```bash
git clone --depth 1 https://github.com/ggml-org/llama.cpp.git
cd llama.cpp
```

### Part 3 — Build llama.cpp with CUDA

```bash
# Set CUDA environment (adjust path if your CUDA is not at /usr/local/cuda)
export CUDA_PATH=/usr/local/cuda
export PATH=${CUDA_PATH}/bin:${PATH}
export LD_LIBRARY_PATH=${CUDA_PATH}/lib64:${LD_LIBRARY_PATH}

# Configure — replace "86" with your target sm_ value (75, 80, 86, 89, 90, 100, 120)
cmake -B build -G Ninja \
    -DGGML_CUDA=ON \
    -DCMAKE_CUDA_ARCHITECTURES="86" \
    -DBUILD_SHARED_LIBS=ON \
    -DLLAMA_BUILD_TESTS=OFF \
    -DGGML_OPENMP=OFF \
    -DGGML_NATIVE=OFF \
    -DGGML_STATIC=OFF \
    -DGGML_RPC=ON \
    -DLLAMA_BUILD_BORINGSSL=ON \
    -DCMAKE_BUILD_TYPE=Release

# Build
cmake --build build --config Release -j$(nproc)
```

To build for multiple architectures in a single binary (larger binary, covers all targets):

```bash
-DCMAKE_CUDA_ARCHITECTURES="75;80;86;89;90;100;120"
```

### Part 4 — Copy Required CUDA Libraries

Bundle the CUDA runtime libraries alongside the binary for portable distribution:

```bash
build_bin_path="build/bin"

cp -av /usr/local/cuda/lib64/libcudart.so*   "${build_bin_path}/" 2>/dev/null || echo "libcudart not found"
cp -av /usr/local/cuda/lib64/libcublas.so*   "${build_bin_path}/" 2>/dev/null || echo "libcublas not found"
cp -av /usr/local/cuda/lib64/libcublasLt.so* "${build_bin_path}/" 2>/dev/null || echo "libcublasLt not found"
cp -av /usr/local/cuda/lib64/libcurand.so*   "${build_bin_path}/" 2>/dev/null || echo "libcurand not found"
```

> **Note on `libcuda.so`:** The CUDA driver library (`libcuda.so`) is part of the NVIDIA
> driver installation and **cannot be legally redistributed**. It is provided by the user's
> NVIDIA driver and must be present on the target system. Do not bundle it.

You can also use the helper script in this repo to discover and copy CUDA dependencies via `ldd`:

```bash
python utils/gather_required_libs_cuda.py \
    --binary build/bin/llama-server \
    --cuda-dir /usr/local/cuda \
    --dest-dir build/bin
```

### Part 5 — Set RPATH for Portable Distribution

Patch the RPATH so the binary finds its bundled libraries regardless of `LD_LIBRARY_PATH`:

```bash
build_bin_path="build/bin"

for file in "${build_bin_path}"/*.so* "${build_bin_path}"/llama-*; do
    if [ -f "${file}" ] && ! [ -L "${file}" ]; then
        patchelf --set-rpath '$ORIGIN' "${file}" 2>/dev/null || true
    fi
done
```

### Part 6 — Strip debug symbols (optional)

```bash
for file in build/bin/*; do
    if [ -f "${file}" ] && ! [ -L "${file}" ]; then
        if file "${file}" | grep -qE 'ELF.*(executable|shared object)'; then
            strip --strip-unneeded "${file}" 2>/dev/null || true
        fi
    fi
done
```

### Part 7 — Verify

```bash
cd build/bin
./llama-server --version
./llama-server --list-devices
./llama-server -m /path/to/model.gguf -ngl 99
```

You can also run the validation script provided in the repo:

```bash
./scripts/validate_cuda_setup.sh ./build/bin/llama-server
```

---

## 🪟 Windows Build Instructions

### Part 1 — Install Required Software

Use Chocolatey or install manually:

```pwsh
choco install ninja -y
choco install visualstudio2022buildtools -y --params "--add Microsoft.VisualStudio.Component.VC.Tools.x86.x64 --add Microsoft.VisualStudio.Component.VC.CMake.Project --add Microsoft.VisualStudio.Component.Windows11SDK.22621"
```

Install the [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) (12.x recommended).
After installation, verify:

```pwsh
nvcc --version
```

### Part 2 — Build llama.cpp with CUDA

Open the **x64 Native Tools Command Prompt for VS 2022** and run:

```cmd
git clone --depth 1 https://github.com/ggml-org/llama.cpp.git
cd llama.cpp

cmake -B build -G Ninja ^
    -DGGML_CUDA=ON ^
    -DCMAKE_CUDA_ARCHITECTURES="86" ^
    -DBUILD_SHARED_LIBS=ON ^
    -DLLAMA_BUILD_TESTS=OFF ^
    -DGGML_OPENMP=OFF ^
    -DGGML_NATIVE=OFF ^
    -DGGML_STATIC=OFF ^
    -DGGML_RPC=ON ^
    -DLLAMA_BUILD_BORINGSSL=ON ^
    -DCMAKE_BUILD_TYPE=Release

cmake --build build --config Release -j %NUMBER_OF_PROCESSORS%
```

### Part 3 — Bundle CUDA DLLs

Copy the runtime DLLs from `%CUDA_PATH%\bin` next to the built binaries:

```pwsh
$buildBin = "build\bin"
$cudaBin  = "$env:CUDA_PATH\bin"
foreach ($pat in @("cudart64_*.dll", "cublas64_*.dll", "cublasLt64_*.dll", "curand64_*.dll")) {
    Get-ChildItem -Path $cudaBin -Filter $pat | Copy-Item -Destination $buildBin
}
```

> `nvcuda.dll` is part of the NVIDIA driver and must NOT be bundled.

---

## 🎯 GPU Architecture Reference

| GPU Target | Architecture | CMake Value | Representative GPUs |
|---|---|---|---|
| `sm_75` | Turing | `"75"` | RTX 2060/2070/2080 Ti, T4, Quadro RTX, Titan RTX |
| `sm_80` | Ampere (data center) | `"80"` | A100, A30 |
| `sm_86` | Ampere (consumer) | `"86"` | RTX 3060/3070/3080/3090, A10, A40, A5000, A6000, A4000 |
| `sm_89` | Ada Lovelace | `"89"` | RTX 4060/4070/4080/4090, L4, L40S, RTX Ada workstation |
| `sm_90` | Hopper | `"90"` | H100, H200 |
| `sm_100` | Blackwell (data center) | `"100"` | B100, B200, GB200 |
| `sm_120` | Blackwell (consumer) | `"120"` | RTX 5060/5070/5080/5090 |

To target a specific GPU, pass its CMake value to `-DCMAKE_CUDA_ARCHITECTURES`. For example,
for an RTX 3090 (sm_86):

```bash
-DCMAKE_CUDA_ARCHITECTURES="86"
```

Forward compatibility: when you compile with `CMAKE_CUDA_ARCHITECTURES=86`, CMake generates
both native sm_86 code and PTX virtual code. The PTX allows the binary to JIT-compile for
newer architectures (sm_89, sm_90) at a small startup cost. For best performance, use the
binary that matches your GPU's native sm_ level.
