---
name: Build StackAdapt audiences, pixels and event subscriptions
description: >-
  Create retargeting, conversion and lookalike pixels, build custom segments (including the
  explicit PII variants), upload CDP profiles, and subscribe to the three webhook events
  StackAdapt emits.
api: graphql/stackadapt-schema.graphql
endpoint: https://api.stackadapt.com/graphql
operations:
  - createConversionPixel
  - createLookalikePixel
  - createRtSegment
  - createCrmSegment
  - createCrmSegmentWithPii
  - createIpCustomSegment
  - createLocationBasedCustomSegment
  - createAbmAudience
  - createNpiCustomSegment
  - createTopicAudienceCustomSegment
  - createProfileList
  - upsertProfiles
  - customSegments
  - audiences
  - createWebhook
  - updateWebhook
  - deleteWebhook
  - webhooks
generated: '2026-08-13'
method: generated
source: >-
  Every field above verified present on type Mutation or type Query in
  graphql/stackadapt-schema.graphql (introspected 2026-06-14). Webhook event names read from
  the WebhookEvent enum in the same file.
---

# Build StackAdapt audiences, pixels and event subscriptions

## Pixels

- `createConversionPixel` — conversion tracking. Fires as `conv` against
  `https://tags.srv.stackadapt.com/` with `cid=<pixel id>`.
- `createLookalikePixel` — lookalike modelling. Fires as `lal` with `sid=`.
- Retargeting fires as `rt` / `drt` with `sid=`; the universal pixel fires as `saq_pxl` with
  `uid=`.

`deleteConversionPixel` and `updateConversionPixel` complete the pixel lifecycle.

**Passbacks.** `Pixel.passbacks` returns a `PixelPassbackConnection` and `Pixel.passbackValues`
returns JSON. Passbacks are macro key/value pairs (`PixelPassbackInput`) returned when the pixel
fires — a server-to-server callback distinct from webhooks.

**Server-side firing.** If you are not installing the JS pixel, StackAdapt ships first-party
Google Tag Manager templates (web and server container) that call the same endpoint. See
`components/stackadapt-components.yml`.

## Segments — the PII split is in the operation name

Several segment builders exist in **two variants**, and the caller declares intent by choosing
one:

| Plain | PII variant |
|---|---|
| `createCrmSegment` | `createCrmSegmentWithPii` |
| `createIpCustomSegment` | `createIpCustomSegmentWithPii` |
| `createDeviceCustomSegment` | `createDeviceCustomSegmentWithPii` |

Use the `WithPii` form **only** when you are knowingly uploading personal data, and check the
advertiser's Data Processing Addendum obligations first
(`https://www.stackadapt.com/legal-document-centre/data-processing-addendum`). For an
autonomous agent, treat any `WithPii` mutation as human-in-the-loop.

Other builders: `createRtSegment`, `createLocationBasedCustomSegment`,
`createIntersectionCustomSegment`, `createCombinedAudienceCustomSegment`,
`createIspCustomSegment`, `createAbmAudience` / `createAbmAudienceWithDomainsList`,
`createNpiCustomSegment` (healthcare NPI), `createTopicAudienceCustomSegment`.

Read back with `customSegments` and `audiences`.

## Profiles

`createProfileList` then `upsertProfiles` (or `createUserUploadedProfiles` /
`createUserUploadedProfilesWithJson`). `upsertProfiles` is idempotent by external id, and
`deleteProfilesWithExternalIds` deletes by the same caller-owned key — the only place on this
API where a caller controls the identifier. Uploads are asynchronous; poll
`profileMappingStatus`, and treat `UPLOAD_ERROR` as a terminal failure state.

## Webhooks

StackAdapt emits exactly **three** events:

| Event | Meaning |
|---|---|
| `CAMPAIGN_CREATION` | Campaign created |
| `CAMPAIGN_UPDATES` | Campaign updated |
| `CREATIVE_CREATION` | Creative created |

Subscribe:

```graphql
mutation {
  createWebhook(input: {
    name: "campaign-updates",
    event: CAMPAIGN_UPDATES,
    url: "https://example.com/hooks/stackadapt",
    active: true
  }) {
    userErrors { message path }
  }
}
```

The `url` **must** start with `https://` — the schema says so explicitly. Manage with
`webhooks` (a Relay connection with `totalCount`), `updateWebhook` and `deleteWebhook`.

**What is not published:** no payload schema, no signing or verification scheme, no retry
policy, no delivery-ordering guarantee, no replay endpoint. Build your receiver defensively —
verify state by calling back into the API rather than trusting the body, and make the handler
idempotent, because you cannot assume at-most-once delivery.

**What is not covered:** there is no delivery, spend, budget-pacing, conversion or
creative-approval event. An agent that needs to react to those must poll the reporting queries
(see `skills/stackadapt-pull-campaign-performance.md`).

## Every mutation

Check `payload.userErrors` before treating the call as successful. See
`conventions/stackadapt-conventions.yml`.
