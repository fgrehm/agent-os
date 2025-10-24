## GitHub Actions best practices for open source

- **Test on Every PR**: Run test suite automatically on all pull requests to catch regressions
- **Matrix Testing**: Test across multiple OS (ubuntu, macos, windows) and language versions when relevant
- **Cache Dependencies**: Use caching for package managers (npm, pip, etc.) to speed up workflows
- **Pin Action Versions**: Use specific versions (`actions/checkout@v4`) not `@main` or `@latest` for stability
- **Secure Secrets Handling**: Use GitHub Secrets for sensitive data, never log secret values
- **Minimal Initial Setup**: Start with basic test workflow, add complexity (linting, multi-platform) as needed
- **Automated Releases**: Use semantic-release or release-please for version bumping and publishing
- **Dependency Updates**: Set up Dependabot for automatic action version updates
- **Status Badges**: Link CI status badge in README to show build health
- **Fast Feedback**: Keep workflows fast (< 5 min if possible) with caching and parallel jobs
- **Fail Fast Strategy**: Use `fail-fast: false` in matrix builds to see all failures, not just first
- **Concurrency Control**: Cancel outdated workflow runs when new commits pushed
- **Security Scanning**: Add CodeQL or similar for automated security analysis (especially for company-backed projects)
