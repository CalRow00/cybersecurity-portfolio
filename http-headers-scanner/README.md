```ruby
██╗  ██╗███████╗ █████╗ ██████╗ ███████╗██████╗ ███████╗
██║  ██║██╔════╝██╔══██╗██╔══██╗██╔════╝██╔══██╗██╔════╝
███████║█████╗  ███████║██║  ██║█████╗  ██████╔╝███████╗
██╔══██║██╔══╝  ██╔══██║██║  ██║██╔══╝  ██╔══██╗╚════██║
██║  ██║███████╗██║  ██║██████╔╝███████╗██║  ██║███████║
╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝╚═════╝ ╚══════╝╚═╝  ╚═╝╚══════╝
```

[![Cybersecurity Projects](https://img.shields.io/badge/Cybersecurity--Projects-Foundations-red?style=flat&logo=github)](https://github.com/CarterPerez-dev/Cybersecurity-Projects/tree/main/PROJECTS/foundations/http-headers-scanner)
[![Tier: Foundations](https://img.shields.io/badge/Tier-Foundations-00C9A7?style=flat&logo=bookstack&logoColor=white)](https://github.com/CarterPerez-dev/Cybersecurity-Projects/tree/main/PROJECTS/foundations)
[![Python 3.13](https://img.shields.io/badge/Python-3.13-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org)
[![License: AGPLv3](https://img.shields.io/badge/License-AGPL_v3-purple.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![Tests](https://img.shields.io/badge/tests-pytest-0A9EDC?style=flat&logo=pytest&logoColor=white)](https://pytest.org)
[![Lint](https://img.shields.io/badge/lint-ruff%20%2B%20mypy%20%2B%20pylint-D7FF64?style=flat)](https://github.com/astral-sh/ruff)
[![HTTP Client](https://img.shields.io/badge/httpx-0.28+-1f5582?style=flat)](https://www.python-httpx.org/)

> Fetch a URL once and grade its HTTP security headers A through F using the same weighted-rubric model as Mozilla Observatory.

> **My notes on this project:** originally built as part of [CarterPerez-dev's Cybersecurity-Projects](https://github.com/CarterPerez-dev/Cybersecurity-Projects) foundations tier. I completed this project independently as part of my SOC analyst training — see [PORTFOLIO.md](./PORTFOLIO.md) for my write-up and real scan findings.

*This is a quick overview, security theory, architecture, and full walkthroughs are in the [learn modules](#learn).*

> [!NOTE]
> **Foundations tier**, this project is built for someone who has never written Python before. The source code is heavily commented as a teaching aid, and the `learn/` folder explains every concept from zero.

## What It Does

- Performs one polite HTTPS request to the URL you provide and inspects the response headers
- Grades six security-critical headers against a weighted rubric (high = 30 pts, medium = 15 pts, low = 5 pts)
- Reports each finding as `ok`, `weak`, or `missing` with a one-line explanation of why
- Computes a 0 to 100 score and maps it to an A through F letter grade (90+ = A, 80+ = B, etc.)
- Catches subtly broken values like `Strict-Transport-Security: max-age=0` (header present, actively disabled) and flags them as `weak`, not `ok`
- Follows redirects and grades the **final** URL, the one your browser would actually land on
- Prints a colored Rich table plus a grade panel plus a recommendation list for every non-`ok` finding
- Returns meaningful exit codes: `0` for A/B, `1` for C/D, `2` for F or network error, useful in CI pipelines

## The Headers It Grades

| Header | Severity | What it stops |
|---|---|---|
| `Strict-Transport-Security` | high | SSL stripping on coffee-shop wifi |
| `Content-Security-Policy` | high | XSS via injected `<script>` tags |
| `X-Content-Type-Options` | medium | MIME-sniffing of uploaded files |
| `X-Frame-Options` | medium | Clickjacking via hidden iframes |
| `Referrer-Policy` | low | Leaking secret tokens via the Referer header |
| `Permissions-Policy` | low | Compromised third-party scripts abusing camera/mic/etc. |

Every header maps to a real attack class with a real history. The [`01-CONCEPTS.md`](learn/01-CONCEPTS.md) module walks through each one with concrete attack examples.

## Quick Start

```bash
./install.sh
just run -- https://example.com
# Grade: B, Score: 85 / 100  (example.com is missing CSP and Permissions-Policy)
```

> [!TIP]
> This project uses [`just`](https://github.com/casey/just) as a command runner. Type `just` to see all available commands.

## Demo URLs

| URL | Expected grade | Why |
|---|---|---|
| `https://github.com` | A | Comprehensive CSP, HSTS with `includeSubDomains`, almost every header set |
| `https://web.dev` | A | Google's own developer-docs site, full modern header set |
| `https://mozilla.org` | A | Mozilla practices what Observatory preaches |
| `https://example.com` | B / C | Has HSTS, but missing CSP, Permissions-Policy, and others |
| `http://neverssl.com` | F | Intentionally serves plain HTTP, no security headers at all |

## Sample Output
