---
name: Launch and control a StackAdapt campaign
description: >-
  Create an advertiser and campaign group, upsert a channel campaign, attach creatives and ads,
  then run the pause / resume / archive / restore lifecycle — with the userErrors trap and the
  missing-idempotency risk handled explicitly.
api: graphql/stackadapt-schema.graphql
endpoint: https://api.stackadapt.com/graphql
operations:
  - createAdvertiser
  - createCampaignGroup
  - upsertCampaign
  - createCreatives
  - upsertAd
  - upsertAdTag
  - pauseCampaigns
  - resumeCampaigns
  - archiveCampaigns
  - restoreCampaigns
  - copyCampaigns
  - campaigns
  - campaignGroups
generated: '2026-08-13'
method: generated
source: >-
  Every field above verified present on type Mutation or type Query in
  graphql/stackadapt-schema.graphql (introspected 2026-06-14).
---

# Launch and control a StackAdapt campaign

Write flow. **Read the two warnings first — they are the difference between a working
integration and duplicate spend.**

## Warning 1 — check `userErrors`, not just `errors`

Every one of the 97 root mutations returns a payload shaped like:

```graphql
type UpsertCampaignPayload {
  campaign: Campaign
  clientMutationId: String
  userErrors: [UserError!]!    # { message: String!, path: [String!] }
}
```

Validation failures come back **inside a `200 OK` payload** as `userErrors`. They do **not**
appear in the top-level `errors` array. A client that checks only `errors` will record a
rejected mutation as a success. After every mutation:

```
if (payload.userErrors.length > 0) -> treat as FAILED, do not retry blindly
```

## Warning 2 — there is no idempotency key

StackAdapt publishes **no** `Idempotency-Key` contract. `clientMutationId` is a Relay
correlation id echoed back on the payload; it does **not** deduplicate.

Consequences for an agent:

- `upsert*` mutations (`upsertCampaign`, `upsertAd`, `upsertAdTag`, `upsertLibraryAd`,
  `upsertProfiles`) converge on the same state when replayed against an existing id — these are
  **safe to retry**.
- `create*` mutations are **not safe to retry**. A timeout on `createCampaignGroup`,
  `createCreatives`, `createAdvertiser` or `createConversionPixel` may already have committed.
  On any ambiguous failure, **query first** (`campaignGroups`, `campaigns`, `ads`) and only
  create if the entity is genuinely absent.

Prefer the `upsert*` form wherever one exists.

## Warning 3 — the environment is the hostname

There is no test/live key prefix. `https://sandbox.stackadapt.com` is sandbox;
`https://api.stackadapt.com` is production. A key alone tells you nothing. Assert the base URL
before any write.

## The flow

1. **Advertiser** — `createAdvertiser`. Or reuse one via `advertisers`.
2. **Campaign group** — `createCampaignGroup`. This is the budgeting container: `budgetType`,
   `budgetAllocation`, `budgetRollover`, `revenueType`, `revenuePricing`, `pacing`, `timezone`,
   `freqCapLimit` / `freqCapExpiry`, and `flights`.
3. **Campaign** — `upsertCampaign(input: CampaignInput!)`. The campaign is channel-typed; the
   concrete type is discriminated by `__typename` and described by `channelType`.
4. **Creatives** — `createCreatives`, then `upsertAd(input: UpsertAdInput!)`. `UpsertAdInput`
   carries one field per channel: `audio`, `ctv`, `display`, `dooh`, `native`, `video`. Populate
   exactly the one matching the campaign's channel.
5. **Ad tags** — `upsertAdTag` for third-party tags and VAST trackers.

## Lifecycle

Verbs are explicit and symmetric, and the plural forms are batch:

| Intent | Mutation |
|---|---|
| Stop delivery | `pauseCampaigns`, `pauseAds`, `pauseCampaignGroup` |
| Resume delivery | `resumeCampaigns`, `resumeAds`, `resumeCampaignGroup` |
| Remove from view | `archiveCampaigns`, `archiveAds`, `archiveAdvertiser`, `archiveCampaignGroup` |
| Undo an archive | `restoreCampaigns`, `restoreAds`, `restoreAdvertiser`, `restoreCampaignGroup` |
| Duplicate | `copyCampaigns` |

`archive` is reversible via `restore` — prefer it to any destructive path.

## Consequence guidance for autonomous agents

`pauseCampaigns` and `resumeCampaigns` move real media spend. Treat resume/upsert on a live
campaign group as **human-in-the-loop**: the operation has an immediate budget consequence, the
OAuth scope (`graphql-public:write`) is account-wide rather than campaign-scoped, and there is
no dry-run mode on this API.

## Verify

Re-query `campaigns` and `ads` after each step and assert `campaignStatus` /
`campaignGroupStatus`. Do not infer success from the absence of a thrown exception.
