# FactIQ Data Schemas

Static overview. The `context` subcommand returns the live, authoritative
catalog with full per-dataset descriptions — run it before relying on this
file. Some schemas are admin-only and simply won't appear in your `context`
output (requests to them return 403).

## United States

| Schema | Source | Coverage |
|---|---|---|
| `bls` | Bureau of Labor Statistics | Employment (CES), unemployment (CPS, e.g. `LNS14000000`), CPI, PPI, JOLTS job openings, wages, productivity |
| `oews` | BLS Occupational Employment & Wage Statistics | Wages and employment by occupation and metro area |
| `census` | Census Bureau | International trade (incl. `us_census_hs` — monthly imports/exports by HS commodity and partner country), retail, housing, demographics |
| `bea` | Bureau of Economic Analysis | GDP and components, personal income/spending, regional accounts |
| `eia` | Energy Information Administration | Petroleum, natural gas, electricity, renewables — production, consumption, prices |
| `ers` | USDA Economic Research Service | Agricultural and food economics |
| `bts` | Bureau of Transportation Statistics | Transportation and freight |
| `earnings` | Alpha Vantage / earnings calls | Earnings-call transcripts intelligence — searched via the `earnings` subcommand, not SQL |

## China

| Schema | Source | Coverage |
|---|---|---|
| `china` | National Bureau of Statistics | Macro indicators: GDP, industrial production, fixed-asset investment, prices |
| `china_customs` | General Administration of Customs (GACC) | Monthly imports/exports by 8-digit HS line and partner country (incl. rare earths) |

## India

| Schema | Source | Coverage |
|---|---|---|
| `mospi` | Ministry of Statistics (MOSPI) | CPI (national key: `State: All India`; note the 2012→2024 base change — two separate series families), WPI, IIP, GDP |
| `rbi` | Reserve Bank of India | Banking, credit, money supply, rates, forex reserves |
| `india_trade` | DGCI&S | Monthly imports/exports by HS commodity and partner (6- and 8-digit levels — never sum across levels) |
| `traffic` | TomTom | Indian city traffic flow |

## Global / other

| Schema | Source | Coverage |
|---|---|---|
| `imf` | International Monetary Fund | Cross-country macro indicators |
| `worldbank` | World Bank | Development and macro indicators by country |
| `singstat` | Singapore Department of Statistics | Singapore national statistics |

## Picking schemas

- US labor/inflation → `bls` (plus `oews` for occupation-level wages)
- US GDP/income → `bea`; US trade → `census`
- Energy anything → `eia`
- India macro → check BOTH `mospi` and `rbi`
- Trade-war / commodity-flow stories → `census` + `china_customs` +
  `india_trade` cover the same flows from each country's own books
- Cross-country comparisons → `imf` / `worldbank`
- Company-specific → `market` and `earnings` subcommands (not SQL schemas)

HS trade schemas (`us_census_hs` in census, `china_customs`, `india_trade`)
carry the same trade at multiple HS digit levels — filter to one level and
never sum across levels.
