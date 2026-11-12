# Assignment 11 – Ethics and Responsible-Use Statement

**Team Members:** Nate Heath, Elliot Warner, William Thomas

**Project Title:** Data-Day

**Goal Statement:** To build and open source a 64 x 32 pixel desktop display and the developer platform for creating applications for the device.

---

## Privacy Summary

| Data item | Source | Interface | Purpose | Consent | Storage | Access | Retention |
| --- | --- | --- | --- | --- | --- | --- | --- |
| User Python Script | Local machine | I5 (Local Machine -> Upload Server) | Execution on Orange Pi to render visuals/apps on 64x32 LED matrix (REQ-01, REQ-05) | Explicit Opt-in via Web UI | Stored unencrypted on Orange Pi microSD card | Accessible only by local system user and device administrator | Retained until explicitly deleted |
| Upload Payload Metadata | Local machine request headers | I5 (Local Machine -> Upload Server) | API response formatting, and slot assignment (REQ-06) | Explicit Opt-in | Stored in local JSON registry file | Read/Write access restricted strictly to the FastAPI backend | Deleted when the corresponding application file is removed |
| Sanitization & Security Inspection Status | Security scanner | I6 (Upload Server -> Device Application) | Validating code safety (NFR-02) and blocking unsafe imports | Automated System Pre-requisite | In-memory during AST evaluation. Pass/fail boolean logged to system | Readable by device log watcher and administrator | Log entries rotated automatically, lasts 30 days stored locally |
| Dial Rotation State | Rotary encoder hardware | I1 (Dial -> Orange Pi GPIO) | Navigation of application selection view (REQ-02) and user app inputs | Implicit via Physical Action | Transient in-memory state inside Linux kernel GPIO buffer | Hardware kernel driver | Discarded immediately after state transition event handling |
| Execution logs | Orange Pi execution manager | I2 (Orange Pi -> Device Application) | Debugging, system recovery and crash tracking | Operational Necessity | Plaintext file written to Orange Pi microSD card | System user | Stored locally for 30 days |
| Framebuffer | SDK / Application runtime | I4 (Device Application -> Display Matrix) | Driving physical HUB75 LED display panel hardware via memory mapping | Implicit via Operation | Orange Pi volatile memory | Driver thread | Wiped immediately if exiting app or the system is shutdown |

### Explicitly Removed

**Supabase:** Originally, we planned on using a Supabase database and cloud telemetry pipeline for online storage and tracking. We have since removed it since the product is a single-owner local hardware device and the applications can be stored/installed on GitHub.

---

## Security Summary

### Default Credentials

**Risk:** Computers like the Orange Pi Zero 3 often ship with default operating system credentials.

**Mitigation:** To access the repository and install the software on the Orange Pi, the user must already be signed in.

### Unauthenticated Endpoints

**Risk:** The local REST API allows code uploads, which could permit malicious lateral network attacks.

**Mitigation:** API key authentication and local subnet pinning. Users will have a secret local token that must be authenticated when using the API. These tokens will only be valid for the lifetime of the session. Also, the FastAPI server binds exclusively to the local network interface and will reject external requests.

### Secrets in Source Control

**Risk:** Committing SSH keys, WiFi passwords or API tokens to the repository.

**Mitigation:** Implement strict .gitignore rules to prevent environment files from entering version control.

### Repository Git History Audit Results

### Self-Audit Questions and Answers

| Self-Audit Question | Team answer |
| --- | --- |
| Are default passwords and keys removed? | Yes |
| Are all endpoints authenticated or locally bound? | Yes |
| Has the full git history been checked for exposed secrets? | Yes |
| Is user-submitted code isolated from host OS system files? | Yes |
| Are we preventing uploaded scripts from filling memory/storage? | Yes |

---

## Licensing Summary

No dependencies are installed yet. Versions named in the Test Plan are listed; everything else is unpinned and will be pinned in requirements.txt / package.json at the first code commit, with this table updated.

| Name | Version | License (SPDX) | Type | Obligation |
| --- | --- | --- | --- | --- |
| FastAPI (upload server) | unpinned | MIT | Permissive | Keep copyright and license notice |
| Uvicorn (ASGI server) | unpinned | BSD-3-Clause | Permissive | Keep notice; no endorsement using their name |
| python-multipart (FastAPI file uploads) | unpinned | Apache-2.0 | Permissive | Keep notice and NOTICE file; state changes if modified |
| React (upload page) | unpinned | MIT | Permissive | Keep notice |
| Node.js (build only, not shipped) | 20 LTS | MIT | Permissive | None for our release |
| Python | 3.11 | PSF-2.0 | Permissive | Keep notice if redistributed in an image |
| pytest (test only) | 8 | MIT | Permissive | None (not shipped) |
| pytest-cov (test only) | unpinned | MIT | Permissive | None (not shipped) |
| httpx (test only) | unpinned | BSD-3-Clause | Permissive | None (not shipped) |
| HUB75 display driver (not yet chosen; any compatible HUB75 LED panel supported) | n/a | Must be MIT, BSD, or Apache-2.0; LGPL only if dynamically linked and unmodified | Permissive required | Keep notice; a GPL driver may not be used in the same process as our code |
| GPIO library for dial (not yet chosen) | n/a | Must be MIT, BSD, or Apache-2.0; LGPL only if dynamically linked and unmodified | Permissive required | Keep notice |
| Armbian OS / Linux kernel | Debian 12 Bookworm | GPL-2.0-only (kernel) and others | Copyleft | Separate program, not linked with our code (mere aggregation); if we publish a full image, offer corresponding source |
| Datasets | none | n/a | n/a | None used; demo apps calling weather / UC shuttle APIs will add those services' terms here |
| Fonts, icons, images | none yet | n/a | n/a | Bitmap font for text rendering (task 13) will be added with its license |

**Project license:** MIT (LICENSE file at the repository root).

**Compatibility:** Every permissive row above (MIT, BSD-3-Clause, Apache-2.0, PSF-2.0) is compatible with an MIT project; we keep each one's copyright and license notice in NOTICE.md. Test-only and build-only tools are not distributed with the project. Armbian and the Linux kernel are copyleft, but they are a separate operating system our code runs on rather than code we link to or modify, so they place no license obligation on Data-Day's source.

**Copyleft flag and decision:** The most widely used HUB75 driver, rpi-rgb-led-matrix, is GPL-2.0-or-later. Linking it into our SDK would require releasing Data-Day under the GPL, which conflicts with our MIT license. Because Data-Day is meant to work with any compatible HUB75 LED panel, we are not tied to that driver. We decided that the display driver and GPIO library must be permissively licensed (or LGPL, dynamically linked and unmodified). Any GPL driver is excluded unless it runs as a separate process that our SDK talks to over a pipe or socket. Each library's license will be checked against this rule before it is added to requirements.txt.

---

## Attribution List

This same list is maintained in [NOTICE.md](NOTICE.md) at the repository root.

### Libraries

| Library | Version | Author / Maintainer | Source URL | License |
| --- | --- | --- | --- | --- |
| FastAPI | unpinned | Sebastián Ramírez and contributors | https://github.com/fastapi/fastapi | MIT |
| Uvicorn | unpinned | Encode OSS and contributors | https://github.com/encode/uvicorn | BSD-3-Clause |
| python-multipart | unpinned | Andrew Dunham, Marcelo Trylesinski and contributors | https://github.com/Kludex/python-multipart | Apache-2.0 |
| React | unpinned | Meta Platforms, Inc. and contributors | https://github.com/facebook/react | MIT |
| Node.js (build only) | 20 LTS | OpenJS Foundation and contributors | https://github.com/nodejs/node | MIT |
| Python | 3.11 | Python Software Foundation | https://www.python.org | PSF-2.0 |
| pytest (test only) | 8 | pytest-dev | https://github.com/pytest-dev/pytest | MIT |
| pytest-cov (test only) | unpinned | pytest-dev | https://github.com/pytest-dev/pytest-cov | MIT |
| httpx (test only) | unpinned | Encode OSS and contributors | https://github.com/encode/httpx | BSD-3-Clause |
| Armbian (operating system) | Debian 12 Bookworm | Armbian project | https://www.armbian.com | Various; Linux kernel GPL-2.0-only |
| HUB75 display driver | not yet chosen | TBD | TBD | Must be MIT, BSD, or Apache-2.0 (see Licensing Summary) |
| GPIO library for dial | not yet chosen | TBD | TBD | Must be MIT, BSD, or Apache-2.0 (see Licensing Summary) |

### Code adapted from tutorials or other repositories

None so far. Any snippet adapted from a tutorial, Stack Overflow, or another repository will carry a comment above it with the source URL and license and will be added to this list in the same pull request.

### Datasets

None used. If demo applications call outside services (for example weather or UC shuttle data for US01), each service will be listed here with how its data is collected and its usage restrictions before the demo ships.

### Non-code assets (icons, fonts, images)

None so far. The bitmap font needed for text rendering (task 13) will be added here with its author and license before it is committed.

---

## AI-Use Disclosure

> "Generative AI tools may be used for drafting and code assistance but not for the design decisions or written analysis that are assessed in this course."

| Tool | Used by | Task | Column | How verified |
| --- | --- | --- | --- | --- |
| Claude (Anthropic) | Elliot Warner | Looked up OS default credentials (Armbian, Orange Pi images) and the SPDX licenses of planned dependencies | Generally OK | Checked each claim against the project's own documentation or license file |
| ChatGPT | Nate Heath | General architecture planning and ensuring components cohesively work together | Generally OK | Checked responses against technical component specs and documentation |
| Copilot | William Thomas | Writing repetitive unit test cases for SDK pixel operations | Generally OK | Code-reviewed line-by-line and verified by running them |

**Confirmation.** Each member confirms that the architecture, data model, priorities, and design rationale in this project were the team's own.

- Nate Heath: "I confirm the architecture, data model, priorities, and design rationale were the team's own." Nate Heath, Date: 11/11/2026
- Elliot Warner: "I confirm the architecture, data model, priorities, and design rationale were the team's own." Elliot Warner, Date: 11/11/2026
- William Thomas: "I confirm the architecture, data model, priorities, and design rationale were the team's own." William Thomas, Date: 11/11/2026
