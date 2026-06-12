# ChartSpec and share-chart

`share-chart --spec chart.json` POSTs the spec to FactIQ's web app and
returns `{shareId, shareUrl}` — a live, rendered chart page. The CLI accepts
either a bare ChartSpec or a `{chart: {...}, question: "..."}` envelope.

## Minimal valid spec

Required: `id`, `title`, `type`, `xField` (object with `key`), `series`
(array), `data` (wide-format rows — one object per x value, one key per
series).

```json
{
  "id": "us-unemployment-2019-2025",
  "title": "US unemployment spiked to 14.8% in April 2020, then recovered to 4.1% by mid-2025",
  "type": "line",
  "xField": { "key": "time", "label": "Date", "format": "date", "scale": "time" },
  "series": [
    { "key": "unemployment_rate", "label": "Unemployment rate" }
  ],
  "yAxisLabel": "Percent",
  "data": [
    { "time": "2019-01-01", "unemployment_rate": 4.0 },
    { "time": "2019-02-01", "unemployment_rate": 3.8 }
  ],
  "sources": [
    {
      "name": "Bureau of Labor Statistics",
      "program": "Current Population Survey (LNS14000000)",
      "url": "",
      "type": "database"
    }
  ],
  "annotations": [
    { "date": "2020-04-01", "text": "Pandemic peak" }
  ]
}
```

## Titles make a claim

The title is the chart's headline insight, not a description. It must name
the entity, the metric, and the claim with numbers, self-contained:

- Bad: "Employment Trends" (descriptive)
- Bad: "Healthcare added 2.8M jobs since 2020" (which country?)
- Good: "US healthcare added 2.8M jobs since 2020 while retail shed 340K"

If the claim depends on a time window, keep the range in the title.

## Chart types

| `type` | Use when |
|---|---|
| `line` | Trends over time (default for timeseries) |
| `area` | Single magnitude over time |
| `bar` | Discrete comparisons or short categorical time spans |
| `stacked_area` | Composition of a total over time |
| `pie` | Single-period composition, few categories |
| `scatter` | Two metrics correlated; needs `yField` |
| `bubble` | Scatter + a size dimension; needs `yField` + `sizeField`; no date x-axis |
| `small_multiples` | Same metric across many entities; set `facetField` |
| `map` | Geographic distribution; needs `mapConfig` `{visualizationType, mapId, geoKey, valueKey}` |

## Field reference

- `xField` / `yField` / `sizeField`: `{key, label?, format?, scale?}`.
  `format`: `date | number | currency | percent | text`. For time x-axes use
  `format: "date", scale: "time"` with ISO date strings.
- `series[]`: `{key, label?, type?, color?, stacked?, dashed?}` — `key` must
  match a key in each `data` row. Omit `color` unless you have a reason; the
  renderer assigns a palette.
- `data`: array of flat objects, `string | number | null` values. Use `null`
  for gaps, don't drop rows.
- `sources[]`: `{name, program, url, type}` where `type` is
  `database | web | derived`. Pull `name`/`program` from the
  `constituent_series` metadata the sql/series responses include. Use
  `derived` for computed metrics (YoY, indexed, ratios).
- `annotations[]`: `{date: "2020-04-01", text: "..."}` — use sparingly to
  mark events the narrative references.
- `subtitle`, `description`, `notes[]`, `footnote` — optional supporting
  text.

## Workflow

1. Fetch full data to disk: `... sql --full --out /tmp/data.json` (or
   `series ... --full --out`).
2. Write a small local Python script that reads the file, computes any
   derived metrics, and emits the wide-format `data` array + full spec to
   `chart.json`. Sort rows by the x value first — some endpoints return
   data reverse-chronological, which would render a backwards x-axis.
3. Validate locally that every `series[].key` and `xField.key` exists in the
   `data` rows.
4. `python3 scripts/factiq.py share-chart --spec chart.json --question "..."`
5. Return the `shareUrl`.
