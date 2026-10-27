# Engineering decisions

[← Tiholist overview](../README.md)

## 1. Preserve the input behind every translation

An upstream chapter can be refreshed after it has already been translated. Replacing one mutable text field would make it difficult to explain which input produced the published result.

Sources owns canonical source revisions. Translator retains an input snapshot, and each translation revision refers to the exact snapshot used. Unchanged normalized content can reuse revision identity; changed input makes an older translation stale. The previous translation remains available as history.

Original publication is also useful on its own. It requires saved input and an explicit publication request, without requiring an LLM configuration or a completed translation.

**Trade-off:** retained revisions need more storage and more careful state projections. That cost buys traceability and allows acquisition, processing, and publication to evolve independently.

## 2. Recover publication after an ambiguous network result

Consider this sequence:

1. A worker sends a chapter revision to reader.
2. Reader saves the version.
3. The response is lost before the worker records success.
4. The job is retried.

The publication record pins the selected revision and destination generation. Reader receives a stable version key, allowing a retry to update or reconcile the same logical version. A persisted request timestamp prevents an older delivery from replacing a newer same-key result.

Already recorded success also requires care. Reader content may later be removed. Reconciliation checks whether the specific reader version still exists. A confirmed absence can requeue the same immutable publication; an unavailable reader leaves the outcome unresolved and raises an error.

Existing hidden or manually edited versions are treated as retained data. Reconciliation leaves them intact. Destination changes are versioned and revalidated so stale work cannot silently publish to a newly selected book.

**Trade-off:** there is no distributed transaction across the services. Persistent intent, idempotent writes, and reconciliation handle the gap. These mechanisms require tests for partial success, stale observations, and concurrent changes.

## 3. Give services explicit ownership

| Boundary | Owns |
| --- | --- |
| Sources | Acquisition configuration, source revisions, fetch jobs, and schedules |
| Translator | Pinned inputs, processing revisions, glossary state, and publication orchestration |
| Reader API | Public catalog, reader accounts, chapter versions, and reading state |
| Admin BFF | Operator UI and authenticated calls to the owning services; no application database |
| Reader BFF | Same-origin reader API access and browser session boundary |

Sources and translator do not query reader tables. Separately owned PostgreSQL data is exchanged through authenticated APIs. Redis provides queue infrastructure, while each service retains its own domain state.

The monorepo simplifies coordinated source changes and CI. Applications still have individual dependencies, lockfiles, Dockerfiles, migrations, and builds.

**Trade-off:** this separation requires several runtime components, versioned contracts, and coordinated recovery. It is a deliberate cost for independent acquisition and processing workflows; it is not a claim that every small application needs this topology.

## 4. Keep credentials behind the browser boundary

The Nuxt reader BFF receives the backend session token, sets an HttpOnly cookie, and returns the user response to the browser. Admin uses server-side credentials when calling the internal service APIs. Operator mutations also enforce request-origin and CSRF requirements at the relevant boundaries.

Acquisition validates outbound destinations, including resolved IP addresses, to reject private or loopback targets by default. Image handling and request parsing also have explicit bounds.

These are specific defenses implemented in the application. A full security assessment includes the deployment and every request path as well.

## 5. Make recovery and discovery explicit

Backup tooling covers the application databases and stored media, with isolated restore verification and integrity checks. The offsite workflow encrypts content and filenames before upload, verifies downloaded bytes, and records a successful receipt only after verification. A failed upload retains the local snapshot for recovery or retry.

Public discovery uses the same principle of preserving meaningful failure states. If sitemap metadata is unavailable, the endpoint returns a temporary failure instead of a successful empty sitemap. Public projections include published content and avoid changing reader counters or reading progress.

SEO uses one configured canonical origin while normal reader navigation stays same-origin. This supports the move from LibNode to Tiholist without renaming database identities, queues, or existing accounts just to change the public brand.
