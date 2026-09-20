# Pythology War Room Workflow

> Internal architecture note for the Pythology war-room stack.
>
> **Working systems in scope:** Atlas (including Poseidon), EarthNet, ARCUS, Prometheus, and Oracle.
>
> **Supporting repositories in scope:** DoWhy, causal-learn, PySTAC Client, GeoPandas, Pyro, and Agent Reach.
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
6. **Agent Reach**, when explicitly asked for an evidence gap, performs a constrained public-information retrieval task and returns provenance-bearing evidence. It does **not** perform purchases, account changes, arbitrary execution, unrestricted browsing with credentials, or other side effects.

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

Likely contributions from the six supporting repos:

- GeoPandas for spatialising changes/anomalies;
- causal-learn for candidate dependency structure;
- DoWhy for testing whether a proposed relationship survives causal checks;
- Pyro for uncertain / latent state inference;
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

## 6. Target “Check this out” workflow

The long-term showcase workflow:

```text
1. Prometheus detects or promotes an interesting event/hypothesis.
               |
               v
2. A shared EVENT_ID is published with geometry, time, confidence and provenance.
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
3. Oracle centres on EVENT_ID and enriches the live spatial picture:
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
4. Sensor / evidence gaps are identified.
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
5. GeoPandas builds deterministic spatial relationships.
               |
               v
6. causal-learn may propose competing structures.
               |
               v
7. Prometheus declares explicit competing mechanisms.
               |
               v
8. DoWhy tests estimable causal claims / refutations.
               |
               v
9. Pyro propagates uncertainty / latent-state probabilities.
               |
               v
10. Prometheus updates:
    - hypothesis
    - confidence
    - forecast
    - Decision Futures
    - evidence ledger
               |
               v
11. Atlas, EarthNet and Oracle update around the SAME EVENT_ID.
               |
               v
12. Later outcome is linked back to the entire lifecycle.
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
- Agent Reach in a separately sandboxed environment.
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

### Phase D — Prometheus-assisted requests

Allow Prometheus to request:

- an EO search;
- a spatial relationship calculation;
- a causal test;
- a probabilistic update;
- a constrained evidence-gathering task.

Every request and response should be ledgered.

### Phase E — coordinated war room

Atlas, EarthNet and Oracle subscribe to shared focus events and coordinate their visual state while retaining independent analytical responsibilities.

---

## 11. Things we should deliberately not do

- Do not turn Oracle into another source-of-truth backend.
- Do not give Agent Reach unrestricted internet + credentials + shell + purchasing authority.
- Do not let causal-learn silently rewrite Prometheus mechanisms.
- Do not let Pyro probabilities masquerade as causal validity.
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
                  +-----------------+------------------+
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

Supporting reasoning:
causal-learn -> candidate structures
DoWhy       -> causal tests/refutations
Pyro        -> uncertainty/latent states
```

---

## 13. Definition of success

We know this architecture is working when Prometheus can identify an event and every participating system can answer a different, useful question about the same thing without losing provenance:

- **EarthNet:** what is happening environmentally at planetary/regional scale?
- **Atlas:** what does it mean operationally here?
- **Poseidon:** what is happening in the maritime dimension?
- **ARCUS:** what changed, and is the change unusual?
- **Oracle:** what physical entities, sensors and infrastructure surround it right now?
- **Prometheus:** what mechanism best explains the evidence, what alternatives remain, what happens next, and how uncertain are we?

The war-room spectacle is the visible result.

The real asset is the shared, traceable intelligence underneath it.
