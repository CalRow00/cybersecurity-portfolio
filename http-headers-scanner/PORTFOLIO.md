# HTTP Security Headers Scanner

## What it does
A command-line tool that scans any website and checks it against six
HTTP security headers, grading the result A–F based on what's present,
missing, or misconfigured.

## Why I built it
Practicing vulnerability assessment fundamentals for SOC analyst work —
specifically how to identify and interpret defensive misconfigurations
on web infrastructure.

## Tech used
Python, httpx (HTTP requests), rich (terminal output), pytest (testing)

## How to run it
just run -- <url>

## Sample findings

### github.com — Grade A (95/100)
All six headers present and correctly configured. Minor deduction:
Permissions-Policy not set, meaning third-party scripts could still
request camera/microphone access if compromised — low real-world risk
since GitHub doesn't embed untrusted third-party scripts.

### facebook.com — Grade A (95/100)
Missing Referrer-Policy header. This means URLs a user navigates away
from Facebook could leak in full (including any query parameters) to
the destination site. Low severity on its own, but worth flagging as
a defense-in-depth gap.

## What I learned
- How six specific security headers each defend against a real attack
  class (SSL stripping, XSS, clickjacking, MIME sniffing, referrer
  leakage, feature abuse)
- How to set up a Python dev environment and troubleshoot Windows/Git
  Bash tooling issues (PATH, PowerShell shell conflicts with `just`)
- Practiced writing up technical findings in plain language for a
  non-technical audience
