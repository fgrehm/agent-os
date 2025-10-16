## Secrets management best practices

- **NEVER Commit Secrets**: Never push secrets (API keys, AWS credentials, tokens) to any repository in plain text
- **Password Manager Integration**: Use chezmoi's built-in password manager support
  - 1Password (recommended for comprehensive features)
  - Bitwarden, pass, KeePassXC, LastPass, etc.
- **1Password Integration**:
  - Use `onepasswordRead` template function to access secrets
  - Access secrets across different vaults and accounts
  - Example: `{{ onepasswordRead "op://vault/item/field" }}`
  - Secrets are fetched dynamically during `chezmoi apply`
- **Encryption Options**:
  - **Whole File Encryption**: Use age or GPG to encrypt entire files
  - **Value Encryption**: Use password managers to inject secrets via templates
- **Age Encryption Setup**:
  ```toml
  [encryption.age]
      identity = "/path/to/age/private/key"
      recipient = "age_public_key_string"
  ```
  - Add encrypted files: `chezmoi add --encrypted <file>`
- **AWS Secrets Manager**: Use `awsSecretsManager` template function for structured data
- **macOS Keychain**: Use `keyring` for basic values in macOS keychain
- **Secret File Patterns**: Use templates for files containing secrets
  - Example: `.aws/credentials.tmpl`, `.npmrc.tmpl`, `.env.tmpl`
- **Bootstrap with Secrets**: Plan how secrets are accessed during initial setup
  - Start with basic keyring/keychain
  - Expand to full password manager once installed
- **Template Secret Access**: Inject secrets at apply time, not commit time
- **Audit Secret Usage**: Regularly review which secrets are stored and where
- **Rotation Strategy**: Plan for secret rotation without breaking configurations
