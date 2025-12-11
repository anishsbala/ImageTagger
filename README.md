# 🌌 **ImageTagger Studio — NASA AI Lab**

### A next-generation vision analysis workspace for NASA engineers

Interactive • Agentic • Explainable • Moondream-powered

---

## 🚀 Overview

**ImageTagger Studio** is an interactive notebook application that provides two powerful workspaces for analyzing NASA imagery:

### **1. Vision Reasoning Agent**

A semi-agentic pipeline combining **Moondream vision models** with **GPT-4.1-mini** planning + reasoning to answer safety, logistics, terrain, and equipment-related questions with full explainability and confidence scoring.

### **2. Moondream Playground**

A hands-on environment for querying Moondream directly using:

* **Query**
* **Caption (Short / Normal / Long)**
* **Point**
* **Detect**

Each workspace includes a custom UI, overlays, sample images, suggestion chips, and Moondream-style result panels.

---

## 🧩 Features

### 🔍 **Vision Reasoning Agent**

A structured pipeline:

1. **Caption**
   Extracts an initial scene description using Moondream.

2. **Planner** (GPT-4.1-mini)
   Determines:

   * What the question is *really* asking
   * Relevant safety/logistics/terrain/equipment categories
   * What objects to detect
   * Whether zoom-in region captions are needed

3. **Detection + Region Captions**
   Runs Moondream sub-tools based on the plan.

4. **Final Reasoning**
   Produces:

   * Multi-step reasoning bullets
   * A concise NASA-context final answer

5. **Confidence Estimation**
   A final pass assigns a **0–100 confidence score** + short explanation.

6. **Panel Rendering**
   Outputs a Moondream-style HTML panel with:

   * PROMPT
   * TOOLS (planner outputs)
   * RESULT
   * CONFIDENCE
   * Response time

---

### 🧪 **Moondream Playground**

A low-level model probing environment with full UI polish.

#### **Query Mode**

* Freeform questions
* Optional reasoning toggle
* Smart answer steering + fallback retries
* Suggestion chips powered by image-specific Q-generation

#### **Caption Mode**

Choose caption length:

* Short → 1 sentence
* Normal → ~3 sentences
* Long → 2 short paragraphs

If Moondream doesn’t support a length, the notebook automatically rewrites/expands the caption using GPT.

#### **Point Mode**

* Find the location of an object (e.g., *helmet*, *grapes*)
* Draws glowing blue point overlays

#### **Detect Mode**

* Detect multiple labels
* Draws red bounding boxes similar to the official Moondream playground

---

## 🖼 UI Features

* NASA-branding inspired gradient UI
* Clean centered white card layout
* Sample image gallery
* Overlay rendering for detections + points
* Loading animations
* Fully responsive layout for notebook and Voila environments
* Hidden code sections for non-technical users

---

## 📦 Project Structure

```
ImageTagger/
 ├── ImageTaggerNotebook.ipynb     # Main notebook app
 ├── sample_images/                # Demo images
 ├── api.env                       # API keys (NOT committed)
 ├── requirements.txt
 ├── README.md
 └── ... supporting utilities
```

---

## ⚙️ Setup Instructions

### **1️⃣ Create / activate your environment**

```bash
python -m venv .venv
source .venv/bin/activate        # macOS/Linux
.venv\Scripts\activate           # Windows
```

### **2️⃣ Install dependencies**

```bash
pip install -r requirements.txt
```

### **3️⃣ Configure API keys**

Create a file named **`api.env`**:

```env
MOONDREAM_API_KEY="your_moondream_key_here"
MOONDREAM_ENDPOINT="http://localhost:2020/v1"
OPENAI_API_KEY="your_openai_key_here"
```

* If `MOONDREAM_API_KEY` is missing → falls back to local endpoint
* If `OPENAI_API_KEY` is missing → Vision Agent is disabled, Moondream playground still works

> **Never commit this file.**

---

## ▶️ Running ImageTagger Studio

Open Jupyter Notebook or VS Code and run all cells:

```python
demo.queue().launch(
    inline=True,
    show_error=True,
    server_name="127.0.0.1"
)
```

This starts a **fully interactive Gradio UI** inside your notebook.

---

## 📘 Example Workflows

### **🔧 Safety Review**

Upload a lab/workshop image → ask:

> “What safety hazards are present, and how confident are you?”

Agent returns:

* Structured reasoning
* Object detection
* Region zoom analysis
* Confidence score

---

### **🛰 Terrain Assessment**

Upload a planetary surface or rocky terrain → ask:

> “How accessible is this terrain for rover navigation?”

---

### **🍽 Everyday Scenes (for testing)**

Upload any random photo → try Query or Caption modes to validate behavior.

---

## ⚠️ Limitations

* Requires external APIs (Moondream + OpenAI)
* Confidence scores are heuristic — not a formal NASA risk analysis tool
* v1 supports single-image workflows only

---

## 🛠 Future Extensions (Optional Roadmap)

* Export tags (CSV / JSON)
* Multi-image batch processing
* Pipeline tuning for specific NASA missions
* Integration with internal datasets
* Notebook → standalone web app via Voila/Kubernetes

---

## 📄 License / Usage

Internal NASA AI Lab prototype.
If distributing publicly, ensure:

* Proper model licenses
* Removal of internal references
* External user documentation




