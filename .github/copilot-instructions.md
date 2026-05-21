# CloudOpt -- AI Assistant Context

This file is the canonical source of product framing for any AI assistant
(GitHub Copilot, Claude, Cursor, etc.) working on this repository. Read it
before answering any question about what CloudOpt is, what it does, or
which direction to take a feature.

---

## What CloudOpt is

**CloudOpt is a Cloud Efficiency tool.** Its primary focus areas are:

1. **Performance** -- detect workloads that are under-provisioned, mis-fit
   to their SKU family, or running on a SKU that constrains them
   (saturated CPU, memory pressure, retiring SKUs, wrong family for the
   workload profile).
2. **Capacity** -- ensure customers have the right amount of capacity in
   the right places: quota headroom, capacity reservations actually
   utilized, instance counts matched to load, no resources stranded.
3. **Resiliency** -- surface configurations that put workloads at risk
   (legacy / retiring SKUs, missing redundancy signals, orphan
   resources that mask real dependency gaps, capacity reservations
   that aren't backing what they were created for).

Cost reduction can be a *consequence* of these recommendations (smaller
SKU, fewer instances, reclaimed orphans) but **cost optimization is not
the product's mission**. Do not describe CloudOpt as a cost-optimization,
FinOps, or savings tool, and do not bias recommendation framing toward
"cheapest option."

## What CloudOpt is not

- Not a cost-optimization tool.
- Not a FinOps tool.
- Not Azure Advisor or a wrapper around it.
- Not a billing analyzer -- CloudOpt does not consume invoice or
  consumption data.
- Not a runtime / agent product -- it runs read-only, on-demand, against
  Azure APIs and (optionally) externally supplied observability exports.

## Recommendation framing rules

When generating user-facing text in findings, docs, prompts, or
explanations:

- Frame *every* recommendation around **performance fit, capacity
  health, or resiliency posture**, not savings.
  - Good: "This SKU's CPU is saturated; move to a family that matches
    the workload profile."
  - Good: "Quota utilization is at 92% -- request an increase before
    capacity blocks deployment."
  - Bad: "Save $X/month by downsizing."
  - Bad: "This is the cheapest SKU that fits."
- When a "cheaper" SKU is selected, the rationale is "smallest fit that
  preserves performance headroom," not "lowest cost." Cost is a
  tiebreaker among performance-equivalent options, never the primary
  selector.
- Right-size *down* recommendations exist to **release stranded
  capacity** (over-provisioned headroom that hides real demand
  signals), not to cut the bill.
- Right-size *up* recommendations are **first-class** -- they protect
  performance and resiliency. They are not a deprioritized "we increase
  the bill so we don't do them" feature; they wait on enrichment data
  quality, not on product positioning.
- Decom / cleanup recommendations exist because orphans **distort
  capacity planning and operational hygiene**, not because they cost
  money.

## The three confidence tiers map to efficiency claims, not savings claims

- HIGH: the efficiency claim is verifiable from authoritative Azure
  signals or guest/workload-level telemetry.
- MEDIUM: the efficiency claim is supported by host-level platform
  metrics only; the workload owner should validate.
- LOW: structural signal exists but a key dimension (e.g. duration for
  CRR) cannot be confirmed from a snapshot.

## Operational facts (don't restate the mission incorrectly)

- Read-only. Two phases: `collect` (in customer tenant) -> `analyze`
  (in engineer's environment).
- Per-finding code: `CAT-SUB-NNN`. Categories: `rightsize`, `swap`,
  `decom`, `cleanup`, `quota`, `crr`.
- The product surface is the Excel workbook, the local web dashboard,
  and the JSON export -- not a SaaS, not a recurring agent.
- Data sources: Azure Resource Graph, Azure Monitor (PT1H), Compute
  resource_skus API, Azure Quota API, Capacity Reservation Groups API,
  and *optional* customer-supplied OS-agent / APM CSVs (Datadog,
  Splunk, Dynatrace, New Relic, VM Insights, Prometheus, Elastic).

## When in doubt

Reach for "efficiency," "fit," "headroom," "saturation," "capacity
posture," "performance class," "resiliency posture." Avoid "savings,"
"cost," "spend," "bill," "ROI," "TCO," and "FinOps" except where the
underlying API or metric literally has that name (e.g. "still billed"
when describing the stopped-allocated state, which is the
*Azure platform's* description, not CloudOpt's framing).
