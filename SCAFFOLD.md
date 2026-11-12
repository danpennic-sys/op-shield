# OP-Shield Scaffold

**Status:** Scaffold only. Not demonstrated. Not production.

## Layout

```
op-shield/
├── README.md
├── SCAFFOLD.md
├── docs/
│   └── PAYLOAD-SCHEMA.md
├── evidence/
│   └── sample-payload.json
├── samples/
│   └── platform-demo.json
└── src/
    └── opshield.py
```

## Evidence Path

1. Run `python3 src/opshield.py samples/platform-demo.json` → deterministic payload.
2. Seal payload bytes into CONTRACT-v1 bundle (Ed25519 over SHA-256 hash) using the same pattern as DailySeal / VeriQuote.
3. Verify with existing QuantumGuard: `qg verify sealed-bundle.json` → PASS | FAIL | INDETERMINATE.

Sample inner payload is in `evidence/sample-payload.json`.

## Next

- Wire real package/service discovery (dpkg/apt, systemd, or container inspect).
- Add sealer function that produces a full CONTRACT-v1 JSON.
- Generate signed conformance vectors.
- Align to Navy DON27BZ02-NV010 Phase I statement of work.

Operator Pages LLC — We don’t promise anything we prove it.
