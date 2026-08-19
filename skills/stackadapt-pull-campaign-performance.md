---
name: Pull campaign performance from StackAdapt
description: >-
  Walk an account from advertiser to campaign group to campaign, then read delivery and insight
  records at the right grain, using Relay cursor pagination and the async twin queries for large
  reporting pulls.
api: graphql/stackadapt-schema.graphql
endpoint: https://api.stackadapt.com/graphql
operations:
  - tokenInfo
  - account
  - advertisers
  - campaignGroups
  - campaigns
  - campaign
  - campaignDelivery
  - campaignDeliveryAsync
  - campaignInsight
  - campaignGroupInsight
  - adDelivery
  - adDeliveryAsync
  - ads
generated: '2026-08-13'
method: generated
source: >-
  Every field above verified present on type Query in graphql/stackadapt-schema.graphql
  (introspected 2026-06-14).
---

# Pull campaign performance from StackAdapt

Read-only. This is the flow the hosted MCP server exists to serve, and the one a reporting
integration should implement.

## Authenticate

```
POST https://api.stackadapt.com/graphql
Authorization: Bearer <GraphQL API key>
Content-Type: application/json
```

The GraphQL key is **not** the REST v2 key. Confirm your credential before anything else:

```graphql
query { tokenInfo { __typename } }
```

Without a token every request — including introspection — returns `401`:

```json
{"errors":[{"message":"Schema introspection requires authentication.","extensions":{"traceId":"..."}}]}
```

## Walk the hierarchy

`Advertiser -> CampaignGroup -> Campaign -> Ad`.

```graphql
query Advertisers($first: Int!, $after: String) {
  advertisers(first: $first, after: $after) {
    totalCount
    nodes { id name isArchived }
    pageInfo { hasNextPage endCursor }
  }
}
```

Then `campaignGroups`, then `campaigns`, then `ads` — each is a Relay connection taking
`first` / `after` / `last` / `before`.

## Pagination rules

- Cursors are **opaque**. There is no page number and no offset.
- Loop on `pageInfo.hasNextPage`, feeding `pageInfo.endCursor` into `after`.
- `totalCount` is exposed on 116 of the 102 connection families — use it to size a job **before**
  walking it rather than counting as you go.

## Campaign is an interface

`Campaign` and `Ad` are **interfaces**, not concrete types. The concrete type is the channel
(native, display, video, CTV, audio, DOOH, programmatic linear TV). To read channel-specific
fields you must use inline fragments:

```graphql
campaigns(first: 50) {
  nodes {
    __typename
    id
    campaignStatus
    channelType
    campaignGroup { id name }
    advertiser { id name }
  }
}
```

`channelType` is the human-readable string; `__typename` is the value to branch on. A generated
client that assumes flat objects will silently drop channel fields.

## Delivery vs insight

- `campaignDelivery` / `adDelivery` / `advertiserDelivery` — delivery records (impressions,
  spend, pacing) at three grains.
- `campaignInsight` / `campaignGroupInsight` — insight records.
- `conversionPath`, `audienceInsights`, `footfallInsight`, `brandLiftStudies` — measurement.

## Use the async twins for big pulls

Every delivery query has an `*Async` sibling: `campaignDeliveryAsync`, `adDeliveryAsync`,
`advertiserDeliveryAsync`. For any pull spanning long date ranges or many entities, schedule the
async form and poll, rather than holding a synchronous request open. The same schedule-then-poll
shape governs `scheduleAudienceInsights`, `scheduleContextualTargeting`, `scheduleForecast` and
`scheduleTopicSuggestion`.

## Error handling

- HTTP status is meaningful — StackAdapt returns real `401`/`400`, not `200`-plus-errors.
- On error read `errors[].extensions.traceId` (a 32-hex string) and quote it to support.
- `429` means rate-limited. **No `Retry-After` or `RateLimit-*` header is returned and no
  numeric threshold is published**, so choose your own backoff — exponential, starting at a few
  seconds.

## Money and time

`MoneyValue` is a decimal **string** (`"100.57"`) and `BigInt` is an integer **string**. Parse
them as decimals/bigints, never as JavaScript numbers. Dates are `ISO8601Date` /
`ISO8601DateTime`; campaign groups carry their own `timezone`, so align reporting windows to it.
