## Cross-platform compatibility standards

- **OS Detection**: Use standard methods to detect operating system (check `/etc/os-release`, `uname`, etc.)
- **Package Managers**: Support multiple package managers (apt, dnf, pacman) with graceful fallbacks
- **Path Handling**: Use portable path constructions; avoid hardcoded paths when possible
- **User Home Directory**: Use `$HOME` instead of `~` in scripts for better portability
- **Temp Directories**: Use `mktemp` for temporary files/directories, never hardcode `/tmp`
- **Command Alternatives**: Provide fallbacks for commands that may not exist on all systems
- **Platform-Specific Logic**: Clearly separate platform-specific code with comments explaining why
- **Test on Multiple Platforms**: Validate scripts work on Ubuntu, Fedora, and other target distributions
- **Container Awareness**: Detect and handle container environments differently (check `/.dockerenv`, `/run/.containerenv`)
- **Architecture Awareness**: Handle different CPU architectures when necessary (x86_64, arm64)
- **User Permissions**: Don't assume root access; check and request when needed
- **Standard Utilities**: Only rely on utilities guaranteed to be present (coreutils, common shell tools)
