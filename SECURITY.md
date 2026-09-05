# Security Policy

_Last updated: September 5th 2026_

We take the security of Haggle seriously and we genuinely appreciate reports from researchers, users, and the community. This document describes how to report a vulnerability, what we'll do with it, and the rules of engagement.

---

## Supported versions

Security fixes are issued for:

- The **current minor release** (the latest version available on [GitHub Releases](https://github.com/Algeris/haggle-releases/releases)); and
- The **previous minor release** for a reasonable transition window after a new minor ships.

Older versions are not supported. If you find a security issue in an unsupported version, please first reproduce it on the current release before reporting.

You can find the current version in the app's **Settings → About** screen, in `package.json` at the repo root, or on the [GitHub Releases page](https://github.com/Algeris/haggle-releases/releases).

---

## Reporting a vulnerability

**Please do not report security vulnerabilities through public GitHub issues, GitHub Discussions, social media, blog posts, or any other public channel.** Public disclosure before a fix is available puts users at risk and is the single most common reason a coordinated-disclosure relationship breaks down.

### Where to send reports

Email **contact@algeris.com** or **security@algeris.com** with a clear subject line such as "Security report — [short description]".

If your report is sensitive enough that you'd like an encrypted channel, mention this in your initial email and we'll arrange one before you send the technical details.

### What to include

Please include as much of the following as you can:

1. The **type of issue** (e.g., remote code execution, sandbox escape, IPC injection, license-validation bypass, audio-capture privacy boundary, dependency CVE, etc.).
2. The **affected component(s)**: full file paths and, ideally, the commit hash or release version where the issue is present.
3. **Steps to reproduce**, including any special configuration, OS version, hardware, or network setup needed.
4. A **proof-of-concept or exploit code** if one is practical.
5. The **impact** — what an attacker could realistically do, and against which class of user.
6. Any **suggested mitigation** you've considered (optional).

### Our response

- We acknowledge receipt of every security report **within 72 hours** on weekdays.
- We aim to give you an initial assessment within **7 days** of acknowledgement.
- We aim to release a fix for confirmed in-scope issues within **30–90 days** of confirmation, depending on severity and complexity.
- We will keep you informed of progress and let you know when the fix is released.

---

## Safe Harbor

If you make a **good-faith effort** to follow this policy, we commit to:

- Treating your report as authorised security testing under any applicable computer-misuse or anti-hacking law.
- **Not pursuing legal action against you** for security research conducted under this policy.
- Working with you in good faith to understand and resolve the issue.
- Crediting your contribution publicly (with your consent) once the fix is released.

"Good-faith effort" means: you didn't access, modify, or destroy data beyond what was necessary to demonstrate the issue; you didn't degrade service quality or pivot to other systems; you didn't share the issue with anyone outside our coordinated process; and you gave us a reasonable opportunity to fix it before public disclosure.

---

## Coordinated disclosure timeline

Our default position is **coordinated disclosure with a 90-day window**:

- If we confirm an issue is in-scope, we will work with you on a timeline to release a fix.
- After **90 days from the date we confirmed the issue** (or sooner if a fix is shipped earlier), you may publicly disclose the issue.
- If a fix is shipped, we'll publish a corresponding **GitHub Security Advisory** and link your name (with consent) in the credits.

---

## Scope

The following areas of the Service are **in scope** for security reports:

- ★ **License activation &amp; validation** — license-server protocol, signature checks, hardware-ID generation, attempts to bypass device-binding.
- ★ **IPC bridge** — communication between the Electron renderer and main processes; preload bridge; exposed API surface.
- ★ **Audio &amp; screen capture** — privacy boundaries around what's captured, when, and where it's sent.
- ★ **Auto-update mechanism** — integrity of updates, signature verification, downgrade protection.
- ★ **Phone Mirror pairing** — pairing-token handling, replay or hijack of paired sessions.
- **Authentication &amp; account boundaries** — anything that lets one user access another user's licence, quota, or data.
- **Payment-flow handling** (the parts the desktop app participates in — Dodo Payments handles the actual card processing).
- **Network communication** — TLS configuration, certificate handling, request integrity.
- **Local data storage** — SQLite database, encrypted license cache, API-key storage.
- **Dependency vulnerabilities** — Electron, Node, and Rust-crate CVEs that materially affect the shipped app.
- **The haggle.algeris.com website &amp; API** — server misconfigurations, common web vulnerabilities, and information leaks.

---

## Out of scope

The following are **not** considered in-scope vulnerabilities under this policy:

- **Issues in unsupported versions** (see "Supported versions" above).
- **Issues already publicly known** or already tracked in our issue tracker / advisories.
- **Reports without a working proof-of-concept** or with only theoretical impact.
- **Bugs that don't have a security impact** — please file these as regular GitHub issues.
- **Reports from automated scanners** without manual verification or a clear exploitability case.
- **Self-XSS** and issues that require an attacker to already have full control of the victim's machine.
- **Social-engineering attacks** against support or users.
- **Physical-access attacks** requiring physical possession of an unlocked device.
- **Denial-of-service** attacks against our infrastructure.

### AI-specific behaviour — clarification

The following are **not vulnerabilities** under this policy:

- **AI hallucinations or factual errors** in model outputs.
- **Prompt-injection attacks** that cause the underlying AI provider to behave in unintended ways. These are limitations of the underlying models, discussed in our [Terms &amp; Conditions](https://haggle.algeris.com/terms).
- **Jailbreaks** of the AI assistant persona that don't expose data or control the user's machine.

A prompt-injection issue **becomes** in-scope if it leads to **exfiltration of user data**, **unauthorised local file access**, **privilege escalation in the IPC bridge**, or **execution of arbitrary code on the user's machine**.

---

## Public advisories &amp; credit

When a confirmed issue is fixed, we publish a **GitHub Security Advisory** at:

<https://github.com/Algeris/haggle-releases/security/advisories>

---

## Official Links & Resources

- **Website**: [https://haggle.algeris.com](https://haggle.algeris.com)
- **Terms of Service**: [https://haggle.algeris.com/terms](https://haggle.algeris.com/terms)
- **Privacy Policy**: [https://haggle.algeris.com/privacy](https://haggle.algeris.com/privacy)
- **Refund Policy**: [https://github.com/Algeris/haggle-releases/blob/main/refund.md](https://github.com/Algeris/haggle-releases/blob/main/refund.md)
- **Releases**: [https://github.com/Algeris/haggle-releases/releases](https://github.com/Algeris/haggle-releases/releases)
- **Email:** `contact@algeris.com` / `security@algeris.com`

Thanks for helping keep Haggle safe.
