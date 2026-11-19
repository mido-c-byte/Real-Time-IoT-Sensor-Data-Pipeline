# Implementation outline

Use this checklist when building the pipeline. Check off each milestone after its acceptance check passes. Record the selected stack, units, thresholds, window settings, and measured demo latency here as decisions are made.

## 1. Select the stack and define the event

- [ ] Choose Kafka or Azure Event Hubs, then select Spark Structured Streaming (PySpark) or Azure Stream Analytics. Identify the cloud store and the dashboard data source. Use Power BI for the Azure path.
- [ ] Define the event fields: `device_id`, `timestamp`, `temperature`, `pressure`, `vibration`, and `machine_status`. Document units, UTC timestamp format, and allowed statuses. Map CSV `machine_id` to `device_id` if replaying the dataset.
- [ ] Agree on an event-to-dashboard latency target so “near real time” can be measured.

**Acceptance check** — Document one sample event, the chosen services, and the path a reading takes to the dashboard and historical store.

## 2. Generate and ingest events

- [ ] Build a Python simulator with configurable device count and emission interval.
- [ ] Add a repeatable demo mode that emits normal readings, one known threshold violation, and one malformed record.
- [ ] Publish to the selected topic or event hub. Document how to configure and run the producer and consumer.

**Acceptance check** — Observe events from two devices at the consumer and identify both injected cases by their input values.

## 3. Validate, detect, and aggregate

- [ ] Check required fields, nonempty device IDs, parseable timestamps, finite numeric readings, and allowed statuses. Keep rejected input and a rejection reason.
- [ ] Define configurable operating limits for temperature, pressure, and vibration in the agreed units. Document whether equality triggers each rule.
- [ ] Compute per-device reading counts, average sensor values, and violation counts over event-time windows. Choose a window duration and policy for late events.

**Acceptance check** — Replay a small known sequence spanning two windows. Verify per-device totals and averages, boundary behavior, and exclusion of malformed input from metrics.

## 4. Persist history and expose live metrics

- [ ] Write validated readings, violation details, and window summaries to the cloud store. Make quarantine records retrievable for diagnosis.
- [ ] Connect the dashboard to the live metrics source. Show latest device status, last reading time, sensor trends, and violation counts.

**Acceptance check** — Find the injected violation by device and timestamp in storage, confirm the dashboard reflects it, and measure how long it took to appear.

## 5. Package the demonstration

- [ ] Update the README diagram with the deployed components and add setup, run, and shutdown instructions.
- [ ] Record the injected inputs, expected outcomes, observed dashboard update, quarantine result, and matching historical query.

**Acceptance check** — A teammate can follow the README, reproduce the abnormal-event demo, and retrieve the reading after its dashboard window closes.

## Optional extension

- [ ] Build a historical warehouse baseline and compare a device's current window with its earlier performance using the same units and window duration.
