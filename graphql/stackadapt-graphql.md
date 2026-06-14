# StackAdapt GraphQL API

StackAdapt is an AI-powered programmatic advertising platform (DSP) that provides a full-featured GraphQL API for creating and managing digital advertising campaigns across native, display, video, connected TV (CTV), audio, and digital out-of-home (DOOH) channels. The GraphQL API is the primary interface for write operations and supports campaign management, creative management, audience targeting, pixel tracking, conversion attribution, and performance reporting.

**Endpoint:** https://api.stackadapt.com/graphql

**Authentication:** Token-based via `X-Authorization` header

**Documentation:** https://docs.stackadapt.com/reference/graphql-api

## References

- Documentation: https://docs.stackadapt.com/reference/graphql-api
- GettingStarted: https://docs.stackadapt.com/
- SDK (TypeScript): https://www.npmjs.com/package/@stackadapt/pa-typescript-sdk
- GitHub: https://github.com/StackAdapt

## Key Capabilities

- **Campaign Management** — Create, update, archive, pause, resume, and copy campaigns and campaign groups across all ad channels (native, display, video, CTV, audio, DOOH, programmatic linear TV)
- **Creative Management** — Upload and manage image, native, video, audio, CTV, and DOOH creatives; manage ad tags and VAST trackers
- **Audience Targeting** — Build custom segments (CRM, IP, device, location-based, lookalike, ABM, B2B, contextual, keyword, topic audience, NPI); manage third-party and catalogue segments
- **Geo Targeting** — City, DMA, zip code, radius, and polygon-based geographic targeting with global zip support
- **Pixel and Conversion Tracking** — Create and manage retargeting pixels, conversion pixels, lookalike pixels, pixel rules, and pixel passbacks
- **Reporting** — Campaign delivery records, insight records, brand lift studies, footfall tracking, conversion path analysis, audience insights
- **Forecasting** — Campaign reach and budget forecasting with channel guidance and impression dropoff projections
- **Deal Management** — Programmatic deal groups and BYOD (Bring Your Own Deal) deal management

## Schema Summary

- **1,147 total types** (excluding built-ins)
- **707 object types** including Campaign, CampaignGroup, Advertiser, LineItem (Ad), Creative variants, Segment variants, Pixel variants, Goal, Flight, GeoTargeting, Audience
- **251 input types** for mutations
- **141 enum types** for status, channel, targeting, and configuration values
- **18 interface types** for polymorphic entities
- **15 union types**
- **15 scalar types** including ISO8601Date, ISO8601DateTime, MoneyValue, BigInt
