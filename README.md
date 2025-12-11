# ImageTagger Studio – NASA AI Lab

**ImageTagger Studio** is an interactive notebook app built for the NASA AI Lab.  
It provides two complementary workspaces for exploring imagery with vision models and LLM-based reasoning:

- **Vision Reasoning Agent** – a semi-agentic pipeline that uses Moondream + GPT-4.1-mini to answer higher-level questions about safety, accessibility, terrain, equipment, logistics, and more.
- **Moondream Playground** – a low-level sandbox for directly probing a Moondream vision model via Query, Caption, Point, and Detect modes.

The notebook is designed to be run as a Gradio app from within Jupyter (or VS Code), so that NASA engineers and analysts can work with images without touching any Python code.

---

## Workspaces

### 1. Vision Reasoning Agent

**Goal:** Answer “NASA-flavored” questions about an image with transparent reasoning and a confidence score.

**Pipeline:**

1. **Base caption (Moondream)**  
   - The image is preprocessed (`exif_rgb`) and captioned with Moondream.

2. **Planner (OpenAI GPT-4.1-mini)**  
   - The planner sees only:
     - Overall caption  
     - User question  
   - It decides:
     - What the question is *really* about (intent)  
     - A coarse “profile” (safety, accessibility, terrain, equipment, logistics, general)  
     - Which object labels (up to 6 nouns) to detect  
     - Whether region-level captions would help  
   - Output is a structured JSON plan.

3. **Optional detection + region captions (Moondream)**  
   - For each planned label, run `MODEL.detect`.  
   - Optionally crop and caption a few regions (`region_caption`) to zoom in on key objects.

4. **Final reasoning (OpenAI GPT-4.1-mini)**  
   - A reasoning prompt receives:
     - Caption  
     - Planner intent + profile  
     - Detection summary  
     - Region captions  
     - User question  
   - It returns:
     - Bulleted reasoning  
     - A short, NASA-context answer.

5. **Confidence pass (OpenAI GPT-4.1-mini)**  
   - A final lightweight model call rates the answer:
     - Confidence 0–100  
     - Label (`low` / `medium` / `high`)  
     - One-sentence justification.

6. **UI formatting**  
   - Results are rendered as a Moondream-style panel with chips:
     - `AGENT` mode label  
     - Sections: `PROMPT`, `TOOLS`, `RESULT`, `CONFIDENCE`  
     - Response time metadata.

> If `OPENAI_API_KEY` is missing, the agent is disabled and a clear warning panel is shown.

---

### 2. Moondream Playground

**Goal:** Provide a direct, Moondream-style playground for experimenting with the vision model.

**Modes:**

- **Query**  
  - Ask arbitrary questions about the image.  
  - Optional “Reasoning” toggle: asks Moondream to briefly explain how it arrived at the answer.  
  - The code uses a small steering helper (`_steer`) to:
    - Encourage full-sentence answers  
    - Avoid low-information responses like “this” or “that”  
    - Retry once if the answer is too vague.

- **Caption**  
  - Generate image captions at three lengths:
    - Short (1 sentence)  
    - Normal (≈3 sentences)  
    - Long (2 short paragraphs)  
  - If Moondream does not support `length`, the notebook falls back to querying the model to rewrite/expand the base caption.

- **Point**  
  - Given an object label (e.g., `"helmet"`, `"grapes"`), call `MODEL.point`.  
  - Overlays glowing blue point markers using `draw_overlay`.

- **Detect**  
  - Given one or more comma-separated labels, call `MODEL.detect`.  
  - Draws red bounding boxes for all detections using `draw_overlay`.

**UI features:**

- **Sample image strip** for quick experimentation.
- **Suggestion chips** in Query mode, powered by `suggest_questions`, to help users formulate good prompts.
- Moondream-style result panels with `PROMPT`, `RESULT`, and timing metadata.

---

## Architecture Overview

### Preprocessing

- `exif_rgb(img, max_side=1024)`  
  - Converts images to RGB, fixes EXIF orientation, and downsamples the longest side to 1024 px to reduce payload and latency.

### Overlays

- `to_px_box` / `to_px_point`  
  - Convert normalized Moondream coordinates into pixel coordinates.

- `draw_overlay(base, detections, points)`  
  - Draws:
    - Red bounding boxes for detection results.
    - Blue glowing circles for point results.

### Panels & Layout

- `_panel_sections(mode, sections, meta, extra_chip=None)`  
  - Renders HTML result panels with:
    - Mode chip (`QUERY`, `CAPTION`, `POINT`, `DETECT`, `AGENT`)  
    - Section headers (`PROMPT`, `RESULT`, `TOOLS`, `CONFIDENCE`)  
    - Response time (`meta["ms"]`).

### Agent

- `run_vision_agent(image, question, detailed=True)`  
  - Orchestrates the 5-step pipeline described above.  
  - Returns a single HTML panel suitable for the “Results” area.

### Moondream Bridge

- `run(mode, img, question, reasoning, cap_len, labels)`  
  - Core handler for the Moondream playground modes.  
  - Returns:
    - Updated image (with overlays if needed)  
    - HTML result panel  
    - (Currently disabled) JSON “Show code” block.

---

## Setup

### 1. Clone and environment

```bash
git clone <your-repo-url>
cd ImageTagger-main  # or your repo root

# Create a virtual environment (optional but recommended)
python -m venv .venv
source .venv/bin/activate        # on macOS / Linux
# or
.venv\Scripts\activate           # on Windows

pip install -r requirements.txt
