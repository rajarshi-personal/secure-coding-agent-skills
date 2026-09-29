# Enterprise Secure Coding Agent and Skill

Reusable instructions for secure implementation and review across Java 17, Java 21, Python, TypeScript/JavaScript, C#, Android, and Swift. The agent guide and skill work independently or together.

## Files

```text
AGENTS.md
README.md
enterprise-secure-coding/
    SKILL.md
    references/
        language-controls.md
```

- **AGENTS.md**: standalone Enterprise Secure Coding Agent guidance, including baseline controls for every supported stack.
- **enterprise-secure-coding/SKILL.md**: reusable secure-coding workflow with independent security, comment, verification, and performance requirements.
- **language-controls.md**: focused stack-specific details bundled with the skill.

The conventional filenames are `AGENTS.md` (plural, uppercase) and `SKILL.md` (singular, uppercase). The descriptive skill name is `enterprise-secure-coding`. An AGENTS.md file guides an existing coding agent; it does not launch a separate process or scanner.

## Setup and independent usage

### Agent only

Copy `AGENTS.md` into the target repository root, or merge its guidance into an existing file without overwriting project instructions. Start a session in that repository. Codex discovers applicable AGENTS.md instructions; nested project instructions can refine their scope. See [official AGENTS.md guidance](https://developers.openai.com/codex/guides/agents-md).

Example prompt:

```text
Follow AGENTS.md. Implement a Java 21 endpoint to update an invoice.
Derive the tenant from the verified session and enforce invoice ownership.
Add security rationale comments and regression tests. Run relevant checks
and report performance evidence and any unverified assumptions.
```

### Skill only

Copy the entire `enterprise-secure-coding` directory, including `references`, into your coding tool's skill directory. For Codex repository discovery, use:

```text
<your-repository>/.agents/skills/enterprise-secure-coding/SKILL.md
<your-repository>/.agents/skills/enterprise-secure-coding/references/language-controls.md
```

Reload/start a session if necessary and verify that the skill appears in the available skills list. The delivered directory is a portable package; it has not been installed globally. Discovery and invocation follow [official skill documentation](https://developers.openai.com/codex/skills).

Example prompt:

```text
Use $enterprise-secure-coding to fix SQL injection in this Python module.
Preserve behavior, use the installed database driver's parameter syntax,
add an explanatory security comment and a regression test, and run the
applicable checks. No AGENTS.md is required for this task.
```

For tools without skill discovery, explicitly supply SKILL.md and its referenced language guidance as task instructions. Do not assume arbitrary Markdown filenames are loaded automatically.

### Agent and skill together

Place AGENTS.md at the repository root and install the skill as above. AGENTS.md asks the agent to load the available skill while writing application code. Explicit invocation makes the intended use clear:

```text
Follow AGENTS.md and use $enterprise-secure-coding to implement this feature.
Apply the controls while writing code, add nearby security rationale comments,
and complete one combined security and performance verification report.
```

## More example tasks

| Stack | Example request |
| --- | --- |
| Java 17 | Harden this XML import against external entity access and oversized input; preserve Java 17 compatibility and test rejected documents. |
| Java 21 | Secure this virtual-thread HTTP fan-out with destination restrictions, deadlines, and bounded downstream concurrency; measure latency. |
| Python | Review this file-upload handler for traversal, symlink races, archive expansion, and authorization; patch verified in-scope defects. |
| TypeScript/JavaScript | Implement a profile update with runtime validation and explicit writable fields; test role assignment and cross-user access attempts. |
| C# | Secure this ASP.NET Core document endpoint with resource authorization, safe EF queries, and cancellation; test cross-tenant access. |
| Android | Review exported components, deep links, WebView bridges, token storage, and release configuration; fix issues within this module. |
| Swift | Secure this iOS sign-in flow's Keychain handling, URLSession trust, and deep-link validation; test invalid callback state and cancellation. |

For an audit without changes, explicitly say: `Review only; do not modify files.`

## Example security comment

Illustrative Python DB-API code using SQLite parameter syntax, inside an existing handler. `principal` must already be authenticated and authorized to read invoices in its tenant; `invoice_id` must already be validated. This fragment illustrates one query control, not a complete endpoint.

```python
# SECURITY: Bind values and scope the lookup to the verified principal's tenant,
# preventing SQL injection and reads of invoices belonging to another tenant.
row = connection.execute(
    "SELECT id, total FROM invoices WHERE id = ? AND tenant_id = ?",
    (invoice_id, principal.tenant_id),
).fetchone()
```

Tests should show that a valid invoice is returned, another tenant's invoice is not returned, and hostile values cannot alter SQL structure. Applications with narrower per-user access also need that authorization predicate. Comments must accurately describe the implemented protection and introduce no runtime logging or computation.

## Security and performance expectations

The package uses [OWASP Top 10:2025](https://top10.owasp.org/2025/) as its risk taxonomy and targets applicable enterprise verification requirements from [ASVS 5.0.0](https://owasp.org/projects/asvs), with [API security](https://api-security.owasp.org/editions/2023/en/0x11-t10/) and [mobile security](https://mas.owasp.org/MASVS/) guidance where relevant. Baseline checked 2026-09-29. This is an assurance target, not certification.

The agent must implement protections while coding, explain guards in comments, test relevant attack paths, and report evidence. It must avoid unnecessary runtime costs and measure changes that affect critical paths. Necessary security work can have a cost; these files cannot guarantee zero vulnerabilities or zero overhead. A clean scan does not establish either claim.

Provide project-specific context when available: supported framework versions, data classification, authentication and tenant model, required build/test commands, approved scanners, deployment assumptions, and existing performance budgets. Otherwise, the agent should inspect the repository and disclose unresolved assumptions.

## Completion report and practical enforcement

Expect a concise report containing:

1. Changed controls and protected invariants.
2. Confirmed findings with severity, evidence, location, and remediation; hypotheses separately.
3. Actual checks and results, with `NOT RUN` or `N/A` explained.
4. Performance measurements or an explicit lack of evidence.
5. Remaining risks/exceptions and `READY WITHIN VERIFIED SCOPE`, `NEEDS REMEDIATION`, or `VERIFICATION INCOMPLETE`.

These documents establish agent behavior. They do not install security tools, run continuously, or configure branch protection. For mechanically enforced enterprise gates, configure your actual CI to run appropriate builds/tests, SAST, dependency and secret checks, plus applicable performance checks, and make those statuses required for merging. Keep scanner coverage and advisory data maintained. This package does not modify CI.

A confirmed introduced vulnerability or unresolved in-scope vulnerability prevents a security-ready assessment unless an authorized owner documents an exception with rationale, compensating controls, and expiry. Required verification that cannot run must remain visibly incomplete. Never silently weaken a guard or suppress a finding to meet a performance target or obtain a passing status.
