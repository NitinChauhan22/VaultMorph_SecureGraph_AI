# Checksums

SHA256 hashes for released installers. Verify with `certutil -hashfile <file> SHA256`
(Windows) or `sha256sum <file>` (Linux/macOS) and compare against the value below before
running an installer you downloaded.

## v1.0.0 (2026-09-15)

| File | SHA256 |
|---|---|
| SecureGraph AI_1.0.0_x64-setup.exe | `85e0033706d0472ba03545fab31517eb39ef038b6bf3ac6c33b04c78afa3b16a` |
| SecureGraph AI_1.0.0_x64_en-US.msi | `bcf38ce4460b378983bb8ef54bd954bd01f8bc963ec937d1ed5735f167858df6` |

Both installers are signed with VaultMorph's code-signing certificate (self-signed; Windows
SmartScreen may still show an "Unknown Publisher" warning on first run since it isn't issued
by a public Certificate Authority — the signature still proves the file wasn't altered after
signing and that all VaultMorph installers come from the same source).
