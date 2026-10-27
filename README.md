# Madi Turkmenbay

Full-stack developer building and operating web products.

I am the sole developer of **[Tiholist](https://tiholist.app)**, a live reading platform with a content acquisition and translation pipeline. I work across the reader experience, backend APIs, background jobs, deployment, and recovery tooling.

**TypeScript · Vue / Nuxt · C# / .NET · PostgreSQL · Redis · Docker**

## Featured project: Tiholist

**[Try the live product](https://tiholist.app) · [Read the engineering case study](https://github.com/MadiTurkmenbay/MadiTurkmenbay/tree/main/case-studies/tiholist)**

- **Reader experience:** catalog discovery, reading progress, shelves, bookmarks, quotes, and selectable text versions.
- **Content workflow:** independently saved source revisions, translation history, glossary management, and explicit publication.
- **Reliability:** idempotent delivery, protection for manual edits, recoverable jobs, and backup verification.
- **Discoverability:** server-rendered pages, canonical URLs, structured metadata, and a paginated sitemap.

The case study explains the decisions, their trade-offs, and the checks behind them. Tiholist was previously called **LibNode**; the public repositories below contain earlier development snapshots. The current application source is private and can be discussed in a technical walkthrough.

## Public code

| Repository | What to inspect |
| --- | --- |
| [libnode-backend](https://github.com/MadiTurkmenbay/libnode-backend) | .NET APIs, authentication, PostgreSQL persistence, and concurrency tests |
| [libnode-frontend](https://github.com/MadiTurkmenbay/libnode-frontend) | Nuxt reader, server-side auth boundary, SSR state, and regression tests |
| [libnode-deployer](https://github.com/MadiTurkmenbay/libnode-deployer) | Docker Compose and disposable acceptance environments |
| [lunch-together](https://github.com/MadiTurkmenbay/lunch-together) | Office lunch ordering with PostgreSQL, supplier workflows, and integration tests |
