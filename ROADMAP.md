# Roadmap

Work lives in **[issues](https://github.com/tenantlayer-io/tenantlayer/issues)**, grouped by
milestone. This file is the context around them; the issues are the actual list.

| Milestone | Theme | Issues |
|---|---|---|
| **[v0.2](https://github.com/tenantlayer-io/tenantlayer/milestone/1)** | Schema-per-tenant routing, reactive and batch propagation, registry lifecycle, migrations, caching | 16 |
| **[v0.3](https://github.com/tenantlayer-io/tenantlayer/milestone/2)** | Database-per-tenant routing and the configuration-driven strategy switch | 4 |
| **[v0.4](https://github.com/tenantlayer-io/tenantlayer/milestone/3)** | Isolation checker, tenant-aware health, the read-only dashboard | 7 |

**[Good first issues](https://github.com/tenantlayer-io/tenantlayer/labels/good%20first%20issue)**
are small and self-contained. **[Help wanted](https://github.com/tenantlayer-io/tenantlayer/labels/help%20wanted)**
is everything unclaimed, which is currently all of it.

Comment on an issue before you start, so two people don't build the same thing.

## Shipped

**[0.4.0](https://github.com/tenantlayer-io/tenantlayer/releases/tag/v0.4.0)** — the control
plane seams: onboarding a tenant in one call, provisioning hooks that run with the new
tenant bound, `/actuator/tenants` for driving the registry over HTTP, a start-up isolation
checker that reports the tables Postgres is not actually protecting, and a per-tenant
metrics tag with a cardinality cap.

**[0.3.0](https://github.com/tenantlayer-io/tenantlayer/releases/tag/v0.3.0)** —
database-per-tenant routing, per-tenant migrations, and the registry taken off the
tenant-aware datasource it was routing through.

**[0.2.0](https://github.com/tenantlayer-io/tenantlayer/releases/tag/v0.2.0)** —
tenant-scoped cache keys and per-tenant eviction, `TenantConnectionStrategy` as the seam
that decides how a connection is obtained, and schema-per-tenant.

**[0.1.0](https://github.com/tenantlayer-io/tenantlayer/releases/tag/v0.1.0)** — resolution
from header, subdomain, path and JWT claim; membership verification; leak-proof RLS wiring
and policy generation; the Hibernate discriminator; propagation across `@Async`,
`CompletableFuture`, virtual threads, scheduled jobs, outbound HTTP and Kafka; the tenant
registry; and the testing kit.

Every release is in the [changelog](CHANGELOG.md), with the upgrade notes.

## What the milestones are actually for

The first four releases each had a job, and they went in this order for a reason.

**0.2 was breadth**, and it closed the hole that mattered most: a cache hit never consults
the database, so row-level security cannot help it. Caching had to be tenant-scoped before
anything else was worth building on top.

**0.3 was the one that mattered.** Database-per-tenant routing and the strategy switch
together fixed the shape of the isolation abstraction, and everything else hangs off it.
That is why `1.0` was always going to come after it rather than before.

**0.4 is what you want once it is actually running** — knowing a policy is missing before a
customer finds out, driving a tenant's whole lifecycle from your own signup path, and being
able to see per-tenant latency without an unbounded metrics bill.

**What is left before 1.0** is stability rather than surface: the API has to sit still
across a few releases, and the remaining [open issues](https://github.com/tenantlayer-io/tenantlayer/issues)
are additive — more resolver presets, suspend/activate semantics, Liquibase alongside
Flyway, a tenant-aware health endpoint. None of them should break anyone.

## What is not on this list

TenantLayer is open core. The roadmap above is the **free core**, and the rule that decides
what belongs here is published in [CONTRIBUTING.md](CONTRIBUTING.md):

> Free is correctness. Paid is operations, compliance and scale.
> Would a two-person startup need it before they have customers? Free.
> Would a team closing their first large enterprise deal need it? Paid.

The free core is never crippled to sell the paid one. If something is needed to build
tenancy *correctly* and it is not here, that is an omission worth an issue — say so, and
the rule is what the argument gets held up against.

Deliberately out of scope entirely: cross-region replication, active-active, and
failover/DR orchestration. Those belong to the database platform (Aurora Global, Azure
geo-replication, Cloud SQL). TenantLayer stays region-aware — routing, placement, policy —
and never becomes a replication control plane.

## Versioning

`0.x` permits breaking changes between minor versions, because the isolation abstraction is
not finished. Every break gets a [changelog](CHANGELOG.md) entry with a migration note. The
API stabilises at `1.0`, once all three isolation strategies exist and have stress-tested it
between them.
