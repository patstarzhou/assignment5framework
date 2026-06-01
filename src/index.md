---

title: Cereal Nutrition Explorer
toc: false
----------

# Cereal Nutrition Explorer


```js
import * as vega from "npm:vega";
import * as vegaLite from "npm:vega-lite";
import * as vegaLiteApi from "npm:vega-lite-api";
const vl = vegaLiteApi.register(vega, vegaLite);
```

```js
const rawCereals = await FileAttachment("data/cereal.csv").csv({typed: true});

const manufacturerNames = new Map([
  ["A", "American Home Food Products"],
  ["G", "General Mills"],
  ["K", "Kellogg's"],
  ["N", "Nabisco"],
  ["P", "Post"],
  ["Q", "Quaker Oats"],
  ["R", "Ralston Purina"]
]);

const cereals = rawCereals.map((d) => ({
  ...d,
  mfr_name: manufacturerNames.get(d.mfr) ?? d.mfr,
  type_name: d.type === "H" ? "Hot" : "Cold",
  sugar_calorie_ratio: d.sugars / Math.max(1, d.calories),
  rounded_rating: Math.round(d.rating * 10) / 10
}));

const metricOptions = new Map([
  ["Calories", "calories"],
  ["Sugar", "sugars"],
  ["Fiber", "fiber"],
  ["Protein", "protein"],
  ["Sodium", "sodium"],
  ["Carbohydrates", "carbo"],
  ["Potassium", "potass"],
  ["Fat", "fat"]
]);

const metricLabel = new Map(Array.from(metricOptions, ([label, field]) => [field, label]));
const allManufacturers = Array.from(new Set(cereals.map((d) => d.mfr_name))).sort();
```

<div class="note">
  <b>Guiding question:</b> Which cereals have the best nutrition tradeoffs, and what other factors relate to or affect the cereal rating?
</div>

<div class="grid grid-cols-4">
  <div class="card stat-card">
    <h2>Cereals</h2>
    <span class="big">${cereals.length}</span>
  </div>
  <div class="card stat-card">
    <h2>Avg. rating</h2>
    <span class="big">${d3.mean(cereals, (d) => d.rating).toFixed(1)}</span>
  </div>
  <div class="card stat-card">
    <h2>Median sugar</h2>
    <span class="big">${d3.median(cereals, (d) => d.sugars).toFixed(0)}g</span>
  </div>
  <div class="card stat-card">
    <h2>Top cereal</h2>
    <span class="winner">${d3.greatest(cereals, (d) => d.rating).name}</span>
  </div>
</div>

## Explore the data

```js
const selectedManufacturers = view(Inputs.checkbox(allManufacturers, {
  label: "Manufacturers",
  value: allManufacturers
}));

const maxSugar = view(Inputs.range([0, d3.max(cereals, (d) => d.sugars)], {
  label: "Maximum sugar grams per serving",
  step: 1,
  value: d3.max(cereals, (d) => d.sugars)
}));

const minRating = view(Inputs.range([0, 100], {
  label: "Minimum rating",
  step: 1,
  value: 0
}));

const xMetric = view(Inputs.select(metricOptions, {
  label: "X-axis measure for scatterplot",
  value: "sugars"
}));
```

```js
const filteredCereals = cereals.filter((d) =>
  selectedManufacturers.includes(d.mfr_name) &&
  d.sugars <= maxSugar &&
  d.rating >= minRating
);
```

<div class="grid grid-cols-3">
  <div class="card stat-card">
    <h2>Currently shown</h2>
    <span class="big">${filteredCereals.length}</span>
  </div>
  <div class="card stat-card">
    <h2>Avg. filtered rating</h2>
    <span class="big">${filteredCereals.length ? d3.mean(filteredCereals, (d) => d.rating).toFixed(1) : "—"}</span>
  </div>
  <div class="card stat-card">
    <h2>Lowest-sugar shown</h2>
    <span class="winner">${filteredCereals.length ? d3.least(filteredCereals, (d) => d.sugars).name : "—"}</span>
  </div>
</div>

```js
function scatterChart(data, {width}) {
  return vl.markCircle({filled: true, opacity: 0.8})
    .data(data)
    .encode(
      vl.x().fieldQ(xMetric).title(metricLabel.get(xMetric)),
      vl.y().fieldQ("rating").title("Consumer rating").scale({zero: false}),
      vl.color().fieldN("mfr_name").title("Manufacturer"),
      vl.size().fieldQ("fiber").title("Fiber"),
      vl.tooltip([
        "name",
        "mfr_name",
        "calories",
        "sugars",
        "fiber",
        "protein",
        "sodium",
        "rating"
      ])
    )
    .width(Math.max(300, width - 40))
    .height(420)
    .render();
}
```

<div class="card">
  <h2>Nutrition tradeoff vs. rating</h2>
  ${resize((width) => scatterChart(filteredCereals, {width}))}
</div>

```js
function topCerealsChart(data, {width}) {
  const top = data.slice().sort((a, b) => d3.descending(a.rating, b.rating)).slice(0, 15);

  return vl.markBar({cornerRadiusEnd: 3})
    .data(top)
    .encode(
      vl.x().fieldQ("rating").title("Rating"),
      vl.y().fieldN("name").title(null).sort("-x"),
      vl.color().fieldN("mfr_name").title("Manufacturer"),
      vl.tooltip([
        "name",
        "mfr_name",
        "rating",
        "sugars",
        "fiber",
        "calories"
      ])
    )
    .width(Math.max(300, width - 40))
    .height(Math.max(280, top.length * 24))
    .render();
}
```

<div class="card">
  <h2>Top-rated cereals after filtering</h2>
  ${resize((width) => topCerealsChart(filteredCereals, {width}))}
</div>

```js
function sugarHistogram(data, {width}) {
  return vl.markBar({opacity: 0.85})
    .data(data)
    .encode(
      vl.x().fieldQ("sugars").bin({step: 2}).title("Sugar grams per serving"),
      vl.y().count().title("Number of cereals"),
      vl.color().fieldN("type_name").title("Type"),
      vl.tooltip([vl.count()])
    )
    .width(Math.max(300, width - 40))
    .height(260)
    .render();
}
```

<div class="card">
  <h2>Sugar distribution</h2>
  ${resize((width) => sugarHistogram(filteredCereals, {width}))}
</div>

## Conclusion

Cereals with higher ratings favor higher fiber levels while disfavoring high sugar or high calorie levels. These were the only three nutritional factors that had a clear relationship with consumer ratings.  

<div class="repo-callout">
  Replace this with your public GitHub repository link before submission: <a href="https://github.com/YOUR-USERNAME/YOUR-REPO">Project repository</a>.
</div>

<style>
.subtitle {
  color: var(--theme-foreground-muted);
  font-size: 1.1rem;
  max-width: 850px;
  margin-bottom: 1.5rem;
}
.note, .repo-callout {
  border-left: 4px solid var(--theme-foreground-focus);
  background: color-mix(in srgb, var(--theme-foreground-focus) 8%, transparent);
  padding: 1rem;
  border-radius: 0.5rem;
  margin: 1rem 0 1.5rem;
}
.stat-card h2 {
  margin-top: 0;
  color: var(--theme-foreground-muted);
  font-size: 0.95rem;
}
.big {
  display: block;
  font-size: 2.1rem;
  font-weight: 700;
}
.winner {
  display: block;
  font-size: 1.1rem;
  font-weight: 700;
  line-height: 1.25;
}
.caption {
  color: var(--theme-foreground-muted);
  margin-top: -0.25rem;
}
.card h2 {
  margin-top: 0;
}
</style>
