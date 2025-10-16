## Script safety and security standards

- **Avoid `rm -rf` Without Checks**: Always validate paths before recursive deletion; use specific paths, not variables
- **Subshell for Directory Changes**: Use `(cd /path && command)` instead of `cd /path` to avoid side effects
- **Input Validation**: Sanitize and validate all user inputs and external data
- **Secure Temp Files**: Use `mktemp` with proper permissions (600 for files, 700 for directories)
- **No Secrets in Code**: Never hardcode passwords, API keys, or secrets; use environment variables or password managers
- **Safe Downloads**: Verify checksums or signatures when downloading files
- **Command Injection Prevention**: Quote all variables used in commands to prevent injection attacks
- **Privilege Escalation**: Only request sudo when absolutely necessary; explain why in comments
- **File Permissions**: Set appropriate permissions on created files (644 for configs, 755 for executables)
- **Idempotency**: Scripts should be safe to run multiple times without causing problems
- **Backup Before Modification**: When modifying system files, create backups first
- **Clear Destructive Actions**: Warn users before destructive actions; consider confirmation prompts for critical operations
