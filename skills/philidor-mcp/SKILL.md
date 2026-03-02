---
name: philidor-mcp
description: >
  DeFi vault risk analytics MCP server: search 700+ vaults, compare risk scores,
  analyze protocols, run due diligence. Connect via Streamable HTTP — no API key needed.
  Use when setting up or using the Philidor MCP server for DeFi vault analysis,
  risk scoring, protocol research, or yield comparison.
---

# Philidor MCP Server — DeFi Vault Risk Analytics

An MCP server that gives AI agents access to institutional-grade DeFi vault risk analytics. Search vaults, compare risks, analyze protocols, and perform due diligence across Morpho, Aave, Yearn, Beefy, and Spark.

No API key required. No installation needed.

## When to Use This Skill

- User wants to set up the Philidor MCP server in Claude Desktop, Claude Code, Cursor, or Windsurf
- User asks about DeFi vault risk, safety, or yield and you have MCP tool access
- User wants to compare vaults or analyze protocols via MCP tools
- User needs to run due diligence on a specific vault
- User asks about DeFi market stats or risk methodology

## When NOT to Use This Skill

- User asks about token prices, swaps, or trading (read-only analytics only)
- User wants the CLI tool instead (use the `philidor-cli` skill)
- User asks about NFTs or non-DeFi topics

## Setup

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "philidor": {
      "url": "https://mcp.philidor.io/api/mcp"
    }
  }
}
```

### Claude Code

```bash
claude mcp add philidor --transport http https://mcp.philidor.io/api/mcp
```

### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "philidor": {
      "url": "https://mcp.philidor.io/api/mcp"
    }
  }
}
```

### Windsurf

Add to your MCP settings:

```json
{
  "mcpServers": {
    "philidor": {
      "serverUrl": "https://mcp.philidor.io/api/mcp"
    }
  }
}
```

### Docker (stdio)

```bash
docker run -i --rm ghcr.io/philidor-labs/philidor-mcp
```

## Tools

### `search_vaults`

Search and filter DeFi vaults by chain, protocol, asset, risk tier, TVL, and more.

| Parameter   | Type   | Description                                            |
| ----------- | ------ | ------------------------------------------------------ |
| `query`     | string | Search by vault name, symbol, asset, protocol, curator |
| `chain`     | string | Filter by chain (Ethereum, Base, Arbitrum, ...)        |
| `protocol`  | string | Filter by protocol ID (morpho, aave-v3, yearn-v3, beefy, spark) |
| `asset`     | string | Filter by asset symbol (USDC, WETH, ...)               |
| `riskTier`  | string | Filter by risk tier: Prime, Core, or Edge              |
| `minTvl`    | number | Minimum TVL in USD                                     |
| `sortBy`    | string | Sort field: tvl_usd, apr_net, name                     |
| `sortOrder` | string | Sort order: asc or desc                                |
| `limit`     | number | Max results (default 10, max 50)                       |

### `get_vault`

Get detailed vault information including risk breakdown, recent events, and historical snapshots.

| Parameter | Type   | Description                                  |
| --------- | ------ | -------------------------------------------- |
| `id`      | string | Vault ID (e.g. `morpho-ethereum-0x...`)      |
| `network` | string | Network slug (ethereum, base, arbitrum)      |
| `address` | string | Vault contract address (0x...)               |

Lookup by `id` OR by `network` + `address`.

### `get_vault_risk_breakdown`

Detailed breakdown of a vault's three risk vectors with sub-metrics.

| Parameter | Type   | Description            |
| --------- | ------ | ---------------------- |
| `network` | string | Network slug           |
| `address` | string | Vault contract address |

Returns: Asset Composition, Platform Code, and Governance scores with dimension-level detail, caps, hard-fail flags, and overrides.

### `compare_vaults`

Side-by-side comparison of 2-3 vaults on TVL, APR, risk score, risk tier, and audit status.

| Parameter | Type  | Description                                        |
| --------- | ----- | -------------------------------------------------- |
| `vaults`  | array | Array of 2-3 objects with `network` and `address`  |

### `find_safest_vaults`

Find the top 10 audited, high-confidence vaults sorted by risk score.

| Parameter | Type   | Description              |
| --------- | ------ | ------------------------ |
| `asset`   | string | Filter by asset symbol   |
| `chain`   | string | Filter by chain name     |
| `minTvl`  | number | Minimum TVL in USD       |

### `get_protocol_info`

Protocol details including TVL, vault count, versions, auditors, bug bounties, and security incidents.

| Parameter    | Type   | Description                                         |
| ------------ | ------ | --------------------------------------------------- |
| `protocolId` | string | Protocol ID (morpho, aave-v3, yearn-v3, beefy, spark) |

### `get_curator_info`

Curator details including managed vaults, TVL, chain distribution, and performance.

| Parameter   | Type   | Description |
| ----------- | ------ | ----------- |
| `curatorId` | string | Curator ID  |

### `get_market_overview`

High-level DeFi vault market statistics: total TVL, vault count, risk distribution, and TVL by protocol. No parameters required.

### `explain_risk_score`

Explain what a specific risk score means, including the tier, calculation method, and thresholds.

| Parameter | Type   | Description          |
| --------- | ------ | -------------------- |
| `score`   | number | Risk score (0-10)    |

### `list_vaults_with_incidents`

List all vaults that had a recent critical incident (last 365 days). Sorted by TVL descending, then by recency. No parameters required.

## Resources

MCP resources provide reference data that can be loaded into context:

| URI                              | Description                                  |
| -------------------------------- | -------------------------------------------- |
| `philidor://methodology`         | The Vector Risk Framework v4.1 documentation |
| `philidor://supported-chains`    | Supported blockchain networks with counts    |
| `philidor://supported-protocols` | Supported DeFi protocols with TVL data       |

## Prompts

Built-in prompts for structured analysis:

| Prompt                      | Description                                             |
| --------------------------- | ------------------------------------------------------- |
| `vault_due_diligence`       | Comprehensive due diligence report for a vault          |
| `portfolio_risk_assessment` | Portfolio-level risk analysis across multiple positions  |
| `defi_yield_comparison`     | Yield comparison analysis with risk-adjusted metrics    |

## Risk Scoring

Philidor uses the **Vector Risk Framework v4.1** to decompose vault risk into three measurable vectors:

```
Final Score = 40% Asset + 40% Platform + 20% Governance
```

| Tier      | Score      | Meaning                                                              |
| --------- | ---------- | -------------------------------------------------------------------- |
| **Prime** | 8.0 - 10.0 | Institutional-grade — mature code, multiple audits, safe governance  |
| **Core**  | 5.0 - 7.9  | Moderate safety — audited but newer or flexible governance           |
| **Edge**  | 0.0 - 4.9  | Higher risk — requires careful due diligence                        |

### Asset Composition (40%)

Quality of underlying collateral. Blue-chip assets (ETH, USDC) score highest. Factors: oracle reliability, liquidity depth, peg stability.

### Platform Code (40%)

Code maturity measured by: Lindy Score (time-based safety), Audit Density (number and type of audits), Dependency Risk (multiplicative penalties), Incident Penalty (caps score after security events).

### Governance (20%)

Exit window for users: Immutable contract = 10/10, 7+ day timelock = 9/10, No timelock = 1/10.

## Agent Workflows

### Workflow 1: Vault Due Diligence

1. `search_vaults` — find the vault by name, asset, or protocol
2. `get_vault` — get full details including TVL, APR, risk score
3. `get_vault_risk_breakdown` — understand what drives the risk score
4. `list_vaults_with_incidents` — check for any active security events
5. Present findings with risk tier, score breakdown, and any red flags

### Workflow 2: Find Safest Vaults for an Asset

1. `find_safest_vaults` with `asset` filter — get top audited vaults
2. `compare_vaults` — side-by-side the top 2-3 candidates
3. `get_vault_risk_breakdown` on the winner — confirm risk profile
4. Recommend with risk-adjusted yield rationale

### Workflow 3: Protocol Research

1. `get_market_overview` — understand the overall landscape
2. `get_protocol_info` — deep-dive into a specific protocol
3. `search_vaults` with `protocol` filter — see its vault offerings
4. `compare_vaults` — compare top vaults across protocols

### Workflow 4: Yield Comparison

1. `search_vaults` with `sortBy: apr_net` — find highest-yielding vaults
2. `compare_vaults` — side-by-side comparison
3. `get_vault_risk_breakdown` — ensure high yield isn't masking high risk
4. Present risk-adjusted yield analysis

## Best Practices

1. **Always check risk before recommending.** Never recommend a vault based on APR alone. Always call `get_vault_risk_breakdown` and check for Edge-tier scores or incidents.

2. **Note data freshness.** The `last_synced_at` field in vault responses indicates when data was last refreshed from on-chain. Flag stale data to users.

3. **Cross-reference incidents.** Before recommending, call `list_vaults_with_incidents` to check for active security events that may affect vault safety.

4. **Use the right lookup method.** Use `id` for known vaults. Use `network` + `address` when you have a contract address. Use `search_vaults` for discovery.

5. **Leverage built-in prompts.** The `vault_due_diligence` and `portfolio_risk_assessment` prompts provide structured analysis templates.

## Links

- **Analytics Dashboard**: [https://app.philidor.io](https://app.philidor.io)
- **API Documentation**: [https://api.philidor.io/v1/docs](https://api.philidor.io/v1/docs)
- **Risk Methodology**: [https://app.philidor.io/methodology](https://app.philidor.io/methodology)
