# ICHOR · Astronaut Health & Radiation Monitoring

**Live app → [ichore.streamlit.app](https://ichore.streamlit.app)**

ICHOR is an interactive Streamlit dashboard for monitoring astronauts' health on board a deep-space mission.
It brings together radiation exposure, vital signs, symptoms, mental health and the crew's position inside the
**Orion** spacecraft into one control screen, and explains every result in plain words.

The idea: in deep space a solar storm can deliver a month's allowed dose in a few hours, and Earth is up to
22 minutes away. The crew has to know **where to go, what the radiation is doing to their own body, and what to
do next** without waiting for mission control.

> ⚠️ Educational prototype. Not a medical device and not for real medical use. Medication is always decided
> by a flight surgeon.

---

## Contents

- [The app at a glance](#the-app-at-a-glance)
- [Sidebar: ICHOR Control](#sidebar-ichor-control)
- [View 1 · Monitoring](#view-1--monitoring)
- [View 2 · Symptom Search](#view-2--symptom-search)
- [View 3 · Measurements](#view-3--measurements)
- [Optional categories](#optional-categories-sidebar)
- [Run it locally](#run-it-locally)
- [Deploy on Streamlit Community Cloud](#deploy-on-streamlit-community-cloud)
- [Repository structure](#repository-structure)
- [Behind the app (algorithms, briefly)](#behind-the-app-algorithms-briefly)
- [Data: what is real, what is simulated](#data-what-is-real-what-is-simulated)

---

## The app at a glance

| Part of the app | What the user sees | Main file |
|---|---|---|
| 📊 **Monitoring** | Mission status, 5 health gauges, live vitals, ICHOR console, 3D body model, radiation panel, Orion 3D map, clinical playbooks | `main.py` |
| 🔎 **Symptom Search** | Type what you feel in plain English → likely conditions and medication from the NASA medical kit | `ichor_search.py`, `ichor_symptoms.py` |
| 🧪 **Measurements** | Log a new measurement for an astronaut and see the history | `ichor_measurements.py` |
| 🛰 **Orion 3D** | Interactive model of Orion, inside and outside, with the dose in every room and an ML storm forecast | `orion_3d.py` |
| 🧠 **Mental health** | Live voice & breathing monitor, daily check-in, trends and prognosis | `ichor_mind.py`, `ichor_voice_monitor.py` |
| ▶ **Demo** | One button that runs every algorithm and shows the results in plain words | `run_all.py` |

The interface uses a dark green "HUD" theme (Orbitron / Share Tech Mono fonts) and three colour states
everywhere: 🟢 **NOMINAL**, 🟠 **WARNING**, 🔴 **CRITICAL**.

---

## Sidebar: ICHOR Control

The sidebar drives the whole dashboard:

| Control | What it does |
|---|---|
| **Astronaut / Cohort Profile** | Picks the astronaut. Astronauts from the crew database come first, then published cohort profiles (e.g. ISS / Shuttle cohorts) |
| **Mission Day (Mars Transit)** | Day 1–180 of the Earth → Mars transit; moves the rocket on the mission bar and changes the cumulative values |
| **Telemetry Override · Mission Scenario** | `Routine Operations`, `Solar Particle Event (Storm)`, `Deep Space Isolation Fatigue`, `Fluid Shift / Vision Change (SANS)` |
| **🔊 ICHOR Voice** / **Voice briefing** | Spoken status briefing in the browser |
| **📡 Live vitals feed** | Refreshes the simulated vital signs every 2 s |
| **☢️ 3D Organ Scan** | Link the organ exposure to the mission radiation, or set it manually; viewing angle; body outline on/off |
| **🔒 Categories** | Opens extra panels: Communications, Mental Health & Rocket, Fictional radiation data |

---

## View 1 · Monitoring

The main screen, from top to bottom:

1. **Header** with the **Crew Integrity Index** (a team-defined overall score) and a status banner:
   *NOMINAL* or *ANOMALY DETECTED* with the number of active protocols.
2. **Data provenance** strip: which values are real, which are simulated, which are team-defined.
3. **Mission bar** 🌍 → 🔴 showing the current day of the 180-day transit.
4. **Five gauges**: Radiation (mSv cumulative), Lymphocytes (cells/µL), Ocular pressure · SANS (mmHg),
   Bone density (% vs baseline), Sleep (hours/night), each coloured against published limits.
5. **Live biometric stream** with an animated ECG and heart rate, SpO₂, respiration and HRV.
6. **ICHOR Console**: a terminal-style log. Type commands such as `status`, `radiation`, `immune`, `eyes`,
   `bone`, `sleep`, `protocol`, `sources`, `help` and ICHOR answers.
7. **Vital signs · 3D body model**: the astronaut's organs (brain, heart, lungs, liver, stomach) coloured
   by their latest measurements and symptoms from the database.
8. **☢ Radiation hazard · effect on the organs** (*CHOOSE RADIATION*): pick an environment or event
   (ISS, deep space, Moon, Mars surface, Mars round trip, solar storms up to 2× the NASA limit), a duration,
   and whether the crew is in the storm shelter. The 3D body shows the dose per organ. An optional overlay shows
   NASA organ-dose reference values.
9. **Radiation & solar-storm detection (database)**: dose-rate over time, automatic storm detection,
   alerts that can be acknowledged, and a *"what if sheltered"* toggle.
10. **🛰 Orion · Radiation map (3D)**: see below.
11. **▶ Demo · All the algorithms**: see below.
12. **🎬 Asclepius onboard clinical playbooks**: appear only when something is abnormal. Each one shows the
    trigger, the condition, reference medication information (with source) and the immediate actions, plus a
    button to log that the protocol was applied.
13. **📚 Data sources**: what is real, what is simulated, with links.

### 🛰 Orion 3D radiation map

A three.js model of the Orion crew module and European Service Module, built from public NASA / ESA figures.

- **OUTSIDE / INSIDE** views, launch abort system, labels, auto-rotate, zoom and reset.
- Every room is coloured by its radiation dose; click a room for its details.
- **☀ SIMULATE SOLAR STORM** and a **warning banner** that tells the crew when to go to the storm shelter.
- **🤖 ML FORECAST** downloads the current NOAA proton flux, runs the storm forecast and applies it to the
  room colours. **APP VALUE** returns to the dashboard's dose.
- **📷 PICTURES**: gallery of the forecast and ISS dosimeter figures.
- **🔊 VOICE**: spoken warnings.

Needs internet in the browser (three.js is loaded from a CDN).

### ▶ Demo · All the algorithms

One button runs `run_all.py nolive` (about 1 minute): forecast → shielding → alert → health, on a 14-day
mission with the **real solar storm of 11 November 2025**. The results appear as green / yellow / red boxes
in plain words:

- 🚨 **Alert**: ALL CLEAR · WATCH · PREPARE · GO TO SHELTER, and why
- 🧭 **Where to go**: each astronaut's route to the safest room (e.g. *Galley → Hygiene bay → Storm shelter, 60 s*)
- ❤️ **Health**: one card per astronaut (radiation, body, early warning sign)
- 🎬 **The 14-day mission**: the story of the mission and of every astronaut

Doses are told as **% of the NASA monthly allowance** and **years of natural radiation on Earth**,
e.g. *"126 mSv, 50% of your monthly allowance, like 53 years on Earth"*.

---

## View 2 · Symptom Search

Decision support built on the real **NASA Medical Kit 3.001** contents.

1. The astronaut types what they feel in everyday English. The search understands synonyms, small typos,
   negations (*"no nausea"*) and severity words.
2. It ranks the most likely conditions.
3. It lists the medication in the kit: location, strength, rules, side effects, whether the crew can take it
   alone or needs flight-surgeon authorization, and conflicts with what is already taken.
4. It adds caution notes from the astronaut's latest vitals (SpO₂, blood pressure, heart rate, temperature).
5. The symptoms can be **saved to the medical record**, and the 3D organ model reacts to them.

Not a diagnosis.

---

## View 3 · Measurements

A simple measurement log: choose the astronaut and an indicator (heart rate, blood pressure, sleep …), enter
the value and optional notes, and save it to the crew database. The history table updates immediately and the
new values feed the 3D body model and the health assessment.

---

## Optional categories (sidebar)

| Category | Content |
|---|---|
| 📡 **Communications** | A code-locked crew-to-crew chat: enter a callsign and a shared channel code; refreshes every 2 s |
| 🧠 **Mental health** | Three tabs: **🎙 Voice & breathing** (live microphone analysis in the browser, nothing is recorded; breathing rate and voice stress against a personal calm baseline, with calming music generated live), **📝 Daily check-in** (short screening scales PHQ-2, GAD-2, PSS-4 plus sleep and energy), **📈 Prognosis & trends** (daily and monthly health index, improved / stable / declined, 7-day trend). A recorded `.wav` can also be analysed |
| 🚀 **Rocket · Orbital defense grid** | A small arcade mini-game in the ICHOR style |
| ☢ **Fictional radiation data** | A clearly labelled simulated 3D organ scan (cell-level exposure, mean exposure per organ) |

The microphone needs `https://` or `localhost`, and the browser will ask for permission.

---

## Run it locally

Python **3.11 or newer** (Streamlit Cloud: choose 3.12).

```bash
cd FINAL_PROJECT_1
pip install -r requirements.txt
python setup_db.py        # only if database/nasaspacechallenge.db is missing
streamlit run main.py
```

The app opens at `http://localhost:8501`.

To run the algorithms without the app:

```bash
python run_all.py nolive      # every algorithm once, ~1 minute
python live/06_live.py live   # live forecast every 5 minutes (Ctrl+C to stop)
```

> The trained model `models/forecast_models.pkl` opens only with the same scikit-learn version it was
> trained with (`scikit-learn==1.9.1` in `requirements.txt`).

---

## Deploy on Streamlit Community Cloud

1. Push the repository to GitHub.
2. On [share.streamlit.io](https://share.streamlit.io) choose **Create app** and select the repository and branch.
3. Set **Main file path** to `FINAL_PROJECT_1/main.py` and Python to **3.12** in the advanced settings.
4. Deploy. Streamlit installs `FINAL_PROJECT_1/requirements.txt`.

The bundled SQLite database lives inside the hosted app, so new measurements, symptoms and check-ins are
**not kept** after a rebuild or restart.

---

## Repository structure

```
FINAL_PROJECT_1/
├── main.py                  the ICHOR Streamlit app (sidebar, views, dashboard)
├── orion_3d.py              Orion 3D model + radiation map + ML forecast button
├── ichor_3d_panel.py        3D body model, radiation chooser, storm-detection panel
├── ichor_radiation.py       storm detection and dose integration from the database
├── ichor_health.py          organ-level health assessment from the measurements
├── ichor_search.py          Symptom Search view
├── ichor_symptoms.py        NASA Medical Kit 3.001 and symptom → condition map
├── ichor_measurements.py    Measurements view
├── ichor_mind.py            mental-health panel (check-in, trends, prognosis)
├── ichor_voice_monitor.py   live voice & breathing monitor (runs in the browser)
├── ichor_calm_audio.py      calming audio for the voice monitor
├── real_data.py             published thresholds, scenarios, playbooks, sources
├── run_all.py               runs every algorithm in order (Demo button)
├── setup_db.py, create_db.py, fill_db.py   build the crew database
├── database/                SQLite databases, schema and seed data
├── models/                  trained forecast model used by the app
├── orion_pics/              figures for the PICTURES gallery
├── forecast/                solar-storm forecast: download, detection, training, prediction
├── live/                    live forecast with NOAA data every 5 minutes
├── shielding/               dose per room of Orion and best room per astronaut
├── warning/                 crew alert, crew routes, 14-day mission
├── health/                  organ dose, biomarkers, anomaly detection, health cards
├── gis/                     floor plans and ArcGIS export
├── app/                     plain-words viewer for the algorithm results
├── ALGORITHMS.md            every algorithm in detail
└── requirements.txt
```

Every algorithm writes its result twice: as data, and as a `..._readable.json` in plain words with the same
shape (`level`, `headline`, `what_to_do`, `why`, `crew`). The app reads these files to show the results.

---

## Behind the app (algorithms, briefly)

- **Storm forecast**: Ridge and logistic regression on the proton flux of the first 30 minutes of a storm,
  trained on 301 solar storms. The alert uses the worst case (forecast + 1.65 × model error).
- **Shielding**: proton physics (NIST PSTAR) through the walls and the body; the safest room is found with
  Dijkstra on the doors, counting the dose on the way.
- **Health**: z-score against each astronaut's own baseline (alert after 2 unusual measurements in a row) and
  an Isolation Forest for several small changes together.

Full details in [`FINAL_PROJECT_1/ALGORITHMS.md`](FINAL_PROJECT_1/ALGORITHMS.md).

---

## Data: what is real, what is simulated

| Real, published | Simulated or team-defined |
|---|---|
| NASA-STD-3001 dose limits (250 mSv per event, 600 mSv career), NASA HRP, NLM REMM, NHLBI reference values | Day-by-day scenario values in the dashboard |
| Solar storms 1986–2017 (GSEP catalog), GOES proton flux (NOAA NCEI), live flux (NOAA SWPC) | Astronaut health records in the database (demo data) |
| NIST PSTAR proton range and stopping power | Crew movements and room dosimeters |
| NASA Medical Kit 3.001 contents | Orion interior layout and per-room shielding factors |
| Orion dimensions (NASA / ESA / Lockheed Martin / Airbus) | Crew Integrity Index, early-warning thresholds, mental-health index |

The full list with links is in the app (**📚 Data sources**) and in `real_data.py`.
