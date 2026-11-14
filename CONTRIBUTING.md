# Contributing to GPU Math

Thank you for your interest in contributing to GPU Math!

Contributions are welcome, including bug fixes, compatibility improvements, performance optimizations, documentation updates, and feature suggestions.

## Getting Started

Before contributing:

1. Fork the repository.
2. Clone your fork.
3. Create a new branch for your changes.
4. Make and test your modifications.
5. Submit a Pull Request.

## Development Requirements

GPU Math uses native Android development technologies.

Recommended tools:

- Android NDK
- CMake
- C++17-compatible compiler
- Zygisk API
- Git

## Contribution Guidelines

### Code Quality

- Keep implementations lightweight.
- Prefer efficient native C++ code.
- Avoid unnecessary background processes.
- Avoid introducing additional dependencies without a clear reason.
- Maintain compatibility with existing Android environments.
- Handle errors safely.
- Avoid unnecessary modifications to existing GPU behavior.

### GPU Optimizations

When introducing GPU-related optimizations:

- Explain the expected performance benefits.
- Consider GPU architecture and driver compatibility.
- Preserve application stability.
- Avoid forcing unsupported precision modes.
- Test shader modifications against different applications.
- Verify OpenCL compiler compatibility.
- Avoid claiming performance improvements without benchmarks.

### Configuration

When adding configuration options:

- Use clear and descriptive names.
- Document default values.
- Provide safe defaults.
- Keep existing configuration options compatible.
- Handle invalid or missing values gracefully.

## Testing

Before submitting changes, verify:

- The module builds successfully.
- Zygisk injection works correctly.
- Existing configuration options remain functional.
- Applications launch normally.
- OpenGL ES applications remain compatible.
- OpenCL applications handle compiler options correctly.
- No unexpected crashes or boot issues occur.

Where possible, include benchmark results and device information.

## Pull Requests

When submitting a Pull Request:

1. Provide a clear title.
2. Explain what was changed.
3. Describe why the change is necessary.
4. Include relevant testing results.
5. Mention known limitations or compatibility issues.

Keep Pull Requests focused on a single feature or fix whenever possible.

## Bug Reports

When reporting bugs, include:

- GPU Math version.
- Android version.
- Device model.
- GPU model.
- Application affected.
- Steps to reproduce.
- Relevant logs or error messages.

Avoid submitting unrelated issues in the same report.

## Feature Requests

Feature suggestions are welcome.

Please explain:

- What the feature does.
- Why it would be useful.
- How it could improve GPU Math.
- Any potential compatibility concerns.

Features should prioritize measurable improvements, stability, and lightweight implementation.

## Code of Conduct

Be respectful and constructive.

- Respect other contributors.
- Provide useful feedback.
- Avoid personal attacks.
- Keep discussions relevant to the project.
- Welcome different technical approaches.

## License

By contributing to GPU Math, you agree that your contributions will be licensed under the repository's [MIT License](LICENSE).

## Thank You

Every contribution helps improve GPU Math.

Thank you for helping make the project better!
