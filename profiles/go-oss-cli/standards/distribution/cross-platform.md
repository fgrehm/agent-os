## Cross-platform build and distribution

- **GoReleaser**: Use GoReleaser for automated multi-platform builds and releases
- **Target platforms**: Build for common platforms: linux/amd64, linux/arm64, darwin/amd64, darwin/arm64, windows/amd64
- **Static binaries**: Build fully static binaries when possible (CGO_ENABLED=0) for maximum portability
- **GitHub Releases**: Publish releases to GitHub Releases with release notes and checksums
- **Version embedding**: Embed version info at build time using `-ldflags "-X main.version=..."`
- **Binary naming**: Name binaries consistently: `toolname-os-arch` or `toolname.exe` for Windows
- **Checksums**: Provide SHA256 checksums for all release artifacts
- **Signatures**: Consider signing releases with GPG or cosign for security-conscious users
- **Archive formats**: Use `.tar.gz` for Unix systems, `.zip` for Windows
