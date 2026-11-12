# OP-Shield Payload Schema (v0.1 draft)

Inner payload for QuantumGuard CONTRACT-v1 evidence bundles.

The payload is canonical JSON (deterministic key order, no whitespace) before base64 encoding into the bundle.

## Structure

```json
{
  "schema": "op-shield/v0.1",
  "platform_id": "string",
  "analysis_id": "string",
  "timestamp_utc": "ISO-8601 (informational only; not used by verifier)",
  "input_hash": "hex SHA-256 of analyzed inputs",
  "findings": {
    "required_packages": ["list"],
    "removed_packages": ["list"],
    "disabled_services": ["list"],
    "config_changes": [
      {"path": "string", "action": "string", "before_hash": "hex", "after_hash": "hex"}
    ],
    "risk_score": 0
  },
  "recommendation_hash": "hex SHA-256 of the hardened config artifact",
  "operator_notes": "string"
}
```

## Rules

- All hashes are lowercase hex.
- Lists sorted lexicographically for determinism.
- No wall-clock dependency inside the verifier.
- Compatible with existing `qg verify` (PASS/FAIL/INDETERMINATE).

Status: Draft. Not frozen. Not demonstrated.
