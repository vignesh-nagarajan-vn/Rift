<p align="center"><img src="docs/logo.svg" width="80" alt="RIFT logo"></p>

# RIFT

**🏆 2nd Place Overall at tidalBYTE 2026 Hackathon at Texas A&M.**

- Live Deployment: [rift-safety.netlify.app](https://rift-safety.netlify.app)
- Hackathon: [tidalBYTE '26](https://f26.tidaltamu.com/), tidalTAMU's 12-hour hackathon in the MSC on November 21, 2026

RIFT (Reality Intelligence & Future Testing) opens on a 3D map of Texas A&M. Pick a building and it becomes an evacuation digital twin with hundreds of simulated occupants, a spreading fire and drifting smoke. An NVIDIA Nemotron agent then plans the evacuation by running real simulations as tool calls, comparing the results, and committing the plan that beats unguided self-evacuation. Drop an obstacle, start a second fire or block an exit, and it replans on its own.

- **Team:** Vignesh Nagarajan, Srivanth Rudrangi, Anish Koduri, Priyansh Ravrani
- **Prompt:** apply AI and machine learning to real-world problems in ways that create meaningful interdisciplinary impact. The NVIDIA track asks for an agent doing real work, with Nemotron central to it.
- **Sponsors:** Jane Street, NVIDIA, Google Gemini, Adobe, ElevenLabs, Base44 and others
- **Datasets:** [OpenStreetMap](https://www.openstreetmap.org/) footprints, heights, entrances, footpaths, lawns and trees for the main campus, via the Overpass API
- **Stack:** React, TypeScript, Vite, three.js (React Three Fiber), Zustand, Web Workers, a Node proxy to NVIDIA's API
- **Run it:** `npm install`, put your build.nvidia.com key in `.env.local` as `NVIDIA_API_KEY`, then `npm run dev`. See [how it works](docs/how-it-works.md) for the model, the simulation and the architecture.
- **Original repo:** Srivanth's original repo is at [github.com/snr2753423/rift](https://github.com/snr2753423/rift), and this repo has been PRed into it.

## Why it matters

In a real fire, most people try to leave the way they came in, even when that is where the fire is. They also lose precious seconds working out what the alarm bell means before they move. A fire drill tests one plan, once a year, and never with real smoke.

RIFT puts you inside your own building, sets a fire beside the exit everyone uses, and lets you watch what today's procedure does to the people around you. Then NVIDIA Nemotron rewrites the plan by running dozens of full evacuations in about a minute. It can add a voice alarm and floor wardens, change signs, send rooms to other exits, and steer people around the smoke, and it cannot commit a plan that loses as many lives as today's.

In RIFT's campus drills, on Texas A&M's own building outlines, doors and floor counts, today's procedure loses **31 people across the first two floors of the original eight featured buildings**. A fixed voice alarm with signs to the other exits cuts that to **14**, measured headlessly with people's exit habit kept. Nemotron's tested plans go further: on MSC floor 1 live Nemotron found a plan that saves all 3 people lost in that run. Every plan can be downloaded as a posted-style evacuation plan PDF.

## Demo

Open the site, pick **MSC** (where tidalBYTE is happening), choose a floor, tap where you are, and sound the alarm. Then ask NVIDIA Nemotron for a better plan and compare.

| Campus map | The alarm in the MSC | Nemotron's impact |
|:-:|:-:|:-:|
| <img src="docs/screenshots/campus.jpg" width="270" alt="Night-time 3D map of Texas A&M with lit windows, lawns and trees; the intro explains RIFT in three steps beside a Start in the MSC button"> | <img src="docs/screenshots/drill-result.jpg" width="270" alt="MSC floor 1 seconds after the alarm: a red banner says a fire has broken out in the Ballroom while the step rail, legend and your maroon ring guide you"> | <img src="docs/screenshots/impact.jpg" width="270" alt="Impact card: Nemotron's plan saves 3 lives on this floor, gets 90 percent out 8 seconds sooner with 96 percent less smoke breathed, and lists the plan's changes"> |

Nemotron has three jobs. As **perception**, Nemotron VL reads a floor-plan image and returns rooms, exits and the likeliest fire source as validated JSON. As **planner**, it inspects the twin, proposes contrasting strategies, runs them in parallel on a Web Worker pool, reads the bottlenecks and commits a plan, with its reasoning streamed under a per-turn budget. As **replanner**, it re-tests its committed plan whenever the building changes. Every number it cites comes from a deterministic simulation it triggered. `commit_strategy` refuses any plan that scores worse than the baseline, and when today's procedure loses anyone, it refuses any plan that does not lower that number. When nobody is lost under either plan, RIFT says so and shows the time, smoke and crowding Nemotron saved instead of inventing a life saved.

Building footprints and entrances are real map data, but detailed floor plans for campus buildings are not public. The interior of each campus twin is a schematic generated from its footprint (corridor, rooms, exits at the mapped entrances), and it is scaled to the simulation's 64 × 40 grid. Lawns, sports fields and 65 trees on the map are OpenStreetMap data; the other trees on the lawns are a seeded decorative scatter. The simulation is a simplified agent-based model for comparing strategies, **not a certified safety prediction**. The deployed site reaches Nemotron through a Netlify Edge Function that keeps the key server-side; a clearly labelled offline fallback drives the same tools only when NVIDIA cannot be reached.
