## Licensing best practices for open source

- **Choose Early**: Add LICENSE file on day one; no license means others can't legally use your code
- **Popular Licenses**: Use well-known licenses (MIT, Apache-2.0, GPL-3.0) unless you have specific needs
- **MIT for Permissive**: MIT is simple, permissive, most popular choice for maximum adoption
- **Apache-2.0 for Patents**: Like MIT but includes explicit patent grant (better for corporate contributors)
- **GPL for Copyleft**: GPL-3.0 ensures derivatives stay open source; AGPL-3.0 covers network services
- **State Clearly**: Add license to LICENSE file and mention in README badge/footer
- **Package Metadata**: Include license in package.json, setup.py, Cargo.toml, etc.
- **Copyright Notice**: Update copyright year and holder in LICENSE file
- **Dependency Compatibility**: Ensure dependency licenses are compatible with your project license
- **License Headers**: Optional SPDX headers in source files for clarity (especially important for GPL)
- **Dual Licensing**: Consider dual licensing (AGPL + commercial) for business model
- **CLA/DCO**: Contributor License Agreement or Developer Certificate of Origin for complex IP situations
- **Third-Party Notices**: Document third-party licenses in THIRD_PARTY_LICENSES.md or similar
- **License Changes**: Changing license requires permission from all contributors or code rewrite
