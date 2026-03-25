# FeedOracle Trust Layer

**Verifiable evidence & verification MCP server for AI agents.**

Adds a verifiable trust layer so claims can be checked for provenance, freshness, and cryptographic integrity before action.

```
npx -y mcp-remote https://mcp.feedoracle.io/trust/mcp/
```

## The problem

AI agents produce plausible-sounding outputs. But plausible is not verified. When agents make decisions about regulated assets, financial recommendations, or compliance determinations, ungrounded outputs create real risk.

The Trust Layer gives agents a verification gate: **before acting, check the evidence.**

## Tools

| Tool | What it does |
|------|-------------|
| `verify_claim` | Check an agent's claim against FeedOracle signed evidence |
| `get_signed_fact` | Get an ES256K-signed, machine-verifiable fact (JWKS public) |
| `provenance_trace` | Full data chain: external source → FeedOracle → agent delivery |
| `freshness_guard` | Is this data current, stale, or expired? Configurable max age |
| `hallucination_check` | Compare statement against ground truth + red-flag pattern scan |
| `verify_signature` | Verify JWS/DSSE signature from FeedOracle evidence |
| `confidence_gate` | Decision gate: proceed / caution / abstain / escalate |
| `evidence_anchor` | Anchor a fact on-chain (Polygon) as immutable timestamped proof |
| `agent_reputation` | Trust score for an AI agent from KYA (Know Your Agent) system |
| `trust_status` | Infrastructure health and capabilities |

## Standardized verdict schema

Every tool returns this structure:

```json
{
  "status": "partially_verified",
  "confidence": 65,
  "freshness": "current",
  "verification": {
    "signature_valid": true,
    "hash_match": true,
    "registry_entry": true,
    "source_count": 3
  },
  "sources": ["FeedOracle Stablecoin Feed", "CoinGecko", "DeFiLlama"],
  "signature": {
    "algorithm": "ES256K",
    "kid": "feedoracle-mcp-es256k-1",
    "jwks_url": "https://feedoracle.io/.well-known/jwks.json"
  },
  "hash": "sha256:a1b2c3...",
  "provenance": [...],
  "limitations": ["..."],
  "recommended_action": "caution"
}
```

## Four action levels

| Action | When | What agent should do |
|--------|------|---------------------|
| `proceed` | Confidence ≥ 85% | Evidence supports this action |
| `caution` | 60–84% | Add uncertainty disclosure |
| `abstain` | 30–59% | Do not make this claim |
| `escalate` | < 30% | Human review required |

## Example: Hallucination catch

```json
{
  "name": "hallucination_check",
  "arguments": {
    "statement": "USDT is guaranteed risk-free and fully authorized under MiCA"
  }
}
```

→ **Result:** `status: unverified | confidence: 25% | action: escalate`
- Red flag: Absolute language detected ("guaranteed", "risk-free")
- Red flag: Factually suspect claim (USDT is NOT authorized under MiCA)

## Infrastructure

The Trust Layer is backed by FeedOracle's Enterprise Trust infrastructure:

- **ES256K signing** — Every response cryptographically signed (RFC 7515)
- **Evidence Registry** — Append-only transparency log (CT-inspired)
- **KYA (Know Your Agent)** — Agent identity and trust scoring (0–100, 4 levels)
- **On-chain anchoring** — Polygon + XRPL for tamper-proof timestamping
- **JWKS public** — `https://feedoracle.io/.well-known/jwks.json`

## Connect

```json
{
  "mcpServers": {
    "trustlayer": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://mcp.feedoracle.io/trust/mcp/"]
    }
  }
}
```

## Part of FeedOracle

The Trust Layer is part of [FeedOracle](https://feedoracle.io) compliance evidence infrastructure and powers the [Decision Preflight](https://github.com/ToolOracle/preflightoracle) monetization layer.

Part of the [ToolOracle](https://tooloracle.io) platform — 50+ MCP servers, 530+ tools.

## License

MIT
