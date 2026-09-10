# HTTP protocol constraints

The wire format is UTF-8 JSON. Successful responses contain a `data` object. Errors contain an `error` object with `code` and `message`; clients branch on `code`. Unknown request fields are rejected so protocol mistakes are visible.

Producer mutations include `X-Chronicle-Timestamp`, `X-Chronicle-Nonce`, and `X-Chronicle-Signature`. The signature covers method, path, timestamp, nonce, and the SHA-256 digest of the exact request body. A nonce is single-use within the accepted timestamp window, including across a service restart.

Worker claim requests may wait briefly for eligible work. Each successful claim returns a task id, immutable lease token, lease expiry, attempt number, and payload. Mutations of a claimed task must present both its task id and lease token. Tokens are opaque and must change after every new claim.

The first implementation may choose concrete paths and status codes, but the server and bundled Python client must agree. Database schema versions are explicit and opening a newer unsupported schema fails safely.

