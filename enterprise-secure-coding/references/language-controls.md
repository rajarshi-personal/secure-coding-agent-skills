# Language and platform controls

Read only sections relevant to the current change. Examples of tools are choices to evaluate against the repository's installed versions and configuration, not instructions to install all of them. Use official documentation for the actual framework/runtime version when defaults or APIs are uncertain.

## Java 17 and Java 21

- Use `PreparedStatement` or correctly bound JPA parameters for values; map dynamic column/order identifiers to fixed application constants. An ORM's raw-query escape hatch can still inject SQL. Scope repository operations to the authorized principal and tenant.
- Disable DTDs/external entities and external schema access in the specific XML parser API; test its effective configuration. Avoid Java native deserialization of untrusted input; restrict polymorphic JSON type selection and parsing size/depth.
- Use `SecureRandom`, vetted JCA providers and authenticated encryption correctly. Keep default trust-chain/hostname validation; do not add permissive trust managers. Never rely on SecurityManager as an application sandbox.
- Use try-with-resources, bounded pools/queues, request deadlines, safe temporary files, and explicit charset handling. Avoid request identity leaking through static state or improperly cleared thread locals.
- For Java 21 virtual threads, bound database/HTTP concurrency separately and review context propagation and mutable shared state. For Java 17 targets, do not introduce Java 21-only APIs. Test on both JDKs only when the project supports both.
- Verify security filters and method/resource authorization on direct calls and alternate routes. Preserve framework CSRF protection where applicable; disabling it requires an actual authentication/transport rationale.
- Verification options: Maven/Gradle wrapper tasks, JUnit integration tests, configured SpotBugs/FindSecBugs or other SAST, dependency analysis, and JMH/service load tests for relevant paths.
- Primary references: [Oracle Java security guides](https://docs.oracle.com/en/java/javase/21/security/) and [OWASP Java Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Java_Security_Cheat_Sheet.html).

## Python

- Use DB-API/ORM bound values; parameter placeholder syntax depends on the driver. Never interpolate queries with f-strings. Explicitly validate external dictionaries/JSON; hints and dataclasses alone do not validate runtime input.
- Do not deserialize untrusted pickle/marshal, call `eval`/`exec` on input, or use unsafe YAML loaders. Safe loaders still need size/nesting limits. Do not use `assert` for security invariants because optimized execution can remove it.
- Prefer subprocess argument arrays with `shell=False`; fix or allowlist executables and arguments, including attacker-controlled options. Use `secrets` for security tokens and appropriate constant-time comparison for sensitive values.
- Use context managers, safe temporary-file APIs, bounded parsers, and explicit connection/read timeouts. Bound task concurrency; move CPU-heavy work off async event loops using the project's established design.
- Keep framework escaping, cookie security, CSRF, and authorization active. Treat template source as trusted code; template escaping does not make attacker-written templates safe.
- Verification options: project tests/pytest, configured Bandit/Semgrep, dependency auditing, secret scanning, and endpoint profiling.
- Primary references: [Python security considerations](https://docs.python.org/3/library/security_warnings.html) and [subprocess security](https://docs.python.org/3/library/subprocess.html#security-considerations).

## TypeScript and JavaScript

- Validate JSON, request bodies, environment values, messages, and third-party responses at runtime. Type assertions and TypeScript interfaces do not enforce external data contracts.
- Use explicit writable-field mapping; avoid recursively merging arbitrary keys into application objects. Reject dangerous prototype-related keys where needed and use safe map structures. Validate dynamic property access against allowed operations.
- Preserve framework escaping; prefer text sinks over HTML sinks. Sanitize intentionally permitted rich HTML with a maintained, context-appropriate library. Avoid dynamic code execution; use CSP as defense in depth, not a substitute for safe rendering.
- Parameterize database queries. Prefer executable/argument APIs to command strings and constrain executable names/options. Do not expose server credentials in browser bundles or build-time public environment variables.
- Use platform crypto. Prefer established secure session handling; make token storage decisions using the actual XSS/CSRF threat model. Enforce all privileged operations on the server.
- Bound request parsing, regex work, queues, promises, and retries; honor cancellation and stream with backpressure. Prevent cross-user cache and server-rendering state leakage.
- Verification options: project typechecks/lint/tests, DOM and API integration tests, configured SAST, lockfile/dependency audits, and event-loop/load profiling.
- Primary references: [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices) and [OWASP DOM XSS guidance](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html).

## C# and .NET

- Use bound ADO.NET parameters or safe EF query APIs; inspect raw SQL calls and dynamic identifiers. Use explicit input DTOs so model binding cannot assign privileged fields.
- Apply ASP.NET Core policy and resource authorization, including tenant ownership. Keep anti-forgery protection for cookie-authenticated mutations. Avoid assuming route-level authentication authorizes every resource.
- Do not use BinaryFormatter with untrusted data; constrain JSON polymorphism/type metadata. Explicitly configure XML readers/resolvers against XXE and parsing exhaustion. Use `RandomNumberGenerator` and vetted crypto instead of `Random` for secrets.
- Preserve certificate validation and use platform secret storage/managed identities. Review Data Protection key persistence/access for the deployment model, including multi-instance services.
- Dispose resources, propagate `CancellationToken`, bound concurrency, avoid blocking async calls, and reuse HTTP clients through the established factory/lifetime pattern. Configure outbound destination restrictions and timeouts.
- Verification options: project build/test and analyzers, configured NuGet vulnerability analysis, secret scanning, integration tests, and BenchmarkDotNet/service benchmarks when appropriate.
- Primary references: [ASP.NET Core security](https://learn.microsoft.com/en-us/aspnet/core/security/) and [BinaryFormatter security](https://learn.microsoft.com/en-us/dotnet/standard/serialization/binaryformatter-security-guide).

## Android (Java and Kotlin where present)

- Treat the app as an untrusted client; enforce backend authorization independently. Never ship a reusable server secret. Use Android Keystore for cryptographic keys and protected app storage for data; SharedPreferences alone is not a secret vault.
- Minimize permissions and exported activities/services/receivers/providers. Validate caller access, incoming intents, URI grants, and deep-link parameters. Prefer explicit intents and appropriately immutable PendingIntents.
- Keep network security policy and TLS validation intact; prohibit unintended cleartext traffic. Apply pinning only when the threat model and certificate rotation/recovery plan justify it.
- Restrict WebView origins, navigation, local file/content access, and native bridges. Do not load untrusted content into a privileged bridge. Disable unnecessary JavaScript and release debugging.
- Review backup inclusion, screenshots/recents, notifications, logs, clipboard, and temporary files against data sensitivity. Apply biometric/key access controls according to risk; a successful local biometric prompt alone is not server authorization.
- Bound background work and network retries; avoid crypto/disk/network work blocking UI threads. Clean up lifecycle-bound work and sensitive session state.
- Verification options: Gradle tests/lint, instrumented tests on relevant API levels, release manifest/configuration review, configured mobile analysis, and MASTG tests for affected controls.
- Primary references: [Android security guidance](https://developer.android.com/privacy-and-security/security-best-practices) and [MASTG](https://mas.owasp.org/MASTG/).

## Swift, iOS, and related Apple platforms

- Store secrets in Keychain with deliberately selected accessibility/access-control policies; do not use UserDefaults or bundled files for secrets. Use platform crypto such as CryptoKit and secure system randomness.
- Retain ATS and normal TLS validation. Inspect custom URLSession delegates for trust bypasses. Introduce pinning only with a justified lifecycle and recovery strategy.
- Validate universal/deep links, pasteboard input, app extensions, and WKWebView messages. Restrict origins and navigation before exposing native capabilities. Enforce backend authorization independent of local app state.
- Use file protection and appropriate backup exclusion for sensitive files. Minimize entitlements and data exposed in logs, notifications, crash reports, and background UI snapshots.
- Parse external data with throwing/failable paths; avoid `try!`, forced unwraps, and unchecked indexing on untrusted input. Review unsafe pointers and C/Objective-C interoperability. Actor isolation helps concurrency but does not prove atomic business authorization across suspension points.
- Honor cancellation, bound parallel work and payload sizes, and keep heavy work off the main actor. Do not retain sensitive session state beyond its intended lifetime.
- Verification options: SwiftPM/Xcode tests, device/simulator integration tests, configured static analysis, appropriate sanitizers for unsafe/interoperability code, Instruments, and MASTG checks.
- Primary references: [Apple platform security](https://support.apple.com/guide/security/welcome/web), [Keychain services](https://developer.apple.com/documentation/security/keychain-services), and [CryptoKit](https://developer.apple.com/documentation/cryptokit).
