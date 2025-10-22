## File permissions and ownership standards

- **Executable Scripts**: Use `executable_` prefix or `chmod 755` for scripts that should be executable
- **Private Files**: Use `private_` prefix or `chmod 600` for files containing sensitive data
- **Readonly Files**: Use `readonly_` prefix or `chmod 444` for files that shouldn't be modified
- **SSH Keys**: Always use `private_` prefix for SSH keys and set permissions to 600
- **Config Files**: Default to 644 for most configuration files (readable by user and others)
- **Directory Permissions**: Use 755 for most directories, 700 for private directories
- **Git Hooks**: Ensure git hooks are executable (755) after chezmoi apply
- **System Files**: Be cautious with files that require specific ownership (like /etc files)
- **Chezmoi Attributes**: Use chezmoi's built-in attributes (executable, private, readonly) over manual chmod
- **Verify Permissions**: Test that files have correct permissions after chezmoi apply
- **Document Special Cases**: Comment on why specific files need non-standard permissions
- **Security First**: When in doubt, use more restrictive permissions
