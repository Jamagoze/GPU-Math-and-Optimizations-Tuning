# GPU Math

A Zygisk module that lowers GPU load in OpenGL ES apps, system-wide (games, browser WebGL, launcher, SystemUI). It hooks the GLES texture and shader calls and changes their parameters before they reach the driver. Vulkan is not touched.

Tuned for a Snapdragon 680 / Adreno 610 tablet (Redmi Pad SE, HyperOS), but it is not device-specific.

## Features

| Key | Default | What it does |
|---|---|---|
| `af_off` | `1` | Forces anisotropic filtering off (max anisotropy = 1). Saves texture bandwidth. |
| `fp16` | `1` | Rewrites fragment shader default precision `highp` to `mediump` (half-precision math). Uniforms are pinned to `highp` so they still match the vertex shader. Vertex shaders and shaders with uniform blocks or structs are left alone. |
| `mip_bilinear` | `1` | Trilinear mip filtering becomes bilinear. Halves mip samples; faint seams where mip levels switch. |
| `tex_min_lod` | `0` | `1` to `4` raises the minimum mip level (`1` = half-res textures, `2` = quarter). Biggest bandwidth saving, softer textures. Only textures with mipmaps, ES 3.x contexts only. `0` = off. |
| `opencl` | `1` | Adds `-cl-fast-relaxed-math -cl-mad-enable` to OpenCL `clBuildProgram` (retries without them if the driver rejects the flags). |
| `tex_nearest` | `0` | Linear filtering becomes nearest. Pixelated and affects UI and text. Usually leave off. |
| `exclude` | empty | Packages to skip entirely. |

Texture options apply to both textures and sampler objects.

## Requirements

- Magisk v24+ with Zygisk enabled
- Android 8.0+ (arm64-v8a and armeabi-v7a apps are both supported)

## Build and install

1. Put `gpu_math_src.zip` and the workflow `gpu_math.yml` in your GitHub repo (`.github/workflows/`).
2. Run the workflow. It downloads `zygisk.hpp`, builds both ABIs with the NDK and uploads `gpu_math.zip` as an artifact.
3. Flash `gpu_math.zip` in Magisk and reboot.

## Config

File: `/data/adb/modules/gpu_math/config.txt`

```
af_off=1
fp16=1
mip_bilinear=1
tex_min_lod=0
opencl=1
tex_nearest=0
exclude=com.playdead.limbo.full com.android.chrome
```

- `1` = on, `0` = off. For `tex_min_lod`, `0` is off and `1` to `4` is the level.
- `exclude` takes package names separated by spaces or commas. The match is exact on the part of the process name before `:`, so sub-processes are excluded too. An excluded app gets none of the options.
- Edits apply the next time an app starts: force-stop the app and reopen it. No reboot needed.
- Flashing a new module version overwrites `config.txt`, so keep a copy of your `exclude=` line.

## How it works

No polling and no background threads.

1. On app start (after specialization) the module patches the export tables of `libGLESv2.so` and `libEGL.so`, so every library that loads later, or is still lazily bound, resolves the GL functions to the module's hooks. The original addresses are saved first.
2. Libraries that were already bound get a one-time GOT patch.
3. With `opencl=1`, the patch is re-run right after every `System.loadLibrary`, because `libOpenCL` normally loads later.
4. `eglGetProcAddress` is hooked as well, so engines that fetch GL entry points through EGL, or look them up with `dlsym`, get the hooks too.

Hooked calls: `glTexParameter{f,fv,i,iv}`, `glSamplerParameter{f,fv,i,iv}`, `glShaderSource`, `eglGetProcAddress`, `clBuildProgram`.

Works with the ANGLE system module (`angle_sys`), because the hooks sit above ANGLE.

## Safety

- Skipped processes: `system_server`, zygotes, isolated processes and anything named `bootanim`. The boot animation is a native binary that Zygisk never injects.
- Bootloop guard: if a boot does not finish, the module disables itself on the next boot (it creates `disable` in its module folder). Re-enable it from Magisk after fixing the cause.

## Limits

- **Vulkan is not covered.** Apps and engines that render through Vulkan are unaffected. In Chrome, page drawing runs on Vulkan (Skia Graphite on Dawn), but WebGL goes through GLES and is hooked.
- Processes that do not have `libGLESv2.so` loaded at startup are skipped. The zygote preloads it, so this is rare.
- Libraries using packed relocations (APS2/RELR) that were bound before the patch are not patched, but later binds still go through the export-table patch.
- OpenCL fast-math only reaches apps whose libraries load `libOpenCL` through `System.loadLibrary` (the module re-patches after each one, no thread). Apps that `dlopen` it from native code are missed.
- `fp16` can cause banding or wrong lighting in some games and WebGL sites. Add those packages to `exclude=`, or turn `fp16` off.
- `tex_min_lod` and `tex_nearest` make textures visibly softer or blockier. Test on one game first.

## Troubleshooting

- Check that the module is loaded and not auto-disabled:
  ```
  su -c 'ls /data/adb/modules/gpu_math; ls /data/adb/modules/gpu_math/disable /data/adb/gpu_math_boot'
  ```
  After a normal boot the second path should say "No such file".
- Check for crashes after starting an app:
  ```
  su -c 'logcat -b crash -d | tail -50'
  ```
- If one app misbehaves, add its package to `exclude=` and relaunch it.
- To compare, set `af_off=0`, `fp16=0`, `mip_bilinear=0`, `opencl=0` and relaunch the app.
