# Security Policy

## Supported Versions

The following versions are currently supported for security updates.

| Version | Supported |
|---|---|
| 1.0.x | Yes |
| < 1.0 | No |

## Reporting a Vulnerability

If you discover a security vulnerability in GPU Math, please report it responsibly.

**Do not publicly disclose vulnerabilities before they have been investigated.**

When reporting an issue, include:

- A description of the vulnerability.
- Steps to reproduce the issue.
- Affected GPU Math version.
- Android version and device architecture.
- Relevant logs or crash reports, if available.

Please use GitHub's **Private Vulnerability Reporting** feature if enabled for this repository.

If private reporting is unavailable, contact the repository maintainer through their GitHub profile.

## Security Considerations

GPU Math operates inside Android application processes through Zygisk.

Because of its runtime hooking implementation, users should consider the following:

- Only install GPU Math from trusted sources.
- Review source code before compiling or installing modified builds.
- Avoid downloading unofficial or modified releases.
- Test new versions before deploying them on daily-use devices.
- Keep Magisk and Android security updates current.

## Runtime Behavior

GPU Math modifies selected GPU-related function behavior at runtime.

Its functionality includes:

- OpenGL ES shader source interception.
- OpenCL compiler option modification.
- Native library function hooking.
- Application process injection through Zygisk.

These operations may introduce application compatibility issues or unexpected behavior with certain GPU drivers.

## Scope

Security reports are welcome for vulnerabilities involving:

- Unauthorized code execution.
- Memory corruption.
- Improper native function hooking.
- Unexpected privilege escalation.
- Module installation or update security.
- Unsafe handling of configuration files.
- Vulnerabilities affecting application process integrity.

Performance regressions, visual inaccuracies, and ordinary application crashes should generally be reported through GitHub Issues rather than security advisories, unless they expose an underlying security vulnerability.

## Disclosure Policy

The maintainer aims to:

1. Acknowledge valid vulnerability reports.
2. Investigate and assess reported issues.
3. Develop fixes where necessary.
4. Release security updates.
5. Publish relevant security advisories when appropriate.

Please allow reasonable time for investigation and remediation before public disclosure.

## Disclaimer

GPU Math is an experimental open-source project.

Users are responsible for evaluating the security and compatibility of the software before installation.
