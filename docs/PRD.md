# Chronicle Queue product requirements

Chronicle Queue is a small self-hosted task scheduling service for applications that cannot depend on a managed queue. A single deployment exposes a JSON HTTP API to producers and a long-poll worker protocol, persists all state in SQLite, and ships a Python client that hides wire details from application code.

Producers can enqueue immediate or delayed jobs with a queue name, priority, JSON payload, idempotency key, and dependencies on earlier jobs. A worker claims work under a time-limited lease, then acknowledges success or reports failure. Failed jobs may be retried with a delay. Expired leases become available again without allowing an old worker to overwrite a newer result. A process restart must retain enough information to continue safely.

The service must support concurrent producer and worker requests. Claiming work and changing a lease are atomic across threads and processes. API errors use stable machine-readable codes. Producer requests authenticated with an HMAC signature must reject stale timestamps and replayed nonces. Health and queue statistics are readable without exposing job payloads.

The Python client covers enqueue, query, claim, acknowledgement, failure, lease extension, and cancellation. Its public exceptions map protocol error codes without leaking transport implementation details. The server, client, schema migration, and core state engine need distinct responsibilities so later protocol versions can evolve without replacing the database.

Runtime code uses only the Python standard library. Tests must be deterministic and exercise HTTP behavior, persistence and restart, lease races, authentication replay protection, dependency transitions, delayed jobs, and client/server compatibility. Time-dependent logic must accept an injected clock.

