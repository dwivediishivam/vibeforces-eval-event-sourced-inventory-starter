# Event-Sourced Inventory — Spec

Build a TypeScript (Node) service:
1. Commands: ReserveStock, ReleaseStock, CommitSale, RestockItem -> events table
   `events(stream_id, version, type, payload, created_at)` with UNIQUE(stream_id, version)
2. Materialized view `inventory_view(sku, available, reserved)` updated by projector
3. Optimistic concurrency: stale `expected_version` -> 409
4. Snapshotting every 1000 events per stream
5. POST /admin/replay drops view, rebuilds from events + snapshots, must finish <60s for 1M events
6. Concurrency test: 200 concurrent ReserveStock for stock=10 -> exactly 10 ok, 190 409
