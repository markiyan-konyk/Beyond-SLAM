# Beyond-SLAM

# Autonomous Room-Mapping Robot — Project Roadmap

**Goal of this document:** a complete, executable plan to take you from "I installed Ubuntu" to a portfolio piece that maximizes your odds of an Nvidia robotics co-op. Read the chat message first for the reasoning behind these decisions; this is the *what to do and when*.

---

## 1. The project, scoped precisely

**One-sentence pitch:**
> A simulated autonomous mobile robot that maps an indoor environment, navigates it autonomously, and locates a natural-language–specified object on command, using a custom CUDA kernel for an accelerated perception step and benchmarked against Nvidia's Isaac ROS stack.

**Why this framing wins:** it hits the Nvidia keyword list (C++, CUDA, ROS2, SLAM, mapping, localization, simulation, sensors, computer vision) *and* it's finishable, because the hard physical-world variables (the washroom bump, wheel slip, WiFi latency, 3D-print iteration) are removed for v1.

### Definition of Done — the July 1 minimum (non-negotiable core)
You have **succeeded** if, by July 1, your public repo contains:
1. A differential-drive robot spawning and driving in simulation (Gazebo or Isaac Sim).
2. A saved occupancy-grid map of a simulated room, produced by SLAM.
3. **One custom CUDA kernel** that accelerates a real sub-task, with a CPU-vs-GPU benchmark table and an Nsight profile screenshot.
4. A README with an architecture diagram and a 60–90s screen recording of the robot mapping.

That alone is a legitimate, interview-worthy project. Everything below this line is upside.

### Stretch goals (great if you reach them; otherwise → fall)
5. Autonomous point-to-point navigation in the map (Nav2).
6. Open-vocabulary object detection (YOLO-World / YOLOE) on the camera feed.
7. A minimal LLM agent: "go to the kitchen and find a mug" → sends a Nav2 goal + a detection prompt → reports back.
8. GPU-SLAM vs CPU-SLAM comparison (Isaac ROS `cuVSLAM`/`nvblox` vs `slam_toolbox`).

### Explicit NON-goals (protect yourself from scope creep)
- ❌ A self-balancing two-wheel robot (hard controls problem).
- ❌ Writing a full SLAM system in CUDA (reinvents `cuVSLAM`; infeasible).
- ❌ A generalist "find literally anything" robot (find *specified* objects; that's enough).
- ❌ Custom-designed 3D-printed chassis for v1 (buy a kit; print later if at all).
- ❌ Physical hardware before the simulation works.

> **The single most important rule:** a finished small thing beats an abandoned big thing. Every time you feel the urge to add a feature, ask whether it threatens the July 1 core. If yes, it goes in the fall backlog.

---

## 2. Architecture decisions (settled)

| Question | Decision | One-line reason |
|---|---|---|
| Drive type | Differential drive: 2 driven wheels + encoders + 1 caster | What ROS2 Nav2 assumes; simple odometry |
| Mapping sensor | 2D lidar (RPLidar A1/C1) + `slam_toolbox` | Cheapest robust room mapping; official ROS2 driver |
| Vision sensor | Cheap RGB webcam (or Pi cam) | Feeds open-vocab detector |
| Object detection | Open-vocabulary (YOLO-World / YOLOE) | Finds arbitrary objects by text prompt, no retraining |
| Camera mount | Fixed forward; rotate robot to scan | Pan/tilt is a fall upgrade |
| CUDA scope | One accelerated sub-task + benchmark | Demonstrable, finishable, exactly what Nvidia asks for |
| ROS2 version | **Jazzy Jalisco** on **Ubuntu 24.04** | LTS; the only modern distro Isaac Sim *and* Isaac ROS support |
| Simulator | Isaac Sim *if GPU qualifies*, else Gazebo | Same skills either way; Isaac Sim is the narrative bonus |
| Compute (physical, later) | Jetson Orin Nano Super if budget allows, else Raspberry Pi 5 | Edge inference = Nvidia's story; Pi = budget fallback |
| Agent | Minimal LLM tool-caller (you already know agents) | Plays to your existing strength |

**Sensor note:** Nvidia's `cuVSLAM` needs a *stereo* camera (or a RealSense), not a single webcam. For the budget path, lidar does the mapping and the mono webcam only does detection. A RealSense + `cuVSLAM` is a fall stretch if you get a Jetson.

---

## 3. Budget — three tiers

### Tier 0 — Simulation only (do this NOW)
**$0.** Everything runs on your laptop. This is your entire June.

### Tier 1 — Budget physical build (fall), Raspberry Pi
| Item | Approx. USD |
|---|---|
| Differential-drive chassis kit (2 motors + wheels + caster) | $25–40 |
| Motors **with encoders** (or add encoders) | $15–25 |
| Motor driver (TB6612 preferred over L298N) | $8 |
| Raspberry Pi 5 (8GB) + SD + power | $90–110 |
| RPLidar A1 or C1 | $80–110 |
| USB webcam | $20–30 |
| IMU (BNO055 recommended; improves odometry) | $10–30 |
| Battery pack + buck converter + wiring + standoffs | $25–40 |
| **Total** | **~$290–390** |

### Tier 2 — Nvidia-narrative build (fall), Jetson
Swap the Pi for a **Jetson Orin Nano Super (~$249)**. Net **+~$150** over Tier 1 → **~$440–540 total**. The Jetson runs Isaac ROS (`cuVSLAM`, `nvblox`) and lets you run your CUDA kernel *on the edge device* — the strongest possible story. Prioritize this purchase if you can; otherwise Tier 1 is completely fine and you note the Jetson as "future work."

> Budget tactic: don't buy anything until the sim works. Then buy Tier 1, get it driving + mapping physically, and only add the Jetson/RealSense if time and money allow.

---

## 4. What to learn, in order, with resources

You're learning four things at once (ROS2, C++, CUDA, a simulator). Don't learn them sequentially to completion — learn each *just enough to do the next step*, then deepen.

### A. ROS2 (highest priority — this is your bottleneck)
- **Articulated Robotics** (Josh Newans) YouTube series — *the* resource for this exact project. He builds a differential-drive robot, simulates it in Gazebo, adds lidar, runs SLAM and Nav2, then ports to real hardware. Follow this as your spine.
- Official ROS2 Jazzy tutorials (`docs.ros.org/en/jazzy/Tutorials.html`) — Beginner CLI + Client Libraries. Do the pub/sub, services, parameters, and TF tutorials.
- The Construct (`theconstruct.ai`) — browser-based ROS2 courses if you want guided practice without local setup pain.
- **Concepts you must understand:** nodes, topics, pub/sub, services, the TF tree, odometry, URDF, launch files, `colcon` workspaces.

### B. Navigation & SLAM stack
- `slam_toolbox` (Steve Macenski's repo + docs) — your mapping engine.
- **Nav2** (`docs.nav2.org`) — costmaps, planners, controllers, behavior trees. Use the official "Getting Started" sim bringup.

### C. CUDA (your differentiator)
- "An Even Easier Introduction to CUDA" — Nvidia Developer Blog. Start here, today.
- *Programming Massively Parallel Processors* (Hwu, Kirk, El Hajj) — the standard text; read the chapters on memory hierarchy and thread organization.
- freeCodeCamp's CUDA course (YouTube) for a fast hands-on pass.
- Nvidia DLI — "Fundamentals of Accelerated Computing with CUDA C/C++" (some free credits available).
- **Concepts Nvidia interviews probe:** occupancy, memory hierarchy (global/shared/registers), warp divergence, coalesced memory access, kernel profiling with **Nsight Compute/Systems**.

### D. C++ (just enough)
- `learncpp.com` — free, excellent. You don't need all of it; cover up through references, pointers, classes, headers/compilation, and `std::vector`. ROS2 C++ nodes + your CUDA host code are the goal.

### E. Simulator
- If RTX-qualified: Isaac Sim docs + the Isaac Sim "ROS2 bridge" tutorial; later Isaac Lab.
- If not: Gazebo (Harmonic) + `ros_gz` bridge. Articulated Robotics covers Gazebo directly.

### F. Perception & agent (you're already strong on agents)
- Ultralytics docs for YOLO-World / YOLOE (open-vocab modes).
- Grounding DINO repo if you want higher accuracy (heavier).
- Isaac ROS docs (`nvidia-isaac-ros.github.io`) for `cuVSLAM`/`nvblox` when you reach the GPU-vs-CPU comparison.

---

## 5. Documentation process (this is graded as heavily as the code)

Nvidia's research lab explicitly values open-sourcing and communication. Treat documentation as a first-class deliverable, not an afterthought.

1. **Public GitHub repo from day 1.** Commit small and often; the commit history should read like a build log.
2. **README** with: the pitch, an architecture diagram (hand-drawn or draw.io is fine), a "Results" section with your benchmark table, setup instructions, and embedded GIFs.
3. **A devlog.** A `DEVLOG.md` in the repo, or short LinkedIn posts, documenting decisions *and failures* ("tried X, it broke because Y, switched to Z"). This signals engineering maturity more than polished success does.
4. **Benchmark artifacts.** Your CPU-vs-CUDA table + Nsight screenshots. Numbers are what separate you from the pile.
5. **A 2–3 minute demo video** at the end. The single highest-leverage portfolio artifact. Screen-record the sim doing the full task; narrate the architecture.
6. **A short writeup / blog post** tying it together for your portfolio site, framed around "GPU-accelerated perception for an autonomous robot."

---

## 6. Week-by-week schedule (tight, honest)

> Reality flag: learning ROS2 + C++ + CUDA + Nav2 + a simulator in ~3 weeks is a heavy load. The schedule front-loads the **core** (Definition of Done items 1–4). Treat stretch items as "pull forward only if ahead." Falling to the fall on stretch items is **planned**, not failure.

### Days 1–3 (Jun 9–11) — Foundations & setup
- [ ] Confirm Ubuntu **24.04**; install **ROS2 Jazzy** (desktop).
- [ ] Check your GPU against Isaac Sim requirements → decide **Isaac Sim vs Gazebo**.
- [ ] Create the GitHub repo + `colcon` workspace; commit a README skeleton.
- [ ] ROS2 beginner tutorials: nodes, topics, pub/sub, services. Start Articulated Robotics.
- [ ] Write your *first* CUDA kernel (vector add) just to confirm toolchain + GPU work.
- **Milestone:** ROS2 installed, a node talking to a node, repo live, CUDA compiles.

### Week 1 (Jun 12–18) — Robot in simulation
- [ ] Get a differential-drive URDF spawning in your simulator.
- [ ] Teleop it (keyboard). Understand the TF tree and odometry.
- [ ] Add a simulated lidar + camera; confirm they publish.
- [ ] Keep grinding ROS2 fundamentals as needed.
- **Milestone:** robot drives in sim, sensors publishing, TF correct.

### Week 2 (Jun 19–25) — Mapping + the CUDA centerpiece
- [ ] Run `slam_toolbox`; drive the robot; **save a map** of a simulated room.
- [ ] **CUDA, in parallel:** choose your sub-task (occupancy-grid update, point-cloud voxel downsample, image undistortion, or costmap inflation). Implement it as a kernel.
- [ ] Benchmark CPU vs GPU; capture an **Nsight** profile; write the results table.
- **Milestone:** saved map + a working custom CUDA kernel with a real speedup number. *(This completes the Definition of Done core, items 1–3.)*

### Week 3 (Jun 26–Jul 1) — Navigation, detection, and the writeup
- [ ] Bring up **Nav2**; send the robot to a goal pose autonomously in the map. *(stretch 5)*
- [ ] Drop in **YOLO-World/YOLOE** on the camera; detect a prompted object. *(stretch 6)*
- [ ] Wire a **minimal agent**: text command → Nav2 goal + detection prompt → report. *(stretch 7)*
- [ ] **DOCUMENT:** finalize README + architecture diagram + benchmark section; record the **demo video**.
- **Milestone:** documented repo + demo video. Ship it. *(Completes Definition of Done item 4; stretch items as far as you got.)*

### July — Travel
Repo is parked in a clean, finished state. No pressure. Optionally read CUDA theory / watch Isaac ROS talks on flights.

### August–September — Physical build & polish (for the application window)
- [ ] Buy Tier 1 (or Tier 2/Jetson) hardware. Assemble the differential-drive base.
- [ ] Port the sim stack to real hardware: real lidar → `slam_toolbox` → map your actual room.
- [ ] Handle real-world issues now (the bump, wheel slip, the IMU, WiFi or on-board compute).
- [ ] If Jetson: run **Isaac ROS `cuVSLAM`/`nvblox`**; do the **GPU-vs-CPU SLAM comparison**. *(stretch 8)*
- [ ] Re-record the demo with the real robot; write the final portfolio blog post.
- **Milestone:** sim + real, documented, benchmarked — ready to attach to applications as they open.

---

## 7. Quick-reference risk list

- **Biggest risk:** scope creep → nothing finished. Defend the July 1 core ruthlessly.
- **Second risk:** ROS2 setup rabbit holes (DDS, environment sourcing, version mismatches). Pin to Jazzy + Ubuntu 24.04 exactly; use the official install steps verbatim; don't mix distros.
- **GPU risk:** if your laptop can't run Isaac Sim, that's fine — Gazebo teaches the same ROS2/Nav2/SLAM skills. Don't lose a day fighting Isaac Sim if it won't cooperate.
- **CUDA risk:** keep the kernel *bounded*. One well-profiled kernel with a clean speedup beats a half-finished ambitious one.
