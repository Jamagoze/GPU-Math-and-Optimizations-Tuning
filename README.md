# GPU Math

**GPU Math** is a lightweight Zygisk module designed to optimize GPU shader precision and OpenCL floating-point operations on Android.

It uses native function hooks to modify selected OpenGL ES fragment shaders and OpenCL compiler options at runtime.

No GPU driver replacement or GPU frequency modification is required.

## Features

### FP16 Shader Math (OpenGL ES)

- Intercepts `glShaderSource()`.
- Changes eligible fragment shader default precision from `highp` to `mediump`.
- Preserves high precision for floating-point uniforms.
- Skips shaders containing `gl_Position` or compute-shader indicators.
- Leaves shaders with unsupported uniform structures untouched.

The goal is to reduce floating-point arithmetic costs where the GPU driver can benefit from medium precision.

**Note:** GLSL `mediump` does not guarantee native FP16 execution. Actual precision and performance depend on the GPU and its driver.

### OpenCL Fast Math

Intercepts `clBuildProgram()` and appends:

```text
-cl-fast-relaxed-math
-cl-mad-enable
```

These compiler options allow additional floating-point optimizations.

If the compiler rejects the added options with `CL_INVALID_BUILD_OPTIONS`, GPU Math retries using the original options.

### Zygisk Integration

- Native C++ implementation.
- Runtime function interception.
- GOT-based function hooking.
- Detects newly loaded native libraries.
- Reapplies hooks after `System.loadLibrary()`.
- Supports application exclusions.

## Configuration

Edit `config.txt` inside the module directory.

```ini
# 1 = on, 0 = off

fp16=1
opencl=1

# Apps to skip (package names)
exclude=
```

### Configuration Options

| Option | Default | Description |
|---|---:|---|
| `fp16` | 1 | Enable GLES fragment shader precision modification |
| `opencl` | 1 | Enable OpenCL fast-math compiler options |
| `exclude` | Empty | Exclude selected application packages |

### Examples

**FP16 only**

```ini
fp16=1
opencl=0
exclude=
```

**OpenCL only**

```ini
fp16=0
opencl=1
exclude=
```

**Exclude applications**

```ini
fp16=1
opencl=1
exclude=com.example.game com.example.app
```

Package names can be separated by spaces or commas.

## Compatibility

GPU Math requires:

- Android with Magisk and Zygisk support.
- An ARM-compatible Android environment.
- Applications using OpenGL ES or OpenCL.
- Compatible GPU drivers.

Performance improvements are hardware- and application-dependent.

## Performance Considerations

Potential benefits include:

- Reduced shader arithmetic precision requirements.
- Additional OpenCL compiler optimizations.
- Potentially improved GPU computation efficiency.

However, results are not guaranteed.

Possible limitations:

- Visual inaccuracies from reduced shader precision.
- Shader compilation incompatibilities.
- Changes in OpenCL numerical results.
- No improvement in CPU-bound or unrelated workloads.

## Safety

GPU Math does not intentionally:

- Overclock the GPU.
- Modify GPU voltage.
- Disable thermal protection.
- Replace GPU drivers.
- Force applications to use Vulkan.

The module includes a basic bootloop guard that disables the module if the previous boot does not reach the expected completion state.

## Building

The native component is written in C++17 and uses the Zygisk API.

Build configuration:

```cmake
set(CMAKE_CXX_STANDARD 17)
add_library(gpumath SHARED gpumath.cpp)
```

Required build dependencies include:

- Android NDK
- CMake
- Zygisk API headers

The resulting native library must be packaged into the appropriate Magisk module directory for the target architecture.

## Project Information

| Property | Value |
|---|---|
| Project | GPU Math |
| Version | 1.0 |
| Author | Jamagoze |
| Language | C++ |
| Integration | Zygisk |
| License | MIT |

## Disclaimer

GPU Math is an experimental optimization project.

Performance gains depend on GPU architecture, driver implementation, application behavior, and shader characteristics.

Use at your own discretion. Always test compatibility and stability before using it as a daily system module.
