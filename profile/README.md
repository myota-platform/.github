# MyOTA

MyOTA is a programme-agnostic Outdoor Activation Platform for amateur-radio initiatives. It provides reusable identity, programme configuration, geospatial data, activation, QSO, award, approval, and map capabilities. Each programme defines its own charter, eligibility, rules, awards, minimum QSOs, jurisdictions, themes, and content; MyOTA does not copy or inherit rules from POTA or another programme.

The organization login is `myota-platform`; the visible organization name is **MyOTA**.

## Platform at a glance

```text
Universal programme UI
        │
API gateway / ingress
        ├── Identity service       accounts, callsigns, SWLs, scopes, OIDC mappings
        ├── Programme service      programmes, entity types, rules, awards, themes
        ├── Geodata service        PostGIS, imports, provenance, conflation, review
        └── Activity service       activations, QSOs, award evaluation primitives
                 │
        PostgreSQL + PostGIS / event outbox
```

The geodata lifecycle is:

```text
authoritative/imported source → CANDIDATE → PROPOSED → APPROVED
```

Approved entities are public programme references. Candidates remain visibly distinct until reviewed by an approver with the correct programme, jurisdiction, and entity-type scope.

## Repositories

| Repository | Responsibility |
|---|---|
| [`myota-platform`](https://github.com/myota-platform/myota-platform) | Runnable integration bootstrap, dependency-free vertical slice, local gateway, tests, API contracts, migrations, and cross-service smoke path. |
| [`myota-contracts`](https://github.com/myota-platform/myota-contracts) | OpenAPI HTTP contracts, event envelopes, compatibility rules, and future generated clients. |
| [`myota-identity-service`](https://github.com/myota-platform/myota-identity-service) | Amateur-radio-native accounts, operator/SWL participation, multiple callsigns, primary callsign, verification lifecycle, roles, scopes, and optional external identity mappings. |
| [`myota-programme-service`](https://github.com/myota-platform/myota-programme-service) | Programme configuration, programme-owned entity types, rules, awards, jurisdictions, themes, and optional per-programme OIDC settings. |
| [`myota-geodata-service`](https://github.com/myota-platform/myota-geodata-service) | PostGIS entities, source provenance, import runs, ParkServe/OSM/government/manual adapter contracts, deduplication/conflation, candidate review, and approval workflow. |
| [`myota-activity-service`](https://github.com/myota-platform/myota-activity-service) | Activations, QSO primitives, idempotent ingestion, audit events, and programme-specific award evaluation inputs. |
| [`myota-web`](https://github.com/myota-platform/myota-web) | Universal, programme-themed browser experience with approved/candidate map distinction and programme switching. |
| [`myota-deploy`](https://github.com/myota-platform/myota-deploy) | PostgreSQL/PostGIS bootstrap, Docker Compose manifests, Helm chart, service routing, health probes, and deployment configuration. |
| [`myota-docs`](https://github.com/myota-platform/myota-docs) | Architecture, ADRs, storage-topology decision, QGIS workflow, threat notes, migration strategy, source inspection, and repository map. |

## Storage and geodata

The initial production topology is one PostgreSQL cluster with two databases:

- `myota_core`: identity, programme, activity, awards, permissions, audit, and outbox data.
- `myota_geo`: PostGIS geometry, imported snapshots, source references, conflation candidates, and review records.

This keeps operations simple while giving geodata an independent backup, scaling, and eventual cluster-split path. QGIS is the recommended graphical PostGIS tool for geometry inspection and controlled editing; lifecycle transitions remain API-owned and audited.

Supported adapter designs include ParkServe US, OpenStreetMap tags (`leisure=park`, `leisure=nature_reserve`, `boundary=protected_area`, and `landuse=recreation_ground`), local-government GIS feeds, and manual proposals. Imports preserve source IDs, licensing, attribution, retrieval time, geometry, and refresh semantics.

## Local development

```bash
cd /Users/Volker_Kerkhoff/Documents/Projects/MyOTA/myota-platform
python3 -m unittest discover -s tests -v
python3 services/dev_server.py
```

Open <http://127.0.0.1:8080>. This starts the gateway and the four service boundaries without third-party Python packages. For the containerized PostGIS path, start Colima and use the Compose manifest in [`myota-deploy`](https://github.com/myota-platform/myota-deploy).

## Design principles

- API-first and programme-agnostic.
- Explicit service/data ownership; services communicate through APIs and versioned events.
- No Keycloak dependency.
- No copied POTA rules or charter language.
- PostgreSQL/PostGIS as the geospatial source of truth.
- Idempotent mutations, auditability, scoped approvals, health checks, and observable deployment boundaries.
- MPOTA is retained only as synthetic sample data; it is not the platform definition.
