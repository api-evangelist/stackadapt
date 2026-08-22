# StackAdapt (stackadapt)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

StackAdapt is an AI-powered programmatic advertising platform with REST and GraphQL APIs for managing campaigns, ad groups, creatives, targeting segments, pixel tracking, and performance reporting across native, display, video, connected TV, audio, and digital out-of-home channels.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/stackadapt/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/stackadapt/refs/heads/main/apis.yml)

## Tags

- Programmatic Advertising
- Digital Advertising
- Campaign Management
- Ad Tech
- DSP
- Demand-Side Platform
- Native Advertising
- Display Advertising
- Video Advertising
- Connected TV
- Audience Targeting
- Real-Time Bidding
- Conversion Tracking
- Performance Reporting

## Timestamps

- **Created:** 2026-06-13
- **Modified:** 2026-06-13

## APIs

### StackAdapt REST API

Read-only REST API for fetching reporting data across dimensions and metrics to analyze campaign performance. Write operations are deprecated; use the GraphQL API for write operations.

- **Human URL:** [https://docs.stackadapt.com/](https://docs.stackadapt.com/)
- **Base URL:** `https://api.stackadapt.com/service/v2`

#### Tags

- Reporting
- Campaigns
- Performance Data
- Analytics

#### Properties

- [Documentation](https://docs.stackadapt.com/)
- [Authentication](https://docs.stackadapt.com/)

### StackAdapt GraphQL API

Full-featured GraphQL API for creating and managing programmatic advertising campaigns, ad groups, creatives, targeting segments, pixel tracking, and performance reporting. The primary API for write operations on the StackAdapt platform.

- **Human URL:** [https://docs.stackadapt.com/](https://docs.stackadapt.com/)
- **Base URL:** `https://api.stackadapt.com/graphql`

#### Tags

- GraphQL
- Campaigns
- Ad Groups
- Creatives
- Targeting
- Segments
- Reporting

#### Properties

- [Documentation](https://docs.stackadapt.com/)
- [S D Ks](https://www.npmjs.com/package/@stackadapt/pa-typescript-sdk)
- [Graph Q L](graphql/stackadapt-graphql.md)

### StackAdapt Pixel API

Server-to-server API for conversion tracking and audience generation without requiring website pixel installation.

- **Human URL:** [https://docs.stackadapt.com/](https://docs.stackadapt.com/)
- **Base URL:** `https://pixel.stackadapt.com`

#### Tags

- Pixel
- Conversion Tracking
- Audiences
- Server-to-Server

#### Properties

- [Documentation](https://docs.stackadapt.com/)

### StackAdapt Data Taxonomy API

API for secure data sharing and audience synchronization with third-party platforms and data partners.

- **Human URL:** [https://docs.stackadapt.com/](https://docs.stackadapt.com/)
- **Base URL:** `https://api.stackadapt.com`

#### Tags

- Data Sharing
- Audience Sync
- Data Taxonomy
- Third-Party Integration

#### Properties

- [Documentation](https://docs.stackadapt.com/)

### StackAdapt MCP Server

Model Context Protocol server enabling AI agents (Claude, ChatGPT) to interact with the StackAdapt platform programmatically via the GraphQL API.

- **Human URL:** [https://docs.stackadapt.com/](https://docs.stackadapt.com/)
- **Base URL:** `https://api.stackadapt.com`

#### Tags

- MCP
- AI Agents
- Automation
- Claude
- ChatGPT

#### Properties

- [Documentation](https://docs.stackadapt.com/)

## Common Properties

- [Website](https://www.stackadapt.com)
- [Documentation](https://docs.stackadapt.com/)
- [Git Hub Org](https://github.com/StackAdapt)
- [LinkedIn](https://www.linkedin.com/company/stackadapt)
- [Blog](https://www.stackadapt.com/resources/blog)
- [Pricing](https://www.stackadapt.com/plans-and-packages)
- [X (Twitter)](https://x.com/stackadapt)
- [Plans](plans/stackadapt-plans-pricing.yml)
- [Rate Limits](rate-limits/stackadapt-rate-limits.yml)
- [Fin Ops](finops/stackadapt-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
