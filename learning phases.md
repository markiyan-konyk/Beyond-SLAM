# Learning Curriculum — Autonomous Robot Project (All-Nvidia Track)
 
The full map of what to learn, ordered to track your build. This is the *map*, not a syllabus you finish before starting — the project forces you to learn the critical-path subset first, and the rest deepens over months.
 
## How to use this
- **Interleave, don't pre-learn.** Learn each module just enough to clear the next build checkpoint, then move. Come back to deepen later.
- **Depth markers:** 🟢 = minimum needed before you can proceed to the next checkpoint. 🔵 = deepen later, as the project demands it.
- **Concept vs Tool:** *Concept* = the theory (what is SLAM, what is an occupancy grid). *Tool* = how to operate the software (ROS2, Isaac Sim). You need both, in parallel.
- Checkpoint references (C0–C4) map to the All-Nvidia Track roadmap.
---
 
## Module 0 — What you already have (foundations)
 
**C++** — `learncpp.com`. Cover through: references/pointers, classes, headers & the compilation model, `std::vector`, smart pointers.
🟢 Enough for ROS2 C++ nodes + CUDA host code. You don't need templates-deep mastery yet.
 
**CUDA** — the official CUDA C++ Programming Guide is a *reference*, hard to learn from cold. Pair it:
- *An Even Easier Introduction to CUDA* (NVIDIA Developer Blog) — gentle first kernels.
- *Programming Massively Parallel Processors* (Hwu, Kirk, El Hajj) — the memory-hierarchy/optimization mindset.
- freeCodeCamp CUDA course (YouTube) — fast hands-on pass.
→ See Module 8 for the applied/profiling layer.
---
 
## Module 1 — Linux + Docker  *(Tool)* → C0
 
Needed because Isaac ROS lives in a Docker dev container.
- Any "Linux command line basics" tutorial: bash, `apt`, paths, environment sourcing, `ssh`.
- A 1-hour Docker intro (concepts: image vs container vs volume, GPU passthrough via `nvidia-container-toolkit`).
- Then the Isaac ROS dev-container setup docs.
🟢 You can run a container, mount a volume, and see the GPU with `nvidia-smi` *inside* the container.
---
 
## Module 2 — ROS2, the framework  *(Tool + Concept)* → C0
 
**Concepts:** the ROS2 graph (nodes, topics, services, actions, parameters), **TF2** (coordinate frames & transforms), **URDF** (robot description), launch files, `colcon` workspaces.
 
**Hands-on, in order:**
1. **Official ROS2 Jazzy Tutorials** — `docs.ros.org/en/jazzy/Tutorials.html`. Beginner: CLI tools → **turtlesim** ("making the turtle") → writing C++ nodes → parameters → the TF2 intro/tutorials.
2. **Articulated Robotics** (Josh Newans) — `articulatedrobotics.xyz` + YouTube. His "Building a Mobile Robot" series is the spine for this project: URDF, `ros2_control`, simulation, lidar, SLAM, Nav2.
3. *Optional structured course:* The Construct "ROS2 Basics" (browser-based, no install) **or** Edouard Renard's "ROS2 for Beginners" (Udemy, 3 parts).
🟢 Comfortable with topics, the TF tree, and **reading/modifying a launch file**. This is non-negotiable before any Isaac tutorial — they assume it.
 
---
 
## Module 3 — SLAM, the concept  *(Concept)* → interleave with C1/C2
 
Without this, cuVSLAM and nvblox are opaque black boxes. Learn the theory in parallel with running them.
 
**Cyrill Stachniss (free, the gold standard)** — channel: `youtube.com/c/CyrillStachniss`; materials: `ipb.uni-bonn.de/teaching`.
- *Online Training: Mobile Robotics* (2020) — Basics Block + Mobile Robotics Block. Newcomer-friendly; start here.
- *SLAM Course* (2013/14 playlist — best audio) — deeper: Bayes filter, EKF, particle filter, FastSLAM, **graph-based SLAM / pose graphs**, **ICP / scan matching**, occupancy-grid mapping.
**Visual SLAM specifically (to understand cuVSLAM):**
- *Introduction to Visual SLAM: From Theory to Practice* — "slambook2", free on GitHub (`gaoxiang12/slambook-en`). Features, epipolar geometry, PnP, bundle adjustment, pose graphs, visual-inertial odometry.
**Reference textbook:** *Probabilistic Robotics* (Thrun, Burgard, Fox) — read selectively (occupancy grids, Bayes filters, GraphSLAM).
 
🟢 Stachniss "Introduction to SLAM" + occupancy-grid-mapping lectures — enough to start C1.
🔵 The visual-SLAM block (slambook2 ch. on VO/VIO) before you go deep on cuVSLAM; everything else as you go.
 
---
 
## Module 4 — Isaac Sim  *(Tool)* → C0/C1
 
**NVIDIA DLI free self-paced courses** — catalog at `learn.nvidia.com` (free with NVIDIA Developer Program registration; cloud GPU labs included):
- *Introduction to Robotic Simulations in Isaac Sim*
- *Assemble a Simple Robot in Isaac Sim* (mini)
- *Simulating Your First Robot in Isaac Sim*
- *Ingesting Robot Assets and Simulating Your Robot in Isaac Sim*
**Official docs** — `docs.isaacsim.omniverse.nvidia.com`: Getting Started + the **ROS2 Tutorials** (the `isaacsim.ros2.bridge` extension; the Nav2 / Nova Carter sample).
 
**OpenUSD** (Isaac Sim's scene format) — light touch: NVIDIA "Learn OpenUSD" intro only if scene editing confuses you.
 
**Cloud fallback:** Isaac Sim runs on cloud GPUs via **Brev** if your 8GB struggles.
 
🟢 You can navigate the GUI, load a scene, enable the ROS2 bridge, and drive a sample robot.
 
---
 
## Module 5 — Isaac ROS / cuVSLAM / nvblox  *(Tool)* → C1/C2
 
- **"Getting Started with Isaac ROS" learning path** — `docs.nvidia.com/learning/physical-ai/getting-started-with-isaac-ros` (structured intro to what Isaac ROS is and the dev workflow).
- **Isaac ROS docs** — `nvidia-isaac-ros.github.io`: dev-container setup, the **cuVSLAM quickstart** (run on the provided bag first), the **"Visual SLAM with Isaac Sim"** tutorial, the **nvblox** quickstart.
- **Depth (optional):** the cuVSLAM technical report (arXiv 2506.04359) once you want the internals.
🟢 Dev container running → cuVSLAM on the provided bag → cuVSLAM in Isaac Sim. (That's checkpoint C1.)
 
---
 
## Module 6 — Nav2, navigation  *(Tool + Concept)* → C2
 
- **Official Nav2 docs** — `docs.nav2.org`: Getting Started + Concepts (costmaps, global/local planners, controllers, **behavior trees**, recovery behaviors).
- Articulated Robotics' Nav2 integration videos (ties it back to your robot).
🟢 Bring up Nav2 on a saved map, send a goal pose, and understand the costmap + the TF/odom inputs it needs.
 
---
 
## Module 7 — Perception + agent  *(Tool)* → C3/C4
 
You already build agents, so this is light.
- **Open-vocabulary detection:** Ultralytics docs (YOLO-World / YOLOE), LearnOpenCV tutorials, the Grounding DINO repo.
- **VLM:** model docs (Qwen2-VL, Moondream) for local, or your chosen API's vision docs.
- **Agent loop:** focus on the tool-calling pattern — command → Nav2 goal + detection prompt → verify → report. (Use an API model during the sim phase to avoid fighting Isaac Sim for VRAM.)
🟢 Run a detector on a sim/webcam feed and wire one tool call that sends a Nav2 goal.
 
---
 
## Module 8 — Applied CUDA + profiling  *(Tool + Concept)* → C4, ongoing
 
Builds on Module 0.
- *An Even Easier Introduction to CUDA* (blog) → your first real kernels.
- *Programming Massively Parallel Processors* → memory hierarchy, tiling, shared memory.
- **DLI: Fundamentals of Accelerated Computing with CUDA C/C++** → hands-on labs.
- **DLI: Optimizing CUDA Codes with Nsight Profiling Tools** → exactly your benchmark workflow.
🟢 Write & launch a kernel, time it against a CPU baseline, profile with **Nsight Compute**, and iterate one kernel through versions (naive → shared memory → coalesced → fewer divergences) with the speedup recorded.
 
---
 
## Suggested sequence (how the modules interleave with the build)
 
| Phase | Learn (🟢 portions) | Build checkpoint |
|---|---|---|
| Days 1–4 | Mod 1 (Linux/Docker), Mod 2 (ROS2 → turtle → nodes/TF/launch) | C0 setup |
| Week 1 | Mod 4 (Isaac Sim basics), Mod 5 (Isaac ROS + cuVSLAM), Mod 3 (SLAM intro + occupancy grids) | C1 cuVSLAM map |
| Week 2 | Mod 6 (Nav2), Mod 3 (more) | C2 nav + nvblox |
| Week 3a | Mod 7 (perception) | C3 your scene + detection |
| Week 3b | Mod 8 (CUDA + Nsight) | C4 CUDA kernel + agent + writeup |
| Ongoing / fall | Mod 3 visual-SLAM depth, Mod 8 depth, slambook2 | physical sim-to-real |
 
---
 
## Not drowning
 
This is genuinely a semester's worth of material compressed against a 3-week sprint. You will **not** master all of it by July — and you don't need to. The project pulls the critical-path 🟢 subset to the front; the 🔵 depth happens over the following months and is what turns "I followed tutorials" into "I understand this." Judge a study session by whether it unblocked the next checkpoint, not by whether you finished a course.
 
