# Smart-X Part 2: Video Demo Script

**Target length: 7 to 8 minutes.** Keep to the order below because each step sets up
the next one. **ACTION** is what you click; **SAY** is what you read or paraphrase.

---

## Before you press record (5 minutes)

- [ ] Start **Docker Desktop**, then in `Smart-X` run `docker compose up --build`
      (wait until both containers are up).
- [ ] **Restart the API once** so the demo starts from a clean state:
      `docker compose restart api`. This resets the alerts, the undo stack and the
      seeded learning history.
- [ ] Open http://localhost:3000 in a browser with **no other tabs**. Zoom to 100% or 110%.
- [ ] **Sign up** with a throw-away email beforehand so you know the password, then
      **sign out** so the video can start on the landing page.
- [ ] Have these ready in a second window, to flash on screen: the folder
      `Smart-X/backend/Smart-X.Api/DataStructures/`, `TelemetryService.cs`,
      `PredictionEngine.cs`.
- [ ] Close notifications. Use a decent microphone and speak slowly.

> **Tip:** the Command Stream page only produces data while it is open (the simulator
> lives in the page). Open it for about 10 seconds before the Ingestion tab so the
> devices show as online.

---

## 0:00 Introduction (20 s)

**ACTION:** Landing page on screen.

**SAY:**
"Hi, I'm [name], student number [number]. This is Smart-X Part 2, Real-Time Processing
and Device Management. Smart-X is an IoT operations console: devices send telemetry to
a .NET 10 API, and a React dashboard shows live data, raises critical alerts, lets an
operator send commands with undo, and suggests the next action from past behaviour."

---

## 0:20 Landing page and authentication (30 s)

**ACTION:** Point at the page, click **Sign up**, show the register screen, click
**Back to home**, click **Log in**, sign in with your account.

**SAY:**
"This is the landing page. Authentication uses a real service, Firebase Authentication,
so there are no hard-coded usernames or passwords. Sign-up and login are validated
in the browser first, then checked by Firebase. I'll log in now."

---

## 0:50 Navigation (30 s)

**ACTION:** Show the sidebar. Click **Command Stream**, then **Telemetry Ingestion**,
then back. Hover the greyed-out **Mesh Topology** item.

**SAY:**
"After login the sidebar shows all three modules. Telemetry Ingestion is Part 1.
Command Stream is Part 2, and it works. Mesh Topology is visible but greyed out because
it belongs to the final PoE. When I switch between tabs nothing is lost, because both
pages stay mounted, so charts, timers and the simulator keep running and nothing reloads."

---

## 1:20 Part 1 is still here: device import (45 s)

**ACTION:** On **Telemetry Ingestion**, scroll to **Import Devices from File**.
Choose `Smart-X/sample-data/devices.txt` and press **Import devices**. Point at the
green and red lines, then scroll to the sensor grid.

**SAY:**
"Part 1 is still included. This is the device file import: I upload a .txt list of
devices. The API reads it and validates every line the same way as manual registration.
Six devices were imported and three lines were rejected, each with the reason, such as
an invalid MAC address, an unknown category or a duplicate ID. The grid and the totals
have already updated."

---

## 2:05 The Command Stream page (60 s)

**ACTION:** Click **Command Stream**. Slowly move the mouse over the five counter cards,
the device grid, then type `pump` in the search box, then clear it. Use the **category**
and **alert state** dropdowns once.

**SAY:**
"This is the Real-Time Command Stream. The five cards at the top show the data
structures live: the telemetry queue, the critical priority queue, the set of
disconnected nodes, the set of active alerts, and the command undo stack.
Below is the device grid. I can search by name, location or MAC address, and filter
by category or by alert state. The data comes from a simulator that posts readings to
the real API endpoints every two seconds, exactly as ESP32 devices would."

---

## 3:05 Alert and the Predictive Action Engine (60 s)

**ACTION:** Scroll to **Device Simulator** and press **💧 Simulate dry soil**.
Scroll back up to the **Suggested Actions** and **Automated Insights** panels and the
**Critical Alert Feed**. Open **Learned patterns**.

**SAY:**
"Now I'll make a greenhouse soil sensor dry out. Moisture drops below 20 percent and a
critical alert is raised, once. The Automated Insights panel flags the out-of-range
reading before I search for anything. And the Suggested Actions panel now recommends
'Search for Pump A' and 'Run PumpOn on PUMP-001', with a confidence score and the
evidence. These come from recorded history: the engine counts which action followed which
alert in the past. It has seen this low-moisture alert followed by a search for Pump A
and then the pump being switched on. If I open Learned patterns, the raw counts are there."

---

## 4:05 Command, effect and undo (60 s)

**ACTION:** Click **Run** on *Run 'PumpOn' on PUMP-001*. Watch the *Active alerts* card
and the moisture chart. **Wait until the alert clears (about 6 seconds)**, then
immediately press **↶ Undo last command**. Wait a few seconds for the alert to come
back, then press **Undo** again.

**SAY:**
"I'll accept the suggestion and switch the pump on. The soil recovers and once it is
above 20 percent the alert clears. This command went onto a stack. If I press Undo, the
last command is popped and reverted, the pump goes off, the soil dries again and the
alert is raised again. If I press Undo a second time the stack is empty and the app says
'Nothing to undo' instead of crashing, because I use TryPop."

> **Timing:** press Undo within about 15 seconds of Pump On, while the soil is still
> recovering. If you wait too long the soil is fully recovered and the alert will not return.

---

## 5:05 No duplicate alerts (30 s)

**ACTION:** Press **⚡ Simulate power spike**. Point at the *Critical Alert Feed* and
*Active alerts*.

**SAY:**
"This button sends three identical power spike packets in a row. The alert feed shows
only one alert, not three. That is the HashSet: adding a duplicate returns false, so
the same alert is never processed twice."

---

## 5:35 Queue and priority queue: the stress test (60 s)

**ACTION:** Scroll to **Queue Stress Test**. Press **🚀 Run stress test** and keep the
panel in view until it finishes. Point at the chart rising and falling, then at the
processing log line that says *processed FIRST*.

**SAY:**
"This is the proof for the queues. The test sends 150 normal readings at once, and then
one critical power spike last. The readings go into a first-in first-out queue and a
background worker processes about fifty a second, so a backlog builds, you can see the
queue depth chart climb and then drain. But look at the processing log: the power spike
was sent last, yet it was processed first, with over a hundred readings still waiting.
That is the priority queue letting a critical alert bypass the normal queue."

---

## 6:35 Code walkthrough (75 s)

**ACTION:** Switch to your editor. Show the `DataStructures` folder, then briefly open
`TelemetryService.cs` (the `ProcessTelemetryAsync` and `ProcessPendingAsync` methods)
and `PredictionEngine.cs` (the `Record` method).

**SAY:**
"Every data structure has its own purpose-named class. TelemetryQueue is a Queue.
CriticalAlertQueue is a PriorityQueue where severity one comes out first.
CommandUndoStack is a Stack. DeviceRegistry holds two Dictionaries, keyed on node ID
and MAC address, so a device is found instantly when a packet arrives instead of looping
through a list. SensorTimeline is a SortedDictionary keyed on timestamp, so charts are
always in order even if packets arrive out of order. DisconnectedNodes holds the
HashSets for disconnected nodes and active alerts.
In TelemetryService a packet is looked up in the dictionary, queued, checked against the
alert rules, and if it is dangerous and the HashSet allows it, it goes on the priority
queue. The background worker processes critical alerts first, then a batch of normal
readings. And in PredictionEngine, every alert, search and command is recorded, and a
dictionary of dictionaries counts how often one action follows another. Confidence is
the number of times it followed divided by the number of times the trigger happened."

---

## 8:00 Wrap-up (30 s)

**ACTION:** Show the GitHub repository page (commit history and README), then the
`docker-compose.yml`.

**SAY:**
"Everything is on GitHub with a full commit history and a README that explains how to
run it with Docker, Visual Studio or the command line, plus step-by-step instructions to
test each queue, dictionary and set, including how to simulate a high-priority alert.
The whole system starts with one command, docker compose up. Thank you for watching."

---

## Rubric cross-check (do not read out)

| Rubric item | Shown at |
|---|---|
| Menu and navigation, state kept, Part 1 included (30) | 0:50 and 1:20 |
| Stacks, queues, priority queue (15) | 4:05 undo, 5:35 stress test |
| Dictionaries and sorted dictionary (15) | 2:05 search, 6:35 code (and README 6.6 and 6.7) |
| Sets: no duplicate alerts (10) | 5:05 |
| Predictive engine and suggestions panel (30) | 3:05 |
| GitHub, README, Docker | 8:00 |

## If something goes wrong while recording

| Problem | Fix |
|---|---|
| No suggestion appears | `docker compose restart api` resets the seeded history; then press **Simulate dry soil** again |
| Alert does not return after Undo | Undo was pressed too late; run **Simulate dry soil** again and press Undo sooner |
| Devices show Disconnected | They were silent for over 60 s; keep the Command Stream tab open so the simulator runs |
| Chart is empty | Click a device card to select it, then wait 2 seconds |
