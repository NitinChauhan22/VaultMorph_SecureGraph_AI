# SecureGraph AI — Privacy Policy

Effective date: 15 September 2026 · Last updated: 15 September 2026
Applies to: the SecureGraph AI desktop application and vaultmorph.org/securegraph

## 1. Overview

**Community Edition collects zero personal data.** Scanning, the security graph, findings, and
reports are generated and stored entirely on your device. No analytics, no telemetry, no account
required to use the product.

**If you choose to register** (free, optional, unlocks governance-dashboard extras only — it does
not gate scanning, reporting, or export), your email address and a one-way device fingerprint are
sent to `api.vaultmorph.org` for license issuance. No payment data is ever collected.

**If you enable an external AI provider** (optional, off by default — Local-only mode is the
default), the AI Context Firewall controls what, if anything, is sent to that provider, and every
such interaction is logged in the AI Audit Log.

## 2. Who we are

- **Data Controller:** VaultMorph (operating under vaultmorph.org)
- **Contact:** contact@vaultmorph.org
- **Data deletion requests:** contact@vaultmorph.org, subject line "Data Deletion Request"

## 3. Data collected, by activity

| Activity | Personal data collected | Sent to |
|---|---|---|
| Scanning, viewing findings/reports, using the graph | None | Nowhere — local only |
| Registering (Settings → License → Register) | Email address, device fingerprint (one-way hash) | `api.vaultmorph.org` |
| Enabling an external AI provider | Whatever the AI Context Firewall policy allows through (configurable; none by default) | The AI provider you configured (OpenAI / Anthropic / your own endpoint) |

Your source code, findings, scan history, and the security graph itself are **never** transmitted
to VaultMorph servers, in any configuration.

## 4. What registration collects, in detail

**Email address** — collected when you register. Used solely to issue and identify your
registration record. Not used for marketing beyond product/security update notices unless you
opt in separately.

**Device fingerprint** — a one-way hash of non-identifying hardware/OS identifiers. Cannot be
reversed to identify your hardware. Used only to bind a registration to a device.

**What we never collect via registration:** your source code, findings, scan results, API keys,
or any data processed by scanning/reporting features.

## 5. Legal basis for processing (GDPR Article 6)

| Data | Legal basis | Details |
|---|---|---|
| Email address | Consent (Art. 6(1)(a)) — the in-app consent screen | You explicitly agree before registration is possible |
| Device fingerprint | Legitimate interest (Art. 6(1)(f)) | Preventing registration abuse; hash is irreversible, impact is minimal |
| AI provider request content (when enabled) | Consent | You explicitly enable the provider and the Firewall policy allows the content |

## 6. Data storage & international transfers

Registration data (email, device fingerprint) is stored on the same shared production server
backing `api.vaultmorph.org` that hosts VaultMorph AI Shield's registration data — **India, OCI
Mumbai region**, operated by Oracle Cloud Infrastructure (per VaultMorph AI Shield's published
Privacy Policy at vaultmorph.org/privacy/, which this project's server infrastructure shares). If
you are in India, processing your email
for registration may constitute a cross-border transfer under the DPDP Act 2023; we process it
under consent obtained via the in-app screen. If you never register, no transfer occurs.

## 7. Data sharing

We do not sell, rent, or share your personal data with any third party. The only sub-processor is
the infrastructure provider hosting `api.vaultmorph.org`. We may disclose data if required by law,
and will notify you to the extent legally permitted before doing so.

## 8. Data retention

| Data | Retention period |
|---|---|
| Email address + registration record | Until you unregister/request deletion, or 12 months of inactivity |
| Device fingerprint | Same as above |
| Access logs (server-side) | 7 days, then automatically deleted |

To request earlier deletion: email contact@vaultmorph.org, subject "Data Deletion Request". We
confirm deletion within 30 days.

## 9. Your rights — GDPR (EU / EEA / UK)

Right of access, rectification, erasure, restriction, portability, and objection, plus the right
to lodge a complaint with your national supervisory authority. Exercise any right by emailing
contact@vaultmorph.org; we respond within 30 days (extendable to 90 for complex requests, with
notice).

## 10. Your rights — India DPDP Act 2023

Right to information, access, correction and erasure, grievance redressal (acknowledged within 48
hours), and the right to nominate another individual to exercise your rights. Contact
contact@vaultmorph.org. Unresolved grievances may be escalated to the Data Protection Board of
India once constituted.

## 11. Your rights — CCPA (California)

Right to know, delete, and opt out of sale/sharing of personal information. **We do not sell or
share personal information** as defined by CCPA. Exercise rights via contact@vaultmorph.org.

## 12. Security measures

- Data in transit: TLS enforced on all `api.vaultmorph.org` endpoints.
- Registration tokens: Ed25519-signed, tamper-evident, verifiable offline.
- Local data (findings, scan history, config): stored only on your device.

## 13. Children's privacy

SecureGraph AI is not directed at children under 13 (or under 18 where applicable). We do not
knowingly collect personal data from children.

## 14. Changes to this policy

Material changes bump the consent screen's version, requiring you to review and re-accept before
continuing. The "Last updated" date above reflects the latest revision.

## 15. Contact

Email: contact@vaultmorph.org — subject line "Privacy Request — [your request type]".
Response time: acknowledgment within 48 hours, resolution within 30 days.
