Stylus Trace Studio
Funding Ask
25,000 USD
Submitted on
21 Sep, 2025
Milestones
5
Category
Developer Tooling
Details
Arbitrum Stylus brings a second virtual machine to the stack, letting teams write smart contracts in Rust and other Wasm languages that interoperate with Solidity on the EVM. That power comes with a painful gap. When a transaction behaves badly or costs spike, developers are left staring at raw trace JSON or dropping into low-level debuggers. It is slow, brittle, and hides the real story behind HostIO usage and the hot paths where Wasm and EVM meet. Teams guess, rerun tests, and still ship regressions to testnet or mainnet. What should be a quick diagnosis turns into hours of forensic work, and the cost of that uncertainty compounds across every release.

Stylus Trace Studio turns those opaque traces into something you can see, trust, and act on. The CLI ingests debug_traceTransaction with the Stylus tracer, aggregates HostIO events and call boundaries, and produces clear flamegraphs and concise JSON reports that highlight the true bottlenecks. A simple web viewer lets you zoom and compare runs side by side, while a ready-made GitHub Action spins up the Nitro dev node, replays a scripted transaction, and fails the pull request when thresholds are exceeded. No secrets, no keys, no reliance on paid RPCs by default. It complements cargo-stylus replay for step-through debugging and sits alongside unit test helpers, but fills the missing layer of aggregate, CI-friendly observability that catches problems before users ever feel them.

The result is a calmer, faster feedback loop for every Stylus team. Engineers get truthful pictures of performance and cost, reviewers get artifacts they can understand at a glance, and product owners get confidence that each merge moves the system forward instead of opening a hole. By making Stylus execution visible and comparable, the project gives Arbitrum builders a practical foundation for reliability at scale and a path to optimize without guesswork.
What innovation or value will your project bring to Arbitrum? What previously unaddressed problems is it solving? Is the project introducing genuinely new mechanisms.
Innovation and value:
Stylus Trace Studio gives Arbitrum developers a clear, Stylus aware observability layer. It turns debug_traceTransaction with stylusTracer into visual flamegraphs, simple before and after comparisons, and CI checks that stop performance and cost regressions before they ship. Teams get a truthful picture of WASM to EVM hot paths and HostIO usage, all generated locally with the Nitro dev node, with no keys, no paid RPC, and no backend.
Previously unaddressed problems:
Developers lack a transaction level performance view specific to Stylus. Current options focus on step by step replay or generic EVM views that miss WASM boundaries and HostIO behavior. This leaves guesswork around cost spikes and hidden bottlenecks. Stylus Trace Studio fills the gap by aggregating execution data into visuals that people can read quickly and by automating regression checks in CI so issues are caught early without expensive rounds on live networks.
Genuinely new mechanisms:
Stylus specific flamegraphs and side by side comparisons that highlight HostIO hotspots and cross VM execution.
a CI ready workflow that starts the Nitro dev node, runs a scripted transaction, produces reports, and fails pull requests when agreed thresholds are exceeded.
best effort source hints when debug info is present while still producing useful profiles when it is not.
Together these pieces create a practical standard for Stylus observability on Arbitrum. They lower iteration cost, improve reliability, and raise developer confidence across the ecosystem.
What is the current stage of your project.
Ideation/research
Do you have a target audience? If so, which one.
Developers building on Arbitrum with Stylus and Solidity who need clear performance and cost visibility during development and review
Protocol and dApp teams working on DeFi, gaming, and infra that run mixed WASM and EVM logic
Performance and reliability engineers who maintain CI pipelines and enforce budgets on HostIO and latency
Security and audit teams that want readable execution evidence to support findings
Orbit chain builders who need local, repeatable profiling for custom chains
DevRel and education groups that teach Stylus and need simple, visual examples for workshops and docs
Do you know about any comparable protocol, event, game, tool or project within the Arbitrum ecosystem.
Yes. There are adjacent tools and they serve different needs. None convert Stylus stylusTracer output into Stylus-aware flamegraphs with before and after diffs and CI regression gating.
cargo stylus replay Official step by step debugging with GDB or LLDB. Logic inspection only. No aggregate performance view or CI thresholds.
Nitro dev node Local Arbitrum node and trace endpoints. Provides the environment. Does not visualize traces or produce reports.
OpenZeppelin Motsu and Stylus test helpers Rust unit testing and utilities. Contract correctness focus. Not transaction level performance profiling.
Remix plugin for Stylus and the Stylus playground Editor and learning tools. Helpful for authoring and quick deploys. Not trace ingestion or analysis.
VS Code Stylus extensions Editing ergonomics. No tracing or profiling.
Arbiscan debug and VM trace views Explorer introspection for EVM. Limited or no Stylus HostIO visibility and no flamegraphs.
General EVM debuggers such as Tenderly Great for Solidity and EVM traces. Not Stylus specific and no WASM to EVM hotpath profiling.
Recently funded local environment patches Add native Arbitrum precompiles and deposit transaction support to Hardhat and Foundry. Improve local correctness. Do not turn Stylus traces into visuals or CI checks.
Overall, good building blocks exist. There is no public tool that ingests Stylus stylusTracer traces and outputs readable flamegraphs and diff reports while enforcing performance budgets in CI. That is the gap this project fills.
Have you received a grant from the DAO, Foundation, or any Arbitrum ecosystem related program or conducted any IRL like a hackathon or workshop.
Yes. We received a grant in the last season which birthed the "Arbitrum Creative Hub". We surpassed our KPIs, collaborated closely with key stakeholders in Arbitrum during our hackathon, we also produced 20+ robust testnet ready Stylus powered applications.
Kindly check full report here: <https://forum.arbitrum.foundation/t/arbitrum-creative-hub-final-report/29082>
Have you received a grant from any other entity in other blockchains that are not Arbitrum.
Yes. We have received grant from NEAR and Solana.
What is the idea/project for which you are applying for a grant.
Stylus Trace Studio gives Arbitrum developers a practical way to see how Stylus transactions actually execute. Stylus adds a second virtual machine (Wasm) alongside the EVM so teams can write high-performance contracts in Rust or C while staying interoperable with Solidity; this multi-VM power also creates blind spots when a transaction is slow or costly because raw traces are hard to interpret at a glance. Our tool consumes the Nitro node’s native stylusTracer (via debug_traceTransaction) and converts that JSON into readable flamegraphs, concise summaries, and “before vs after” diffs that highlight HostIO hot paths and cross-VM bottlenecks. It complements the official cargo-stylus replay flow for step-through debugging by adding aggregate, CI-friendly observability. Teams can run everything locally on the Nitro dev node to avoid paid tracing plans.
Concretely, the project has three parts. A CLI pulls a Stylus trace from a local Nitro dev node or a tracing-enabled RPC and aggregates HostIO events and VM boundaries into SVG flamegraphs plus a JSON report with the top hot paths and HostIO event counts (including storage-related HostIO when aArbitrum Docsvailable). A static web viewer opens those files for zoom, search, and side-by-side diffs. A GitHub Action starts the Nitro dev node inside CI, replays a scripted transaction, produces the reports, and fails the pull request when thresholds are exceeded so performance and cost regressions are caught before testnet or mainnet. This replaces manual JSON inspection with artifacts reviewers can act on, and it aligns with how Arbitrum recommends working with traces today.
Implementation plan:
Trace capture and schema
Default capture path is local Nitro dev node. The CLI connects to the node and calls debug_traceTransaction with {"tracer":"stylusTracer"} to obtain the raw JSON; for users with a tracing provider, the CLI can point at that RPC as well. We will version the parser against the documented stylusTracer fields and smoke-test on the dev node plus at least one public provider each release. (Note: many providers gate debug_* behind paid tiers, which is why we default to the dev node.)
Aggregation and profiling
The CLI converts HostIO records and WASM↔EVM boundaries into collapsed stacks with weights, then renders SVG flamegraphs and emits a JSON summary of top hot paths and HostIO categories. Where builds include debug symbols, we surface best-effort source hints for file or function; when symbols are absent, profiles still label modules and HostIO categories so they remain useful. This creates an aggregate view that step-through debuggers and generic EVM explorers do not provide for Stylus execution.
Diff and regression checks
We normalize two profiles from the same scripted run, compute deltas, and render a diff that highlights increases and decreases. The GitHub Action runs against the local Nitro dev node, executes the scripted transaction or integration test, generates profiles, uploads them as CI artifacts, and enforces thresholds on totals like HostIO so teams block regressions in review without needing keys or paid endpoints.
Developer experience and documentation
We will publish a starter repo with a small Stylus contract and a Solidity caller that demonstrates the full flow end-to-end, plus documentation that includes Nitro dev-node quick start, a CI cookbook, and guidance on when to switch to cargo-stylus replay for step-through debugging. We will also point to OpenZeppelin Motsu for Rust unit tests, clarifying how unit-level correctness complements transaction-level profiling.
Outline the major deliverables you will obtain with this grant.
Milestone 1 Trace ingest and flamegraph:

CLI that fetches stylusTracer output via debug_traceTransaction from the Nitro dev node or a tracing RPC
Parser and versioned JSON schema for the profile summary output
SVG flamegraph generation
Cross platform builds and release packaging
Unit tests plus an end to end smoke test using a sample transaction
Milestone 2 Diff engine and CI Action

Before and after diff generation plus a machine readable diff report schema
Threshold configuration for regression gating
GitHub Action that starts a Nitro dev node in CI, runs a scripted transaction or test, generates profile artifacts, uploads them, and fails PRs on regression
Example workflow files and caching guidance
Milestone 3 Static viewer and source hints

Static web viewer that opens profiles locally with zoom and search
Side by side diff view
Best effort source hints when debug info is present and graceful fallback labels when not
Hosted static demo build and exportable bundle
Milestone 4 Docs, starter repo, templates, demo

Starter repository with a Stylus contract, a Solidity caller, and a green CI pipeline producing flamegraph artifacts
Quickstart docs, CI cookbook, troubleshooting, and provider notes
At least one education template or lesson plan using the tool
At least one Orbit oriented template example using a custom chain ID and RPC
One short demo video and one tutorial post showing the full workflow
Milestone 5 Post release support and ecosystem enablement

Six months of post release support as defined below
Monthly smoke test logs
At least three workshops or posts demonstrating the tool during the support window
Please explain how your idea/project aligns with the Arbitrum ecosystem goals.
Stylus Trace Studio strengthens Arbitrum’s core goal of developer excellence. It gives builders a clear view of how Stylus transactions actually run by turning tracer output into flamegraphs, readable summaries, and before versus after diffs. That makes performance and cost visible during development and code review, not after a failed deploy. Teams catch regressions early, shorten feedback loops, and ship with confidence. This is practical, open source developer tooling that any project on Arbitrum One, Stylus, or Orbit can adopt in minutes, which directly supports the ecosystem’s push for best in class tooling.
It also supports DeFi dominance and ecosystem expansion. DeFi, gaming, and infrastructure projects depend on predictable cost and latency. When developers can see HostIO hot paths and cross VM bottlenecks, they remove hidden risks that cause outages, liquidations, or bad user experience. Safer and faster iteration encourages larger feature sets and more ambitious designs. The tool runs locally with the Nitro dev node, so teams avoid paid tracing plans and can add performance checks to every pull request. That lowers the barrier for new builders and for Rust and C engineers who are entering the Arbitrum ecosystem through Stylus.
As a growth force, the project raises the quality floor across many teams at once. Reviewers get artifacts they can read in seconds. Maintainers can enforce simple thresholds in CI. Educators and DevRel can show live, visual examples when teaching Stylus. Over time this creates a culture of performance hygiene and reliable releases. Better developer experience brings more projects, more retained teams, and more users, which compounds network activity and helps Arbitrum sustain a leadership position in both DeFi and broader onchain applications.
What is your requested grant.
$25k
Website.
na
Please provide a detailed breakdown of the budget in term of utilizations, costs and other relevant information.
CLI: trace ingest and flamegraphs - $9,000 stylusTracer parsing, stack aggregation, SVG flamegraph generation, JSON summary schema, unit tests, cross-platform builds, release packaging.
Diff engine and CI Action - $6,000 Deterministic before/after comparison, machine-readable diff, thresholds, GitHub Action that boots the Nitro dev node, runs scripted tx/tests, captures profiles, uploads artifacts, and fails PRs on regression. Includes example workflow and caching guidance.
Static web viewer and source hints - $6,000 Static SPA that opens profiles locally, with zoom, search, and side-by-side diffs. Best-effort file or function hints when symbols exist, graceful labels when not. Basic a11y and performance checks.
Docs, starter repo, onboarding, polish - $3,000 Quickstarts for Nitro dev node and local profiling, CI cookbook, provider notes about tracing availability, positioning guide showing how this complements step-through debugging and unit tests, and a minimal starter repo (Stylus contract plus Solidity caller) with a green CI pipeline. One short demo video.
Ops and maintenance buffer - $1,000 Light ongoing items for the 6-month window: monthly smoke tests on the dev node and one public provider, minor release chores, issue triage within 72 hours, and modest costs such as Apple Developer account or Windows code signing if used, plus any CI minutes for private mirrors. If code signing is not used, binaries will be shipped unsigned with clear instructions.
Cost controls and notes:
Hosting: viewer is a static site; we use GitHub Pages or equivalent. No backend.
RPC usage: default workflow runs on the local Nitro dev node to avoid paid tracing. Public tracing endpoints are optional and documented.
Compatibility: parser is versioned; each release is smoke-tested on the dev node and one public provider.
Licensing: MIT. Public repos, semantic versioning, changelog.
Provide a list of the milestones, with the USD amount of the grant associated to it, the deliverables that will be provided, and the estimated completion time.
Milestone 1 — Trace ingest and flamegraph
Amount: $9,000 Time: 3 weeks Deliverables:
CLI that reads stylusTracer via debug_traceTransaction from local Nitro dev node or a tracing RPC
SVG flamegraph generator
JSON summary schema with top hot paths and HostIO event counts
Unit tests and cross platform builds
Milestone 2 — Diff engine and CI Action
Amount: $6,000 Time: 2 weeks Deliverables:
Profile diff generator with machine readable report
Threshold policy for HostIO and time based metrics
GitHub Action that starts Nitro dev node, runs scripted transaction, captures profiles, uploads artifacts, and fails pull requests on regression
Example workflow and caching guidance
Milestone 3 — Static web viewer and source hints
Amount: $6,000 Time: 2 weeks Deliverables:
Single page viewer that opens profiles locally with zoom and search
Side by side diff view
Best effort source hints when symbols are present and clear labels when not
Published static bundle and public site
Milestone 4 — Docs, starter repo, onboarding, polish
Amount: $3,000 Time: 1 week
Deliverables:
Starter repository with a Stylus contract, a Solidity caller, and a green CI pipeline producing flamegraph artifacts
Quickstart docs, CI cookbook, troubleshooting, and provider notes
At least one education template or lesson plan using the tool
At least one Orbit oriented template example using a custom chain ID and RPC
One short demo video and one tutorial post showing the full workflow.
Milestone 5 — Ops and maintenance buffer
Amount: $1,000 Time: 6 months of light maintenance after v1 Deliverables:
At least three workshops or posts demonstrating the tool during the support window
Monthly smoke tests on Nitro dev node and one public provider
Minor releases and changelog updates
Issue triage within seventy two hours
Coverage for small operational items such as code signing or CI minutes if needed
Core build duration: 8 weeks Post release support: 6 months
Are milestones clearly defined, time-bound, and measurable with quantitative metrics where applicable? What are your reference KPI, if applicable, for each milestone.
Milestone 1 — Trace ingest and flamegraph
Amount: $9,000 Time: 3 weeks Deliverables:
CLI reads stylusTracer via debug_traceTransaction from the local Nitro dev node or a tracing RPC
SVG flamegraph generator
JSON summary schema with top hot paths and HostIO event counts
Unit tests and cross platform builds KPIs (targets):
Generate flamegraph and JSON for 3 sample transactions with no errors
Publish builds for macOS arm64 and x86_64, Linux x86_64, Windows x86_64 with a green CI matrix
Process a 5 MB sample trace in under ~5 seconds on a typical CI runner
Milestone 2 — Diff engine and CI Action
Amount: $6,000 Time: 2 weeks Deliverables:
Profile diff generator with machine readable report
Threshold policy for HostIO and time based metrics
GitHub Action that starts the Nitro dev node, runs a scripted transaction, captures profiles, uploads artifacts, and fails pull requests on regression
Example workflow and caching guidance KPIs (targets):
Diff engine flags synthetic regressions reliably in test cases
End to end Action run completes in under ~6 minutes on ubuntu latest with example workflow producing a passing and a failing case
Milestone 3 — Static web viewer and source hints
Amount: $6,000 Time: 2 weeks Deliverables:
Single page viewer opens profiles locally with zoom and search
Side by side diff view
Best effort source hints when symbols are present and clear labels when not
Published static bundle and public site KPIs (targets):
Viewer loads a 10 MB profile in a few seconds on a laptop class machine
Basic accessibility and performance checks pass with high scores
Milestone 4 — Docs, starter repo, onboarding, polish
Amount: $3,000 Time: 1 week Deliverables:
Quick start for Nitro dev node and local profiling
CI cookbook and troubleshooting guide
Provider notes about tracing availability
Starter repository with a Stylus contract, a Solidity caller, and a green CI pipeline
Short demo video KPIs (targets):
Time to first profile under ~10 minutes following the Quickstart
Starter repo CI green on first run with artifacts visible
Milestone 5 — Ops and maintenance buffer
Amount: $1,000 Time: 6 months of light maintenance after v1 Deliverables:
Monthly smoke tests on the Nitro dev node and at least one public provider
Minor releases and changelog updates
Issue triage within seventy two hours
Coverage for small operational items such as code signing or CI minutes if needed KPIs (targets):
Monthly smoke tests completed on schedule and logged
At least one patch every two months or as needed based on issues
What is the estimated maximum time for the completion of the project.
3 months
How should the Arbitrum community measure the success of this grant.
Milestone 1 metrics targets

CLI generates an SVG flamegraph and JSON report for at least 3 sample transactions on the Nitro dev node
Cross platform releases published with a green CI build matrix
JSON schema validated in CI
Milestone 2 metrics targets

Diff engine produces deterministic diffs for controlled before and after test cases
GitHub Action demonstrates one passing run and one failing run in the example workflow
Artifacts are uploaded and thresholds are enforced correctly
Milestone 3 metrics targets

Viewer renders local profiles with zoom, search, and side by side diffs
Works in Chrome and Firefox for sample profiles
Fallback labels render correctly when symbols are absent
Milestone 4 metrics targets

Time to first profile under about 10 minutes following the Quickstart
Starter repo CI green and produces artifacts on first run
Education template published
Orbit template example published
Demo video and tutorial post published
Milestone 5 metrics targets

Monthly smoke tests run and logged during the 6 month support window.
Median issue response target within 72 hours and patch releases shipped as needed
Adoption targets 15 public repos using the CLI or Action within 90 days and 30 within 6 months
Community targets 150 stars and 10 forks within 90 days
Impact targets at least 5 external examples of CI gating catching and then fixing a regression within 90 days
Education targets at least 3 workshops or posts during the support window
Delivery and reliability:
v1.0 of CLI, web viewer, and GitHub Action shipped within 8 weeks; sample repo CI green on every push.
Monthly smoke tests for 6 months on Nitro dev node and one public provider; parser remains compatible.
Issues triaged within 72 hours; at least one patch every two months or as needed.
Adoption:
15+ public repos using the Action or CLI in 90 days; 30+ in 6 months.
150+ stars and 10+ forks across main repos in 90 days.
Impact:
≥5 external PRs that the Action fails then pass after fixes within 90 days.
≥3 public before/after flamegraphs or diff posts in 90 days; ≥10 in 6 months.
Developer experience:
Time to first profile ≤10 minutes from a fresh clone (verified in sample repo CI).
Docs pass link checks; one demo video and one tutorial published.
Ecosystem integration:
≥2 provider or community guides referencing the tool within 6 months.
≥1 Orbit or education template using it; ≥3 workshops or posts demonstrating it.
Sustainability:
≥5 external PRs merged within 6 months.
Semantic versioning; no breaking changes without a major bump.
Privacy and cost:
Default workflow uses the local Nitro dev node; no paid RPC required.
Telemetry off by default; none collected unless explicitly enabled.
What is the economic plan for maintaining operations or continuing the growth of your project after the grant period.
Keep costs low:
Host the viewer as a static site (no servers).
Release CLI binaries on GitHub.
Use public CI runners.
Small recurring items (code signing, occasional debug calls) come from a tiny ops budget or sponsors.
How we fund ongoing work:
Open-source sponsors (GitHub Sponsors, OpenCollective).
Microgrants and bounties for add-ons
Optional paid support: onboarding, CI setup, threshold tuning.
Partnerships with node providers for guides and small maintenance co-funding.
How we maintain:
Public roadmap, CONTRIBUTING, semantic versioning, deprecation policy.
Monthly smoke tests on the Nitro dev node and one public provider; results logged.
Triage issues within 72 hours; patch releases as needed.
How we grow:
Add Orbit templates and provider presets.
Ship new summary views that surface HostIO patterns.
Tutorials, sample repos, guest workshops with ecosystem partners.
How the community can tell it’s working:
Adoption rising: more repos using the CLI/Action, more stars and forks.
Maintainer health: fast median response time, quick time-to-patch.
Compatibility stays green each release for the Nitro dev node and at least one public provider.
Market Needs: For developers building apps and interfaces, what specific challenges or opportunities do they face in Arbitrum.
Key challenges:
Cross-VM visibility: Stylus adds a Wasm VM beside EVM. Raw traces exist but rarely give clear HostIO hotspots, latency spikes, or Wasm↔EVM bottlenecks during review.
Local vs mainnet parity: Some Arbitrum behaviors such as precompiles, deposits, retryables, and gas rules are not trivial to simulate in common local stacks.
Observability gaps: Explorers and generic EVM tools often do not expose Stylus-specific signals, making before/after comparisons and CI enforcement harder.
Provider variance: Access to debug_* differs by RPC provider, so teams benefit from a local, provider-agnostic workflow.
Ecosystem maturity: Stylus libraries, examples, and verification paths are still maturing, which slows onboarding for Rust and C developers.
Front-end UX complexity: Bridging, L1↔L2 finality, fees, and account abstraction patterns add interface complexity.
Major opportunities:
Stylus performance and languages: High-speed Rust or C contracts that interoperate with Solidity enable richer DeFi, gaming, and data-heavy apps.
Low fees and high throughput: Better UX for micro-payments, real-time interactions, and consumer apps.
Orbit chains: App-specific L3s create demand for templates, profiling, and standards.
Account abstraction: Smoother onboarding, sponsored transactions, session keys, and recovery.
Ecosystem support: Grants and focused DevRel create room for open-source tools to become common choices.
What developers need to ship faster:
Actionable observability: Visual flamegraphs, diffs, and CI thresholds for Stylus transactions, with local-first workflows.
Reliable local parity: Precompile behavior, deposit and retryable flows, and realistic gas handling without leaving the dev machine.
Battle-tested templates: End-to-end repos combining Solidity, Stylus, and front ends with clear docs and CI.
Provider-agnostic guides: Nitro dev node as the baseline, with notes on when to use tracing from public providers.
Composability: How can your proposed developer tooling (existing or new) facilitate seamless interaction between different protocols and tools built on Arbitrum, the Nova ecosystem and other Orbit chains.
Chain agnostic: Works with the Nitro dev node and any tracing enabled RPC by URL and chain ID. Usable on Arbitrum One, Nova, and Orbit.
Portable artifacts: Emits stable JSON summaries and SVG flamegraphs. Easy to consume in CI, dashboards, explorers, and docs.
Fits existing stacks :Callable from Hardhat, Foundry, or scripts. Includes a reusable GitHub Action for consistent gates across repos.
Cross protocol insight: Captures Wasm to EVM interactions. Before and after diffs verify integrations with DeFi, bridges, oracles, and wallets.
Orbit ready: Accepts custom chain IDs and RPCs. Teams can publish reference profiles and thresholds for app specific L3s.
Easy collaboration: Read only and static outputs. Share in pull requests, issues, and audits; reproduce locally without setup.
Adoption: If your grant focuses on a Growth or Orbit Chain developer tool, how would you ensure its widespread adoption within the developer community.
n/a
If working on Orbit Chain Onboarding and Tooling: How would you adapt your existing developer tooling (or propose a new tool) to simplify building applications on custom Arbitrum Layer 3 chains (Orbit Chains).
n/a
Instagram.
na
If applying for a growth oriented grant: Please provide success metrics for the grant with milestone-oriented disbursements.
n/a
LinkedIn.
na
Discord.
larkim#7130
Others.
<https://t.me/creativesonhainl>
Do you acknowledge that your team will be subject to a KYC requirement.
Yes
Do you acknowledge that, in case of approval, you will have to provide a report at the completion of the grant and, three months later, complete a survey about your experience.
Yes
Team experience and completeness.
Timilehin Olowu – Full Stack Developer & Tooling Engineer Timilehin has over 4 years of experience building secure and scalable applications in fintech, AI, and blockchain. He’s passionate about developer experience and has contributed tools like Methane CLI and Esher to simplify React and AI-powered integrations. LinkedIn: <https://www.linkedin.com/in/timilehin-olowu-9a7832271/> NPM: <https://www.npmjs.com/package/methane-cli>
<https://www.npmjs.com/package/esher>
Michael Afolabi – Infrastructure & Automation Engineer Michael focuses on AI-powered infrastructure and secure distributed systems in blockchain. He built Vault, an AI-powered DeFi yield optimization platform (<https://vault-web-eta.vercel.app>), and Eclipse, a secure interface for interacting with Dimension.xyz (<https://eclipse-eosin-sigma.vercel.app>). He brings deep technical insight into developer automation and backend tooling. LinkedIN: <https://www.linkedin.com/in/ifeoluwa-afolabi-2a8932162?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=android_app> GitHub: <https://github.com/Afoxcute>
Akeem Adelanke – Project manager & Dev Onboarding Specialist: Akeem is a developer onboarding specialist and community builder with extensive experience in user onboarding, crypto marketing, and event coordination. He has collaborated with NEAR Protocol, Creatives DAO, Mintbase, and Arbitrum. As a core contributor to NEAR Creatives DAO, he helped mint over 50,000 NFTs, allocate $2.5 million in creative funding, and support sub-DAOs in 25+ countries. He’s passionate about building systems that empower both developers and creators. Twitter: <https://x.com/youngfreshdao>
Victory Osato – Blockchain Developer Victory is an experienced blockchain developer who has deep knowledge of Arbitrum tech stack and he has helped train over 100 developers and help launch 10 dApps during a recent Arbitrum Stylus workshop. Link: <https://github.com/OsatoVicTory>
