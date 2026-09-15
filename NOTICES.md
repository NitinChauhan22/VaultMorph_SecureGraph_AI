# Third-Party Notices

SecureGraph AI includes or is built using third-party software and data. This file lists
attribution required by those components' licenses.

## Software wrapped as external tools (invoked as subprocesses/daemons, never linked into the binary)

- **Semgrep** -- LGPL 2.1. https://github.com/semgrep/semgrep
- **OSV-Scanner** -- Apache License 2.0. https://github.com/google/osv-scanner
- **Gitleaks** -- MIT License. https://github.com/gitleaks/gitleaks
- **Checkov** -- Apache License 2.0. https://github.com/bridgecrewio/checkov
- **Trivy** -- Apache License 2.0. https://github.com/aquasecurity/trivy
- **OWASP ZAP** -- Apache License 2.0. https://github.com/zaproxy/zaproxy
- **OpenGrep** -- LGPL 2.1. https://github.com/opengrep/opengrep

None of the above are bundled into or statically linked with the SecureGraph AI binary; each
is a separately-installed external tool that SecureGraph AI invokes as a subprocess or local
daemon, per each engine's own license terms.

## Third-party data extracted and transformed

- **github/codeql** (models data only) -- MIT License. Copyright (c) 2006-2025 GitHub, Inc.
  https://github.com/github/codeql

  Structured "Models-as-Data" YAML rows (source/sink/summary/neutral API models) were
  extracted from `javascript/ql/lib/ext/*.yml` and `python/ql/lib/ext/*.yml`, transformed into
  SecureGraph AI's own schema, and used by its taint-analysis engine. SecureGraph AI does not
  use, bundle, or depend on the CodeQL CLI/evaluator (`github/codeql-cli-binaries`), which is
  separately licensed and not open source.

  ```
  MIT License

  Copyright (c) 2006-2025 GitHub, Inc.

  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files (the "Software"), to deal
  in the Software without restriction, including without limitation the rights
  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
  copies of the Software, and to permit persons to whom the Software is
  furnished to do so, subject to the following conditions:

  The above copyright notice and this permission notice shall be included in all
  copies or substantial portions of the Software.

  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
  SOFTWARE.
  ```

## Note

This is an attribution list, not legal advice. Have qualified IP/licensing counsel review
third-party obligations before any commercial launch that bundles or redistributes these
tools.
