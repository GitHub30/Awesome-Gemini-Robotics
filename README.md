<div align="center">
<img width="3334" height="992" alt="image" src="https://github.com/user-attachments/assets/32195746-89c3-4e33-a93d-00a7f0642f79" />

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
</div>

# Awesome Gemini Robotics

A curated, community-maintained gallery of **Gemini Robotics-ER 1.5** (embodied reasoning, VLM) examples and prompts you can copy-paste into your own projects. Each card includes a **representative image** and a **ready-to-use prompt** (often JSON-friendly), following the style of “Awesome Nano Banana Images.”
If you build something cool, send a PR!

> **What is Gemini Robotics-ER 1.5?**
> It’s Google’s state-of-the-art embodied reasoning model for robotics. ER 1.5 excels at **spatial understanding (2D points, boxes, trajectories)**, **long-horizon task planning**, **temporal reasoning on video**, **tool use (e.g., Google Search)**, and **success/progress estimation**. You can call it directly via Google AI Studio / Gemini API and orchestrate your own robot APIs or VLAs. ([Google Developers Blog][1])

---

## Contents

* [Quick start](#quick-start)
* [Use cases (10+)](#use-cases)
* [Contributing](#contributing)
* [License & image attribution](#license--image-attribution)

---

## Quick start

> Minimal Python sketch with the Gemini API (model name may appear as `gemini-robotics-er-1.5-preview` in early access):

```python
from google import genai
from google.genai import types

client = genai.Client()
MODEL_ID = "gemini-robotics-er-1.5-preview"

prompt = """
Point to no more than 10 items in the image.
Return [{"point":[y,x],"label":"<name>"}] with y/x normalized to 0-1000.
"""

img_bytes = open("scene.jpg","rb").read()
res = client.models.generate_content(
    model=MODEL_ID,
    contents=[types.Part.from_bytes(data=img_bytes, mime_type="image/jpeg"), prompt],
    config=types.GenerateContentConfig(
        temperature=0.5,
        thinking_config=types.ThinkingConfig(thinking_budget=0)  # tune if needed
    )
)
print(res.text)
```

This mirrors Google’s official robotics overview (points, trajectories, orchestration, video, etc.). See docs for more examples (object tracking, packing lunch, calling custom robot APIs). ([Google AI for Developers][2])

---

## Use cases

> ✅ = includes a public demo image from Google’s docs/blog • 🧩 = bring your own image (placeholder path)

### 1) Kitchen scene: **Point-and-name objects** (2D points) ✅

![Kitchen pointing](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/unnamed-2_2.original.png)

**Prompt**

```
Point to the following items in the image: dish soap, dish rack, faucet, rice cooker, unicorn.
The points are in [y, x] format normalized to 0-1000.
Only include objects that are actually present in the image.
Return: [{"point":[y,x],"label":"<name>"}]
```

ER 1.5 returns precise, semantically grounded points; combine with your depth/pose stack to motion-plan. ([Google Developers Blog][1])

---

### 2) Temporal reasoning on video: **What happened when?** 🧩




https://github.com/user-attachments/assets/5c55cbea-6946-4d8f-8e33-a17c4f0d6dd7




**Prompt**

```
You are given a short robot-manipulation video.
Describe each step with precise timestamps, as JSON:
[{"start_timestamp":"MM:SS","end_timestamp":"MM:SS","description":"..."}]
```

ER 1.5 can reason about **order, causality, and intervals** (e.g., “green marker into tray, then blue/red pens into the cup”), and even zoom into sub-intervals. Use it to verify execution and annotate episodes. ([Google Developers Blog][1])

---

### 3) Coffee maker: **Plan a closing trajectory** (multi-point path) ✅

![Coffee-lid trajectory](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image1_PCWZQMD.original.png)

**Prompt**

```
Now I need to close the coffee maker.
Plot a trajectory of 8 points that indicates how the handle should move to close it.
Start from the handle. Points are [Y,X], normalized 0-1000.
Return:
[{"point":[Y,X],"label":"p0"}, {"point":[Y,X],"label":"p1"}, ...]
```

Use outputs to seed your motion planner (smoothing, collision checks). ([Google Developers Blog][1])

---

### 4) Coffee cleanup: **Where should I put the mug?** (affordance pointing) ✅

![Put mug away](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**Prompt**

```
I finished my coffee. Where should I put my mug now to clean up?
Return [{"point":[y,x],"label":"<best place>"}] with y/x normalized 0-1000.
```

Great for **affordance-aware placement** after task completion. ([Google Developers Blog][1])

---

### 5) **Trash sorting by local rules** (tool use + spatial grounding) 🧩

https://github.com/user-attachments/assets/503b20d6-1995-4e71-9164-9a79d6bf6a4c

**Prompt**

```
Use the Google Search tool to fetch my city’s latest recycling/compost rules.
Then, based on this image, explain how to sort each visible item into the right bin.
Point to each referenced item using [{"point":[y,x],"label":"<bin>"}], normalized 0-1000.
Return a step-by-step plan I can hand to my VLA controller.
```

ER 1.5 can **call tools** (e.g., Google Search) and output **interleaved text + points** for “spatially grounded” plans. ([Google Developers Blog][1])

---

### 6) **Re-organize my desk** from a reference photo (long-horizon plan) 🧩

**Prompt**

```
Given (A) my current desk photo and (B) a reference photo,
create a step-by-step reorganization plan.
For each step include target placements as [{"box_2d":[ymin,xmin,ymax,xmax],"label":"<item>"}],
normalized 0-1000, and a 1-2 sentence rationale.
```

Demonstrates **agentic planning** with **spatial grounding** over many steps. ([Google Developers Blog][1])

---

### 7) **Make room for a laptop** (choose what to move) 🧩

![Make room](https://ai.google.dev/gemini-api/docs/images/robotics/spatial-reasoning.png)

**Prompt**

```
Point to the single object I should remove to make room for my laptop.
Return [{"point":[y,x],"label":"<object to move>"}], normalized 0-1000.
```

A canonical **orchestration** example from the docs—reason about constraints and pick the minimal change. ([Google AI for Developers][2])

---

### 8) **Pack a lunch** with spatially grounded instructions ✅

![Packing lunch](https://ai.google.dev/static/gemini-api/docs/images/robotics/packing-lunch.png)

**Prompt**

```
Explain how to pack the lunch box and lunch bag from this image.
Point to each object you refer to:
[{"point":[y,x],"label":"<name>"}], normalized 0-1000.
Return a numbered step-by-step plan.
```

Outputs a **multi-step plan** plus points you can render or convert into pick-and-place calls. ([Google AI for Developers][2])

---

### 9) **Pick-and-place via your robot API** (function-calling) 🧩

![Blocks & bowl](https://ai.google.dev/gemini-api/docs/images/robotics/robot-api-example.png)

**Prompt**

```
First, locate the blue block and the orange bowl with:
[{"point":[y,x],"label":"<name>"}], 0-1000.
Then, using ONLY these functions:
  def move(x, y, high: bool)
  def setGripperState(opened: bool)
  def returnToOrigin()
Generate the precise call sequence to pick the block and place it in the bowl.
Explain each call briefly.
```

Docs show this pattern end-to-end (detect → plan → call your API). ([Google AI for Developers][2])

---

### 10) **Move the red pen to the organizer** (trajectory planning) 🧩

![Pen trajectory](https://ai.google.dev/gemini-api/docs/images/robotics/trajectories.png)

**Prompt**

```
Place a point on the red pen, then 15 points for its trajectory onto the organizer.
Label points by order "0"..."15". Return [{"point":[y,x],"label":"<step>"}].
```

Great for **teaching-by-trajectory** and downstream spline fitting / time-parametrization. ([Google AI for Developers][2])

---

### 11) **Laundry sorting by color** (multi-level thinking) 🧩

[![](https://i.imgur.com/2fqyZh6.png)](https://www.youtube.com/watch?v=eDyXEh8XqjM&t=34s)


**Prompt**

```
Sort laundry by color into two baskets: white vs non-white.
Return a short natural-language plan and for each garment a
{"point":[y,x],"label":"white|non-white"} with normalized 2D coordinates.
```

Official blog explains how Robotics 1.5/ER 1.5 think at task/subtask/micro-action levels for chores like **color-sorting laundry**. ([blog.google][3])

---

### 12) **Payload-aware pick** (safety & constraints) 🧩


**Prompt**

```
Plan a pick-and-place for the heaviest visible item
subject to a 1.0 kg payload limit and a 25 cm reach radius.
Explain the constraint checks and refuse if unsafe.
Return [{"point":[y,x],"label":"grasp"}] plus "reason".
```

ER 1.5 adds improved semantic safety and awareness of physical constraints; you must still implement hardware-level safety. ([Google Developers Blog][1])

---

### 13) **Human-robot natural language**: “Clean up the table” 🧩

![Table cleanup](https://storage.googleapis.com/gweb-developer-goog-blog-assets/images/image12_O0rW8PY.original.png)

**Prompt**

```
Given a table scene image, produce a numbered cleanup plan.
For each step, include a target placement point or 2D box and a note on success criteria.
Format:
{"step":n,"action":"...","target":{"point":[y,x] | "box_2d":[ymin,xmin,ymax,xmax]},"why":"..."}
```

ER 1.5 breaks down ambiguous commands into executable sub-goals and can call specialized grasping/VLA skills to act. ([Google Developers Blog][1])

---

### 14) **Cross-embodiment planning** (robot-agnostic steps) 🧩

[![Embodiments](https://github.com/user-attachments/assets/919be470-810b-4974-8fe9-2784623aabd2)](https://www.youtube.com/watch?v=9FV5ZYytkOQ)

**Prompt**

```
From this workstation scene, generate a robot-agnostic plan
expressed only in environment/affordance terms (no joint-space).
Assume it will be executed on ALOHA 2, Apptronik Apollo, or a Franka arm.
Return a step list with success checks and fallback options.
```

DeepMind highlights generalization across embodiments (e.g., ALOHA-2 → Apollo/Franka) to speed skill transfer. ([blog.google][3])

---

## Tips & patterns

* Prefer **normalized [y,x] in 0–1000** for points, or `[ymin,xmin,ymax,xmax]` for boxes. This keeps prompts model-friendly and implementation-agnostic. ([Google Developers Blog][1])
* Tune the **thinking budget** (latency/accuracy trade-off) depending on task complexity. ([Google Developers Blog][1])
* Interleave **text + points/boxes/trajectories** to produce “spatially grounded” plans your controller can execute. ([Google Developers Blog][1])
* Use **tool calls** (e.g., Search) to ground plans in local rules (recycling, kitchen policies, etc.). ([Google Developers Blog][1])

---

## Contributing

* Add a new folder under `cases/<short-name>/` with:

  * `README.md` (1–2 sentences + the prompt)
  * `image.jpg/png` (or link)
* Keep prompts **copy-runnable** and **JSON-friendly**.
* Cite your source(s) (prefer primary docs/blogs/videos).

---

## License & image attribution

* Text: MIT (this repo).
* Images marked ✅ are from Google’s public docs/blog and used here only as **demo references**; please check source licenses before redistributing. Replace 🧩 placeholders with your own images.

**Primary sources**

* Google Developers Blog — *Building the Next Generation of Physical Agents with Gemini Robotics-ER 1.5* (official prompts, images, capabilities). ([Google Developers Blog][1])
* Google AI for Developers — *Gemini Robotics-ER 1.5 (Robotics overview)* (points, trajectories, orchestration, code). ([Google AI for Developers][2])
* Google / DeepMind blogs — ER 1.5 launch context, embodied reasoning, cross-embodiment; JP blog includes laundry sorting example and partner robot references. ([Google DeepMind][4])

---

> 💡 Drop this file into
> `https://github.com/GitHub30/Awesome-Gemini-Robotics/blob/main/README.md`
> and start adding your own screenshots under `assets/` to replace 🧩 placeholders.

## 🙏 Acknowledge

The various cases in this repository rely on sharing from the AI community. Please allow us to express our sincere gratitude to all case contributors.

Thank you to the following users for sharing their amazing works. You can also visit their profiles to learn more:

- [@GoogleDeepMind](https://x.com/GoogleDeepMind)
- [@GeminiApp](https://x.com/GeminiApp)

*We cannot guarantee that all cases come from the original authors. If this causes you any inconvenience, please feel free to contact us for modifications.*

The cases we collect cannot cover all possible application scenarios. If you have other interesting discoveries 🔍, we welcome you to contact us to showcase more creativity 📧!

[![Star History Chart](https://api.star-history.com/svg?repos=GitHub30/Awesome-Gemini-Robotics&type=Date)](https://www.star-history.com/#GitHub30/Awesome-Gemini-Robotics&Date)

[1]: https://developers.googleblog.com/en/building-the-next-generation-of-physical-agents-with-gemini-robotics-er-15/ "Building the Next Generation of Physical Agents with Gemini Robotics-ER 1.5 - Google Developers Blog"
[2]: https://ai.google.dev/gemini-api/docs/robotics-overview "Gemini Robotics-ER 1.5  |  Gemini API  |  Google AI for Developers"
[3]: https://blog.google/intl/ja-jp/company-news/technology/gemini-robotics-15-ai/ "Gemini Robotics 1.5 を発表、AI エージェントを物理世界に"
[4]: https://deepmind.google/discover/blog/gemini-robotics-15-brings-ai-agents-into-the-physical-world/ "Gemini Robotics 1.5 brings AI agents into the physical world - Google DeepMind"
