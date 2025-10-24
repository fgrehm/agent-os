## Dependency management best practices for open source

- **Commit Lockfiles**: Always commit package-lock.json, yarn.lock, or equivalent for reproducible builds
- **Minimize Dependencies**: Question each new dependency; prefer native APIs or small utilities over large libraries
- **Audit Regularly**: Run `npm audit` or equivalent regularly to check for known vulnerabilities
- **Automated Updates**: Use Dependabot or Renovate to automatically create PRs for dependency updates
- **Security-Only Updates**: Configure separate automated updates for security patches (daily) vs regular updates (weekly)
- **Review Update PRs**: Don't blindly merge automated updates; check changelogs for breaking changes
- **Lockfile in CI**: Use `npm ci` or equivalent in CI for faster, more reliable installs from lockfile
- **License Compatibility**: Check dependency licenses are compatible with your project's license
- **Dependency Age**: Prefer actively maintained dependencies; check last publish date and issue activity
- **Bundle Size**: Monitor bundle size impact of dependencies, especially for frontend projects
- **Transitive Dependencies**: Be aware of what your dependencies depend on (`npm ls` or `yarn why`)
- **Version Pinning**: Use `^` for minor updates, avoid `*` or `latest` which are unpredictable
- **Deprecation Handling**: Watch for deprecation warnings and plan migration before dependencies are removed
- **Supply Chain Security**: Verify package integrity, enable npm audit signatures, use lock files to prevent tampering
