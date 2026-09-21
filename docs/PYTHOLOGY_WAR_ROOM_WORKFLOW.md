# Pythology War Room Workflow

> Internal architecture note for the Pythology war-room stack.
>
> **Working systems in scope:** Atlas (including Poseidon), EarthNet, ARCUS, Prometheus, and Oracle.
>
> **Supporting repositories in scope:** DoWhy, causal-learn, PySTAC Client, GeoPandas, Pyro, Agent Reach, NeuralForecast, TimesFM, ContentMachine, and the Pythology-hardened Grok Build fork as the candidate agent-runtime/orchestration layer.
>
> **Oracle** is the internal name for the Pythology fork of God's Eye View. Oracle is a private situational-awareness, spatial-fusion, and visualisation surface. It is not the source of truth for third-party data.

---

## 1. North star

The war room should behave like one connected intelligence system, not five separate products with five unrelated screens.

Prometheus notices something worth attention and can effectively say:

> **Check this out — looks interesting.**

That single event should be able to cause the connected systems to react around the same event ID, time window, geography, provenance, and confidence state:

1. **EarthNet** frames the environmental / planetary context.
2. **Atlas** zooms into operational detail; **Poseidon** handles the maritime dimension inside Atlas.
3. **ARCUS** surfaces relevant state/change/anomaly context.
4. **Oracle** assembles the live physical-world picture around the event: aircraft, vessels, satellites, fires, roads, infrastructure, public sensors/cameras and other lawful contextual feeds.
5. **Prometheus** reasons over the combined evidence, mechanisms, alternatives, uncertainty and outcomes.
6. **The Pythology Agent Runtime** receives scoped tasks from Prometheus and dispatches them to isolated specialist workers with explicit tools, permissions, context and output contracts.
7. **Agent Reach**, when explicitly asked for an evidence gap, performs a constrained public-information retrieval task through that runtime and returns provenance-bearing evidence. It does **not** perform purchases, account changes, arbitrary execution, unrestricted browsing with credentials, or other side effects.

The visual target is intentional: Atlas, EarthNet and Oracle may all show globes, but each globe is a different instrument.

- **Atlas:** local / operational intelligence, with New Zealand as the strongest initial focus.
- **EarthNet:** planetary environmental state, observation, solar / atmospheric / oceanic context and large-scale change.
- **Oracle:** live situational awareness — what physical entities, sensors and infrastructure are around an event right now.

When useful, the globes should be capable of coordinated motion: one event, three perspectives.

---

## 2. Core architecture rule

**Do not route third-party data “through Oracle” and treat Oracle as the source.**

Preserve the original source, licence, retrieval time, spatial/temporal bounds and transformation history.

Preferred pattern:

```text
Original lawful source
        |
        v
Pythology ingestion / normalisation
        |
        +--------------------+
        |                    |
        v                    v
Shared event/evidence bus   Oracle visualisation
        |
        +--> Atlas / Poseidon
        +--> EarthNet
        +--> ARCUS
        +--> Prometheus
```

Oracle may contribute useful provider adapters, normalisers, spatial algorithms and UI components, but the provenance chain must still identify the original provider.

---

## 3. Shared event contract

Every cross-system event should eventually carry a common envelope.

Minimum target fields:

- `event_id` — persistent Pythology event identifier.
- `event_type` — observation, anomaly, hypothesis, forecast, outcome, alert, sensor hit, etc.
- `observed_at` — event time.
- `ingested_at` — Pythology receipt time.
- `valid_from` / `valid_to` — when the evidence applies, when relevant.
- `geometry` — point, line, polygon, bounding box, corridor or null.
- `source` — original provider / dataset / sensor.
- `source_record_id` — upstream identifier when available.
- `source_url` — canonical source or retrieval endpoint when lawful and useful.
- `licence_class` — internal label describing reuse constraints.
- `provenance` — transformations, normalisers and systems that touched the record.
- `confidence` — if the source or Pythology derives one.
- `quality_flags` — stale, partial, simulated, inferred, coarse, unverified, etc.
- `related_event_ids` — mechanism / precursor / outcome / corroboration links.
- `prometheus_hypothesis_id` — when evidence participates in a Prometheus mechanism or hypothesis.
- `retention_policy` — where appropriate.

The event envelope should make it possible to trace a later Prometheus conclusion back through Oracle/Atlas/EarthNet/ARCUS to the exact underlying source.

---

## 4. System responsibilities

### Atlas

Atlas is the operational geospatial intelligence surface.

Primary responsibilities:

- high-resolution regional / NZ situational view;
- environmental, forestry, road, agriculture and infrastructure layers;
- spatial joins and operational overlays;
- historical and current geospatial evidence;
- event drill-down and local impact context;
- **Poseidon lives inside Atlas** for maritime/ocean operational intelligence.

Atlas should consume strong spatial primitives from GeoPandas and Earth-observation discovery from PySTAC Client.

### Poseidon inside Atlas

Poseidon owns the maritime dimension of Atlas:

- vessels and maritime movement;
- ocean state and marine hazards;
- reef/coastal observations where relevant;
- ports, routes and marine infrastructure;
- maritime context around environmental events;
- eventual fusion with lawful AIS and other marine sources.

Poseidon should not become a second standalone spatial stack if Atlas already provides the common map/event infrastructure.

### EarthNet

EarthNet is the planetary environmental-intelligence layer.

Primary responsibilities:

- Earth-observation and remote-sensing state;
- environmental anomalies and change;
- solar / atmospheric / oceanic context;
- larger-scale weather and hazard relationships;
- satellite-observation timing and coverage;
- environmental evidence supplied to Prometheus.

EarthNet should use PySTAC Client for catalogue discovery and GeoPandas for spatial relationships, while Oracle can add live physical-world context around EarthNet detections.

### ARCUS

ARCUS should remain focused on change, state, pattern and anomaly context rather than duplicating the full spatial stack.

Likely contributions from the supporting repos:

- GeoPandas for spatialising changes/anomalies;
- causal-learn for candidate dependency structure;
- DoWhy for testing whether a proposed relationship survives causal checks;
- Pyro for uncertain / latent state inference;
- NeuralForecast for trained specialist forecasts over Pythology time series;
- TimesFM for generalist / zero-shot benchmark forecasts and multivariate forecasting experiments;
- Agent Reach for tightly scoped external corroboration when an evidence gap exists.

### Prometheus

Prometheus is the reasoning and causal-intelligence layer.

Primary responsibilities:

- hypotheses and mechanisms;
- anomaly interpretation;
- precursor relationships;
- forecasts;
- Decision Futures;
- competing explanations;
- outcome resolution;
- confidence updates;
- mechanism scoring and learning.

Prometheus should not blindly trust any one supporting library. Each library contributes a type of evidence or reasoning primitive.

Forecasting should be treated as a **multi-witness system** rather than a single model output:

- NeuralForecast supplies trained specialist models;
- TimesFM supplies a strong generalist / zero-shot comparison where licensing permits;
- causal-learn and DoWhy address causal structure/effect questions;
- Pyro carries probabilistic uncertainty and latent-state beliefs;
- Prometheus compares, scores, resolves and learns from all of them against real outcomes.

A predictive hit does not prove a causal mechanism, and a causal story does not guarantee a good forecast. Prometheus should keep those ledgers related but distinct.

### Oracle

Oracle is the private Pythology situational-awareness and spatial-fusion surface.

Primary responsibilities:

- display the live physical-world context around Pythology events;
- moving-object awareness;
- spatial proximity and contact queries;
- sensor handoff / nearby-sensor discovery;
- target trails and temporal context;
- infrastructure context;
- public-camera / lawful public-sensor discovery;
- orbital context;
- coordinated war-room visualisation;
- prototype provider adapters and spatial interactions before promoting them into shared Pythology services.

Oracle is **not** authoritative simply because something appears on the globe.

### Pythology Agent Runtime

The hardened Grok Build fork is the leading candidate for the **agent-runtime / orchestration fabric** connecting Prometheus to specialist capabilities.

It is not another intelligence system and it is not the shared data bus.

Its job is to execute **bounded work on behalf of Prometheus**:

- spawn specialist subagents with independent context windows;
- attach only the tools/capabilities each worker requires;
- isolate work with sandboxes and Git worktrees where appropriate;
- expose internal capabilities through explicit adapters (for example MCP/ACP-style interfaces);
- maintain structured request/response contracts;
- support headless/automated execution;
- return results to the shared evidence/event layer with provenance;
- keep privilege escalation explicit rather than inherited.

Desired relationship:

```text
                         PROMETHEUS
                   reason / choose tasks
                           |
                           v
                 Pythology Agent Runtime
                   scope / isolate / run
                           |
       +-------------------+--------------------+
       |          |          |         |        |
       v          v          v         v        v
   EO worker   spatial    causal    forecast  research
    PySTAC     GeoPandas  DoWhy +   Neural-   Agent
                         causal-    Forecast   Reach
                         learn      TimesFM
       |          |          |         |        |
       +-------------------+--------------------+
                           |
                           v
                  shared evidence/event layer
                           |
                           v
                       PROMETHEUS
                  evaluates / updates ledger
```

The runtime is a **conductor**, not the orchestra and not the score.

System-to-system state should still move through explicit Pythology APIs/events. We should not make a long-running agent process the only way Atlas, EarthNet, ARCUS, Oracle and Prometheus can communicate.

---

## 5. What each supporting repository adds

## DoWhy

**Role:** causal estimation, identification, refutation and counterfactual testing.

Strengthens:

- **Prometheus:** test whether a mechanism has measurable causal support; refute fragile assumptions; compare intervention/counterfactual expectations.
- **ARCUS:** distinguish correlation from candidate causal effect when analysing state changes.
- **EarthNet / Atlas:** test specific environmental or operational causal questions after the observational pipeline is trustworthy.

Prometheus use pattern:

```text
named mechanism
  -> identify causal estimand
  -> estimate effect
  -> refutation / sensitivity checks
  -> return result + limitations
  -> Prometheus updates mechanism evidence
```

DoWhy is a testing instrument, not an oracle of causal truth.

---

## causal-learn

**Role:** causal-structure discovery and candidate graph generation.

Strengthens:

- **Prometheus:** proposes candidate mechanism structures and challenges manually declared graphs.
- **ARCUS:** identifies possible dependency structure behind recurring change/anomaly patterns.
- **EarthNet / Atlas:** exploratory structure discovery in multivariate environmental/spatial datasets.

Important boundary:

Outputs are **candidate structures**, not established causal truth. Hidden confounding, spatial dependence, temporal autocorrelation, measurement error and non-stationarity must remain visible.

Ideal relationship:

```text
causal-learn proposes
Prometheus names/explains
DoWhy tests
Pyro carries uncertainty
outcomes adjudicate
```

---

## PySTAC Client

**Role:** standardised discovery of Earth-observation assets through STAC APIs.

Strengthens:

- **EarthNet:** find imagery/EO assets by time, geometry, collection and metadata without bespoke provider code for every catalogue.
- **Atlas:** discover imagery relevant to a local event/AOI.
- **Poseidon:** locate coastal/marine EO assets where appropriate.
- **Prometheus:** request new Earth-observation evidence for an active hypothesis.
- **Oracle:** optionally show available/current satellite evidence around an event, while the actual EO ingestion remains a Pythology service.

Key value:

```text
event geometry + time window
        ->
STAC search
        ->
candidate scenes/assets
        ->
quality/cloud/coverage filters
        ->
Pythology evidence record
```

---

## GeoPandas

**Role:** spatial relationship engine for vector data.

Strengthens:

- **Atlas / Poseidon:** joins, buffers, intersections, containment, distance, CRS discipline and operational vector analysis.
- **EarthNet:** relate environmental observations to regions/assets/corridors.
- **ARCUS:** attach detected change to places, infrastructure and boundaries.
- **Prometheus:** answer spatial questions such as “what lies inside / near / downstream / along this affected area?”
- **Oracle:** share deterministic geometry logic rather than duplicating spatial calculations in browser-only code.

GeoPandas should underpin repeatable spatial facts; the UI should not be the only place those relationships exist.

---

## Pyro

**Role:** probabilistic programming, latent-state inference and uncertainty propagation.

Strengthens:

- **Prometheus:** posterior uncertainty over mechanisms, latent regimes, probabilistic Decision Futures and Bayesian updates after outcomes.
- **ARCUS:** latent-state / regime models when observed signals are incomplete.
- **EarthNet / Atlas:** probabilistic state estimation where evidence is noisy or partial.

Boundary:

Pyro models uncertainty. It does **not** make a causal model valid merely because it is Bayesian.

Desired flow:

```text
explicit mechanism assumptions
        +
observations
        ->
Pyro probabilistic model
        ->
posterior / latent state / predictive distribution
        ->
Prometheus reasoning + ledger
```

---

## Pythology-hardened Grok Build

**Role:** agent harness / execution and orchestration runtime.

Strengthens:

- **Prometheus:** lets the reasoning layer delegate bounded investigations rather than directly holding every tool, credential and execution capability.
- **EarthNet:** can expose EO/search/analysis operations as narrow worker capabilities instead of giving Prometheus broad backend access.
- **Atlas / Poseidon:** can expose geospatial or maritime queries through scoped contracts.
- **ARCUS:** can run anomaly/change investigations in isolated workers and return structured findings.
- **Oracle:** can provide situational context through a read-only query adapter while remaining visually and operationally independent.
- **Agent Reach:** supplies the isolation, subagent, permission and tool-runtime patterns needed to keep public-web research on a short leash.

Useful upstream concepts include independent subagent contexts, worktree isolation, explicit agent/persona contracts, persistent memory primitives, headless operation, MCP/plugin adapters and kernel-level sandboxing.

Pythology security divergence is intentional:

- trace/session uploads hard-disabled;
- upstream internal trace export disabled;
- remote xAI config/model fetch defaults off;
- strict sandbox default;
- restrictive subprocess environment inheritance;
- no home-directory plugin auto-trust;
- controlled upstream-sync review path.

Boundary:

The agent runtime **must not become the authoritative integration bus**. It may orchestrate tasks and tools, but durable events, evidence, identities and state transitions belong in Pythology services/ledgers.

---

## Agent Reach

**Role:** constrained public-evidence acquisition worker.

Strengthens:

- **Prometheus:** fill a clearly defined evidence gap in a hypothesis/mechanism.
- **ARCUS:** seek public corroboration of an anomaly when explicitly requested.
- **EarthNet / Atlas:** collect lawful public contextual evidence where ordinary structured feeds are insufficient.
- **Oracle:** can be asked for external corroboration around a selected event, but should return evidence to the shared evidence layer rather than secretly altering Oracle state.

Required boundary:

```text
Prometheus scoped research question
        ->
sandboxed Agent Reach worker
        ->
public-source retrieval
        ->
provenance-bearing evidence
        ->
Pythology evidence store
        ->
Prometheus evaluates
```

Initial prohibitions:

- no purchasing;
- no payment methods;
- no account changes;
- no production credentials;
- no unrestricted shell;
- no autonomous code execution from retrieved material;
- no browser-cookie harvesting in the initial deployment;
- no write access to Pythology production systems.

Agent Reach fetches the evidence. It does **not** buy the pie.

---

## NeuralForecast

**Role:** trainable specialist neural forecasting suite.

Current fork baseline inspected: NeuralForecast 3.2.2, Apache-2.0, Python >=3.10.

Strengthens:

- **Prometheus:** creates specialised forecasting witnesses for recurring Pythology signals and lets Prometheus compare model families rather than trusting one forecaster.
- **EarthNet:** environmental time-series forecasting using historic targets plus weather, solar, oceanic or other exogenous variables.
- **ARCUS:** forecast whether an observed state/change pattern is likely to continue, reverse or transition.
- **Atlas / Poseidon:** operational forecasting for local environmental, agricultural, road or maritime series where sufficient training history exists.

Useful model families include N-BEATS/N-HiTS, DeepAR, TFT, PatchTST, iTransformer and others. NeuralForecast also supports exogenous variables, probabilistic outputs, quantile/distribution losses and automated model selection.

Desired role:

```text
Pythology historical series + known covariates
        ->
one or more trained specialist models
        ->
point + probabilistic forecasts
        ->
Prometheus forecast ledger
        ->
outcome resolution / per-model calibration
```

NeuralForecast models should compete on resolved Pythology events. Their value is earned through calibration and out-of-sample performance, not model reputation.

---

## TimesFM

**Role:** pretrained generalist time-series foundation model used as an independent forecasting witness and benchmark.

The current upstream line includes TimesFM 3.0 with native multivariate forecasting and past-only / past-and-future covariates.

Strengthens:

- **Prometheus:** provides a generalist forecast to compare against trained NeuralForecast specialists and Prometheus mechanism-derived expectations.
- **EarthNet:** rapid forecasting experiments over new environmental series without first training a bespoke model.
- **ARCUS:** zero-shot / low-setup comparison forecasts for newly detected patterns.
- **Atlas / Poseidon:** fast baseline forecasts for operational and maritime series before enough local history exists for a specialist model.

Important licensing boundary:

- TimesFM **source code** is Apache-2.0.
- Upstream states that pretrained weights through **2.5** remain Apache-2.0.
- Upstream currently distributes **TimesFM 3.0 pretrained weights under a separate non-commercial, non-production licence**.
- Therefore TimesFM 3.0 weights are a **lab/research instrument only** unless their licence changes or Pythology obtains appropriate rights.
- Production/commercial experiments must use a model/checkpoint whose rights explicitly permit the intended use.

Desired relationship:

```text
TimesFM generalist forecast
          +
NeuralForecast specialist forecasts
          +
Prometheus mechanism forecast
          ->
forecast tournament
          ->
resolved outcome
          ->
per-model + per-mechanism scoring / calibration
```

TimesFM should never silently become the production default merely because it is a foundation model.

---

## ContentMachine

**Role:** demonstration, communications and event-replay production pipeline. It is **not an intelligence source**.

Current fork baseline inspected: Apache-2.0 Node/React pipeline for story planning, scene generation, images, video, narration, project state and export.

Potential Pythology use:

- turn a completed Prometheus event lifecycle into an understandable cinematic replay;
- build investor / government demonstration packages from real Pythology evidence;
- assemble Atlas, EarthNet and Oracle captures with Prometheus timestamps, hypotheses, forecasts and resolved outcomes;
- preserve scene/project versions so a demonstration can be regenerated when underlying evidence or visual assets change;
- automate narration, scene sequencing and export without contaminating the analytical systems.

Example:

```text
resolved EVENT_ID
   ->
timeline + provenance-approved evidence
   ->
Atlas / EarthNet / Oracle captures
   ->
Prometheus hypothesis + forecast + outcome narrative
   ->
ContentMachine-derived production workflow
   ->
human-reviewed documentary / investor replay
```

Boundary:

ContentMachine consumes **approved outputs** from Gaia/Pythology. It cannot create evidence, revise Prometheus history, or turn generated media into factual source material.

Its current credential architecture must also be reviewed before adoption; Pythology should not inherit browser/localStorage secret handling without a security redesign.

---

## 6. Target “Check this out” workflow

The long-term showcase workflow:

```text
1. Prometheus detects or promotes an interesting event/hypothesis.
               |
               v
2. A shared EVENT_ID is published with geometry, time, confidence and provenance.
               |
               v
3. Prometheus asks the Pythology Agent Runtime for bounded investigation tasks.
   The runtime creates isolated workers with only the required capabilities.
               |
      +--------+---------+---------+
      |                  |         |
      v                  v         v
  EarthNet             Atlas      ARCUS
planetary context   local/ops    state/change
                         |
                     Poseidon
                    maritime view
      |                  |         |
      +--------+---------+---------+
               |
               v
4. Oracle centres on EVENT_ID and enriches the live spatial picture:
   - aircraft
   - vessels
   - satellites
   - fire detections
   - roads / traffic context
   - infrastructure
   - lawful public cameras / sensors
   - nearby observations
               |
               v
5. Sensor / evidence gaps are identified.
               |
       +-------+--------+
       |                |
       v                v
   PySTAC search    Agent Reach
   EO evidence      public evidence
       |                |
       +-------+--------+
               |
               v
6. GeoPandas builds deterministic spatial relationships.
               |
               +-----------------------------+
               |                             |
               v                             v
7A. Forecasting witnesses run:         7B. causal-learn may
    - NeuralForecast specialists           propose competing structures
    - TimesFM generalist where lawful
               |                             |
               +--------------+--------------+
                              |
                              v
8. Prometheus declares explicit competing mechanisms and forecast expectations.
               |
               v
9. DoWhy tests estimable causal claims / refutations.
               |
               v
10. Pyro propagates uncertainty / latent-state probabilities.
               |
               v
11. Prometheus compares:
    - mechanism-derived expectation
    - NeuralForecast outputs
    - TimesFM output where permitted
    - observed evidence
               |
               v
12. Prometheus updates:
    - hypothesis
    - confidence
    - forecast
    - Decision Futures
    - evidence ledger
    - forecast/model tournament state
               |
               v
13. Atlas, EarthNet and Oracle update around the SAME EVENT_ID.
               |
               v
14. Later outcome is linked back to the entire lifecycle and scores models/mechanisms.
               |
               v
15. If useful, ContentMachine can turn the resolved, provenance-approved lifecycle
    into a human-reviewed demonstration/replay.
```

The visual experience may be dramatic. The data model underneath it must remain boring, deterministic and traceable.

---

## 7. Sensor-handoff concept

One of Oracle's most valuable patterns is sensor handoff.

Example:

```text
EarthNet detects smoke / atmospheric anomaly
        ->
FIRMS or another thermal source confirms heat
        ->
weather explains likely movement
        ->
GeoPandas identifies exposed roads/assets
        ->
Oracle discovers lawful nearby public cameras/sensors
        ->
PySTAC discovers recent/relevant satellite scenes
        ->
Agent Reach finds public contextual evidence if asked
        ->
Prometheus evaluates whether all evidence supports one mechanism
```

The handoff should be implemented as a reusable service concept, not hard-coded as a visual trick inside Oracle.

---

## 8. Provenance and legal-data gate

Before a new external source can influence Pythology intelligence, capture:

1. provider;
2. dataset/API name;
3. governing licence / terms;
4. whether internal, commercial, derived or redistributed use is permitted;
5. attribution requirement;
6. rate / quota restrictions;
7. source timestamp semantics;
8. known quality limitations;
9. spatial / temporal accuracy;
10. storage and retention restrictions;
11. transformation history;
12. whether data is observed, inferred, simulated, estimated or synthetic.

If rights are unclear, keep that source out of production intelligence until reviewed.

Private/internal use is **not** a blanket exemption from provider terms.

---

## 9. War-room synchronisation

Future synchronisation should be event-driven rather than screen-driven.

A `focus_event` message may eventually contain:

```json
{
  "event_id": "EVT-...",
  "geometry": {},
  "time_window": {},
  "reason": "prometheus_attention",
  "prometheus_hypothesis_id": "HYP-...",
  "confidence": 0.78,
  "requested_views": ["atlas", "earthnet", "oracle"]
}
```

Each globe decides how to frame that same event according to its own job.

- Atlas: operational zoom.
- EarthNet: environmental / planetary context.
- Oracle: live surrounding entities, sensors and infrastructure.

The event should drive the camera state; the camera state must never become the authoritative event record.

---

## 10. Initial integration order

### Phase A — safe labs

- DoWhy + causal-learn in one Python 3.12 causal lab.
- PySTAC Client + GeoPandas in one Python 3.12 geospatial lab.
- Pyro in a dedicated Python 3.12 probabilistic lab.
- NeuralForecast in a forecasting lab for specialist model tournaments.
- TimesFM in the forecasting lab as a generalist benchmark, with checkpoints gated by licence.
- Pythology-hardened Grok Build in a disposable agent-runtime lab, with no production credentials or writable production mounts.
- Agent Reach as a worker inside or behind that sandboxed runtime boundary, not as a peer with unrestricted host access.
- ContentMachine in a separate presentation/replay lab with no authority over intelligence state.
- Oracle remains private and isolated while its useful adapters/algorithms are audited.

### Phase B — shared evidence primitives

Build / standardise:

- event IDs;
- evidence envelope;
- provenance;
- geometry;
- time windows;
- source/legal metadata;
- confidence / quality flags;
- relationship IDs.

### Phase C — read-only integration

Let the supporting tools enrich copied/test events without changing production state.

Introduce the Pythology Agent Runtime here as a **read-only orchestration fabric**:
- Prometheus may submit scoped jobs;
- workers receive only explicit capabilities;
- every request/result is attached to an EVENT_ID / hypothesis ID;
- worker output returns through the shared evidence layer;
- no worker may mutate Prometheus, Atlas, EarthNet, ARCUS or Oracle production state directly.

### Phase D — Prometheus-assisted requests

Allow Prometheus to submit typed tasks through the Agent Runtime:

- an EO search;
- a spatial relationship calculation;
- a causal test;
- a probabilistic update;
- one or more specialist/generalist time-series forecasts;
- a constrained evidence-gathering task.

Every request and response should be ledgered, including runtime worker identity, tool/capability set, inputs, outputs, timestamps and failure state.

### Phase E — coordinated war room

Atlas, EarthNet and Oracle subscribe to shared focus events and coordinate their visual state while retaining independent analytical responsibilities.

---

## 11. Things we should deliberately not do

- Do not turn the Agent Runtime into the authoritative event bus or source of truth.
- Do not let workers inherit the parent process's full filesystem, environment, network or credential set.
- Do not turn Oracle into another source-of-truth backend.
- Do not give Agent Reach unrestricted internet + credentials + shell + purchasing authority.
- Do not let causal-learn silently rewrite Prometheus mechanisms.
- Do not let Pyro probabilities masquerade as causal validity.
- Do not let forecast accuracy masquerade as proof of a causal mechanism.
- Do not deploy TimesFM 3.0 pretrained weights commercially/into production while their upstream licence prohibits that use.
- Do not let ContentMachine-generated narration, imagery or video become evidence or rewrite the factual ledger.
- Do not ingest imagery merely because PySTAC can find it; preserve quality/licence/cost filters.
- Do not perform browser-only spatial calculations when deterministic backend calculations should exist.
- Do not merge evidence from different licences into a derived database without understanding the obligations.
- Do not let three globes become three copies of the same product.
- Do not break Prometheus lifecycle traceability: observation -> hypothesis -> prediction -> scenario -> outcome -> lesson must retain the original IDs.

---

## 12. Current mental model

```text
                          +------------------+
                          |    PROMETHEUS    |
                          | reason / decide  |
                          +---------+--------+
                                    |
                                    v
                         +-------------------+
                         |  AGENT RUNTIME    |
                         | scope/isolate/run |
                         +---------+---------+
                                   |
                  +----------------+-------------------+
                  |                 |                  |
                  v                 v                  v
             +---------+       +---------+        +---------+
             | EARTHNET|       |  ATLAS  |        |  ARCUS  |
             | planet  |       | ops/NZ  |        | change  |
             +----+----+       +----+----+        +----+----+
                  |                 |
                  |            +----v-----+
                  |            | POSEIDON |
                  |            | maritime |
                  |            +----------+
                  |
                  +-----------------+------------------+
                                    |
                                    v
                             +-------------+
                             |   ORACLE    |
                             | live world  |
                             | / war room  |
                             +------+------+ 
                                    |
              +---------------------+----------------------+
              |          |          |          |           |
              v          v          v          v           v
           PySTAC    GeoPandas   Agent Reach  feeds     sensors
              |          |          |
              +----------+----------+
                         |
                         v
                  shared evidence

Execution / orchestration:
Pythology-hardened Grok Build -> bounded workers, tool routing, isolation, headless execution

Supporting reasoning:
causal-learn   -> candidate structures
DoWhy          -> causal tests/refutations
NeuralForecast -> trained specialist forecasts
TimesFM        -> generalist / zero-shot forecast benchmark
Pyro           -> uncertainty/latent states
ContentMachine -> human-facing replay after evidence is resolved
```

---

## 13. Definition of success

We know this architecture is working when Prometheus can identify an event and every participating system can answer a different, useful question about the same thing without losing provenance:

- **EarthNet:** what is happening environmentally at planetary/regional scale?
- **Atlas:** what does it mean operationally here?
- **Poseidon:** what is happening in the maritime dimension?
- **ARCUS:** what changed, and is the change unusual?
- **Oracle:** what physical entities, sensors and infrastructure surround it right now?
- **Pythology Agent Runtime:** which bounded worker/tool should answer the next question, and can it do so without receiving unnecessary privilege?
- **Prometheus:** what mechanism best explains the evidence, what alternatives remain, what happens next, how uncertain are we, and which forecasting/mechanism witnesses are actually calibrated?
- **ContentMachine (downstream only):** how do we explain the resolved event lifecycle clearly and spectacularly to a human without altering the evidence?

The war-room spectacle is the visible result.

The real asset is the shared, traceable intelligence underneath it.
