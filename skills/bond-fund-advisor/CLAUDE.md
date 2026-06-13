# Bond Fund Advisor / 债券基金顾问 — Developer Notes

## Purpose

A Claude Code skill that recommends bond funds (债基/债券基金) using an 8-factor multi-source scoring model. This skill is for financial education and research use — it does NOT provide investment advice.

## Structure

```
bond-fund-advisor/
  SKILL.md          # Main skill file (YAML frontmatter + full content)
  CLAUDE.md         # This file — developer maintenance notes
```

No Python scripts in v1.0. The skill is knowledge-driven: it guides Claude through data fetching via WebSearch/WebFetch and manual scoring calculations.

## Scoring Model Maintenance

### Factor Weights

The 8 factor weights must always sum to **100%**:

| Factor | Weight | Key Data Source |
|--------|--------|----------------|
| Risk-adjusted return | 25% | Morningstar / 天天基金 Sharpe data |
| Max drawdown control | 20% | 天天基金 drawdown history |
| Return stability | 15% | Monthly return series (calculated) |
| Fund manager capability | 15% | 天天基金 / Howbuy manager data |
| Fee efficiency | 10% | Fund prospectus / 天天基金 fee data |
| Size & liquidity | 8% | 天天基金 AUM data |
| Institutional holding | 4% | Semi-annual/annual report |
| Credit quality | 3% | Quarterly report holdings |

### Scoring Thresholds

The scoring thresholds in SKILL.md are based on **Chinese bond fund market statistics circa 2024-2025**. When updating:

- Verify against current market data (e.g., current distribution of Sharpe ratios, drawdowns)
- For convertible bond funds, thresholds are intentionally relaxed (different risk profile)
- The "excellent" range should target roughly the top 20% of funds in each category

### Data Source URLs

Primary data sources referenced:
- 天天基金: `fund.eastmoney.com`
- 晨星中国: `www.morningstar.cn`
- 好买基金: `www.howbuy.com`
- 中国债券信息网: `www.chinabond.com.cn`

These may change over time. The skill uses WebSearch to find current endpoints.

## Constraints

1. **Bond funds ONLY.** Never stocks, mixed funds, money market funds. This is the core identity of this skill.
2. **Chinese market focus.** The classification system, data sources, and reference models are China-market-oriented.
3. **Educational purpose.** All output must include the disclaimer. The skill is for education and research, not investment advice.

## Testing

Manual verification steps:
1. Invoke: `/bond-fund-advisor 推荐稳健型纯债基金`
2. Check: only bond funds appear (no stocks/mixed/MMF)
3. Check: scoring tables present with weights summing to 100
4. Check: WebSearch used for current data
5. Check: disclaimer present in output
6. Edge case: "推荐股票基金" → should clarify bond-only scope

## Version History

- **v1.0.0** — Initial release with 8-factor model, knowledge-driven architecture
