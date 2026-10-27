# Verification and public code

[← Tiholist overview](../README.md)

## Local verification record

Date: **October 27, 2026**. Runtime: **Node.js 24.21.0**. Application snapshot: **`e6e9de3`** from the current private monorepo.

| Application | Command after dependency preparation | Test files | Tests | Result |
| --- | --- | ---: | ---: | --- |
| Reader web | `npm test` | 39 | 255 | Passed |
| Translator | `npm test` | 65 | 926 | Passed |
| Sources | `npm test` | 21 | 152 | Passed |

Preparation used the committed lockfiles: `npm ci --ignore-scripts` for translator and sources, followed by `npm run prisma:generate`; reader web used `npm ci --legacy-peer-deps --ignore-scripts`, followed by `npx nuxt prepare`.

These suites exercise unit, component, and mocked route/adapter behavior. Infrastructure-dependent suites are separate. Test counts include parameterized cases and should be read alongside the scenarios, not as a measure of coverage or system capacity.

### Separately executable acceptance workflows

The current monorepo includes targets for:

- Original publication and migration acceptance across services.
- Queue pause, retry, resume, and recovery with disposable PostgreSQL and Redis.
- Authenticated admin and reader browser workflows.
- Database/media backup and isolated restore verification.

Those workflows were outside the local run recorded above. Current main-branch CI and a development-branch local run describe different snapshots; this case does not replace their individual results.

## Public code to inspect

These links point to **earlier public LibNode snapshots**. They provide inspectable implementation examples with stable commit references. Current Tiholist behavior is described in the case study and can be covered in a walkthrough of the private source.

| Concern | Implementation or check |
| --- | --- |
| Password changes revoke previously issued tokens | [.NET AuthService and security stamp](https://github.com/MadiTurkmenbay/libnode-backend/blob/1e8047c0181263161165331b86cb57d184c55d3c/Services/AuthService.cs) |
| Browser login keeps the backend token server-side | [Nuxt BFF login handler](https://github.com/MadiTurkmenbay/libnode-frontend/blob/3412dab4e93842a8e59eec1b0ec3fe1aba75aafe/server/api/auth/login.post.ts) |
| Concurrent first writes preserve database uniqueness | [PostgreSQL concurrency integration tests](https://github.com/MadiTurkmenbay/libnode-backend/blob/1e8047c0181263161165331b86cb57d184c55d3c/LibNode.Api.Tests/Integration/QuestConcurrencyTests.cs) |
| Book identity survives SSR hydration | [Reader regression tests](https://github.com/MadiTurkmenbay/libnode-frontend/blob/3412dab4e93842a8e59eec1b0ec3fe1aba75aafe/tests/book-detail-identity.test.ts) |
| Acceptance uses a disposable environment | [Reader browser acceptance runner](https://github.com/MadiTurkmenbay/libnode-deployer/blob/5a743adecc5594e7a1dfed5281a37ffb85b4dbfc/scripts/test-reader-e2e.sh) |

The concurrency tests check specific uniqueness and progress outcomes. They do not constitute a general proof of exactly-once processing or production-scale behavior.

## Public production checks

On October 27, 2026:

- The [public reader](https://tiholist.app) returned HTTP 200 over HTTPS.
- The initial HTML contained a canonical URL for `https://tiholist.app/`, Open Graph metadata, and JSON-LD.
- [robots.txt](https://tiholist.app/robots.txt) and the [sitemap index](https://tiholist.app/sitemap.xml) returned HTTP 200.
- The screenshot in this case was captured from the public, signed-out homepage.

These are point-in-time availability and metadata checks. Search Console data, traffic, revenue, uptime history, and load-test results are outside this record.
