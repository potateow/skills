---
name: purity
description: |
  Use when the user wants to test proxy/VPN nodes for IP purity (fraud score) and latency,
  analyze a Clash/Shadowrocket subscription, compare node quality, or interpret existing
  purity test results. Triggers on requests about: node testing, subscription analysis,
  proxy latency, IP purity, fraud scores, or the "purity" tool itself.
---

# Purity — Proxy Node Purity & Latency Testing

## Project Location

Purity is usually under `~/Documents/code/purity/`. Probe with `glob`/`ls` first — don't assume the path. If not found, ask the user.

## Intent Classification

### A. Run Node Test (most common)

When the user says "test nodes", "analyze this subscription", or "run purity":

**Step 1 — Determine input**
- No file specified → `glob *.yaml`, let user choose (or auto-pick if only one)
- URL provided → use directly; remind to quote the URL
- Base64/vmess text pasted → ask if it's a subscription link

**Step 2 — Check environment (silent)**
```bash
conda env list | grep purity
ls ~/Documents/code/purity/bin/mihomo 2>/dev/null || which mihomo
```
- Missing `purity` env → `conda env create -f environment.yml && conda activate purity`
- Missing `mihomo` → `python download_mihomo.py`

**Step 3 — Run test**
```bash
cd <project-path>
conda run -n purity python analyze_subscription.py \
  --config <file> \
  --output <filename>.json \
  --groups 0
```
- `--groups 0` selects all nodes (required in non-interactive mode)
- Workers auto-scale (4–12), no need to pass manually
- For specific regions: `--groups 香港,日本`
- For detailed small-region view: `--merge-small-groups-threshold 0`

**Step 4 — Summarize results**
Read the output JSON and summarize in plain language:
- **Top 3 nodes**: name, fraudScore, avg latency
- **By region**: overall performance per region
- **Not recommended**: fraudScore >= 50 or status == error
- **Technical details**: only if user asks

### B. Read Existing Results

When the user asks "how were last results" or "which node is best":
- Read existing `<filename>.json`
- Summarize as in Step 4 above
- If file missing, prompt to run a test first

### C. Compare Multiple Subscriptions

1. Test each config with different `--output` filenames
2. Read both result files
3. Compare: node count, fraudScore distribution, median latency per region

### D. Troubleshooting

When the user reports errors or all nodes failing:
- Check mihomo exists
- Suggest `--keep-temp` to retain logs
- Common causes: TUN mode still on, expired subscription link, port conflict

## Plain-Language Translation

| Metric | Interpretation |
|--------|---------------|
| fraudScore 0–15 | "Purity excellent" |
| fraudScore 16–40 | "Purity good" |
| fraudScore 41–70 | "Purity average, possible risk" |
| fraudScore >= 100 | "Purity poor, not recommended" |
| status: ok | "Test passed" |
| status: partial | "Partially available (IPPure or latency abnormal)" |
| status: error | "Test failed" |
| avgLatencyMs < 100 | "Latency very low" |
| avgLatencyMs 100–300 | "Latency normal" |
| avgLatencyMs > 300 | "Latency high" |

## Key Parameters

| Parameter | Default | Note |
|-----------|---------|------|
| `--workers` | 4–12 (auto) | Integer only, usually don't override |
| `--request-timeout` | 15s | Standard request timeout |
| `--ippure-timeout` | 25s | IPPure query timeout |
| `--startup-timeout` | 8s | mihomo startup wait |
| `--merge-small-groups-threshold` | 5 | 0 = disable merging |
| `--groups` | — | **Required**; `0` = all, or comma-separated names/indices |
| `--keep-temp` | false | Keep debug logs |

## Anti-Patterns

- ❌ Don't show raw JSON as the answer
- ❌ Don't assume `~/Documents/code/purity/` — probe first
- ❌ Don't write `--workers auto` (only integers accepted)
- ❌ Don't omit `--groups` (required in non-interactive mode)
- ❌ Don't ask user to handle interactive prompts

## Important Notes

1. **Close TUN mode before testing** or results will be skewed
2. **Quote URLs** to prevent zsh wildcard expansion
3. `partial`/`error` nodes may still work — could be temporary IPPure failure
4. Result file updates continuously; progress can be checked anytime
