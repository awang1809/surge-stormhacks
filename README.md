# SURGE

**DEVPOST: https://devpost.com/software/surge-u2vh86**

**Bridging the communication gap between government bodies and citizens during floods.**

SURGE is a hackathon prototype that brings official instructions, regional risk information, mapped routes, and community reports into one flood-response platform. It gives government teams a shared workspace and residents a place to understand the latest guidance and ask for help.

## Why we built it

Inspired by the flash floods in Nepal, we focused on a communication gap: people facing a flood need clear, detailed information about what happens next. Knowing that a flood is happening does not answer the questions that matter in the moment: **Where should I go? Which roads should I avoid? What are officials asking me to do? How can I get help?**

We built SURGE to make those next steps easier to communicate. Government bodies can publish area-specific instructions, while citizens can access updates and send information from the ground back to response teams. Our goal is to connect official decisions with the people who need them, while helping officials understand what residents are experiencing.

## Features



### Government dashboard

- View regional risk and decision-support signals.
- Publish official instructions for specific areas.
- Review community reports and SOS requests.
- Track alerts, published instructions, and response activity.
- Compare walking and vehicle routes using the Rasuwa and Sunsari planners.



### Resident app

- Read the latest official instruction for an area and browse alert history.
- View mapped route options to candidate evacuation destinations.
- Submit field reports and SOS requests.
- Ask questions about published guidance through a chat interface, with multilingual voice support when providers are configured.



### Sensor risk and route planning

- Load simulated rainfall, river-level, and soil-moisture readings from JSON files.
- Convert readings into Low, Moderate, High, or Extreme risk categories; missing readings remain Unknown.
- Combine sensor risk with synthetic hazard geography within the assessed area.
- Find shortest mapped road paths to qualifying Low-risk destinations.
- Respect travel-mode access rules, mapped hazards, and blocked roads.



## How it works

1. **Officials publish guidance.** The backend stores area-specific instructions and makes them available to the resident app.
2. **Residents receive updates.** The app periodically checks for the latest instructions and presents them alongside alerts and maps.
3. **Residents report conditions.** Community reports and SOS requests flow back to the government dashboard.
4. **Maps support planning.** Prepared geographic datasets provide risk overlays and road networks for comparing route candidates.

The sensor model uses a fixed weighted calculation: rainfall contributes 45%, river level 40%, and soil moisture 15%, after normalization. Routing uses Dijkstra's algorithm to find the shortest available road paths. Sensor risk determines which destinations qualify; road access, closures, and hazard intersections determine which connections can be used.

## Tech stack


| Layer                   | Technology                                         |
| ----------------------- | -------------------------------------------------- |
| Frontend                | HTML, CSS, JavaScript                              |
| Maps                    | MapLibre GL JS and OpenStreetMap-derived geography |
| Backend                 | Python, FastAPI, Uvicorn                           |
| Local storage           | SQLite                                             |
| Cloud storage           | Snowflake                                          |
| Language classification | Gemini                                             |
| Speech services         | ElevenLabs                                         |
| Tests                   | pytest, Node.js test runner, Playwright            |




## Getting started



### Requirements

- Python 3.11 or newer.
- A modern web browser.
- Node.js and npm for the standalone map server and frontend tests.



### Run the full application

From the repository root, create a virtual environment:

```sh
python -m venv .venv
```

**macOS and Linux:**

```sh
.venv/bin/python -m pip install -r backend/requirements.txt
.venv/bin/python -m uvicorn app.main:app --app-dir backend --host 127.0.0.1 --port 8008
```

**Windows:**

```powershell
.venv\Scripts\python -m pip install -r backend/requirements.txt
.venv\Scripts\python -m uvicorn app.main:app --app-dir backend --host 127.0.0.1 --port 8008
```

Open [http://127.0.0.1:8008/](http://127.0.0.1:8008/).


| Page                 | Local URL                                                        |
| -------------------- | ---------------------------------------------------------------- |
| Home                 | [http://127.0.0.1:8008/](http://127.0.0.1:8008/)                 |
| Government dashboard | [http://127.0.0.1:8008/gov](http://127.0.0.1:8008/gov)           |
| Resident app         | [http://127.0.0.1:8008/victim](http://127.0.0.1:8008/victim)     |
| Rasuwa planner       | [http://127.0.0.1:8008/rasuwa/](http://127.0.0.1:8008/rasuwa/)   |
| Sunsari planner      | [http://127.0.0.1:8008/sunsari/](http://127.0.0.1:8008/sunsari/) |
| SFU Burnaby walking map | [http://127.0.0.1:8008/sfu/](http://127.0.0.1:8008/sfu/) |
| API documentation    | [http://127.0.0.1:8008/docs](http://127.0.0.1:8008/docs)         |


The local demo runs without API keys or an `.env` file. On first startup, the backend creates `backend/local.db` and seeds an empty database with bundled sample instructions, reports, and events. The database is gitignored and persists local changes between runs.

### Optional integrations

To configure Snowflake, Gemini, or ElevenLabs, copy [.env.example](.env.example) to `.env` and fill in the relevant settings. See the [backend documentation](backend/README.md) for provider behavior and configuration. Keep credentials and private keys out of version control.

To manually check a configured Snowflake key-pair connection:

```sh
python backend/scripts/check_snowflake.py
```



### Run the map planners independently

The planners can run without the backend or API keys:

```sh
cd frontend/rasuwa
npm ci
npm run serve
```

Open [Rasuwa](http://127.0.0.1:3010/rasuwa/) or [Sunsari](http://127.0.0.1:3010/sunsari/). Serve the pages over HTTP rather than opening the HTML files directly.

The same server hosts the [SFU Burnaby walking map](http://127.0.0.1:3010/sfu/), a basic campus planner with eight landmarks, start/destination selection, and mapped walking distances. Open it from the government route planner's **SFU Burnaby** button or either regional planner's sidebar. See the [campus map documentation](frontend/sfu/README.md) for data and routing details.

## Project structure

```text
backend/
  app/                 API routes, services, schemas, storage, and demo seeds
  scripts/             Integration utilities
  tests/               Backend tests
frontend/
  index.html           Home page
  gov.html             Government dashboard
  victim.html          Resident app
  rasuwa/              Shared map and routing modules, regional data, and tests
  sunsari/             Sunsari map page and regional data
  sfu/                 SFU Burnaby campus walking map and bundled path data
.env.example           Optional integration settings
render.yaml            Deployment configuration
```



## Testing

Backend tests, from the repository root on macOS or Linux:

```sh
.venv/bin/python -m pip install -r backend/requirements-dev.txt
cd backend
../.venv/bin/python -m pytest
```

Use `.venv\Scripts\python` and `..\.venv\Scripts\python` for the equivalent Windows commands.

Map and routing tests, from the repository root:

```sh
cd frontend/rasuwa
npm ci
npm test
```

For browser tests and sensor-data preparation, see the [Rasuwa documentation](frontend/rasuwa/README.md). The [Sunsari documentation](frontend/sunsari/README.md) covers its dataset and offline rebuild process.

## Demo scope

SURGE is a hackathon prototype. Sensor readings are simulated, risk scores are deterministic heuristics, and route candidates use bundled mapped geography and scenario constraints. A Low-risk label does not establish safety, and route distance excludes access between location markers and mapped road endpoints.

Destinations still require field verification of capacity, access, structural condition, and suitability. The optional language classifier helps interpret questions about published guidance; it does not author official evacuation directives. The prototype is intended to demonstrate the communication workflow and planning tools, rather than provide live emergency guidance.

## Future improvements

- Connect verified live sensor feeds and official emergency data.
- Add verified shelter capacity, availability, and accessibility information.
- Improve access to instructions under poor network conditions.
- Expand supported regions and languages.
- Evaluate the communication workflow with residents and emergency-response teams.



## License

SURGE's original source code is licensed under the [MIT License](LICENSE).
Third-party libraries and geographic datasets retain their respective licenses.
The license text follows the [Open Source Initiative's MIT template](https://opensource.org/license/mit).

## Acknowledgments

Geographic data is derived from OpenStreetMap contributors.
