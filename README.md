# 🎨 Blender MCP Agent — Autonomous 3D Scene Construction Graph

**Blender MCP Agent** is an autonomous multi-agent pipeline built with **LangGraph**, **LangChain**, **Groq**, **OpenAI**, and **Tavily**. It converts high-level natural language prompts into precise 3D Blender assets and full environments by generating technical specifications, gathering real-world reference dimensions, planning construction steps, and executing Python (`bpy`) scripts directly inside Blender via the **Model Context Protocol (MCP)**.

## ✨ Key Features

* 📐 **Technical Specification Generator**: Expands basic user prompts into 500-word 3D technical blueprints covering geometry, scale (meters), materials, lighting, collection hierarchy, and camera composition.

* 🧠 **Dynamic Research Router**: Evaluates whether an asset requires real-world specifications or brand dimensions (`closed_book`, `hybrid`, `open_book`) and triggers real-time web search via Tavily when necessary.

* 📋 **Structured Build Planner**: Produces an 8–14 step sequential Blender `bpy` construction plan with exact metric coordinates and material assignments.

* 🔌 **MCP Tool Integration**: Connects directly to Blender 4.x using `mcp-for-blender` over stdio transport to run Python execution stages in real-time.

* 🛠️ **Built-in `bpy` Helper Suite**: Includes procedural helper routines for scene resetting, bounding-box placement, PBR materials, procedural wood textures, lighting rigs (studio, daylight, warm interior, dramatic), and automated camera framing.

* 🔍 **Automated Verification Loop**: Verifies object alignment, camera bounding, light presence, and zero-gap mesh constraints before concluding execution.

## 🏗️ Graph Workflow Architecture

```
                       ┌─────────────────────────┐
                       │       START State       │
                       └────────────┬────────────┘
                                    │
                                    ▼
                       ┌─────────────────────────┐
                       │   detail_prompt_temp    │ (Generates 3D Tech Specs)
                       └────────────┬────────────┘
                                    │
                                    ▼
                       ┌─────────────────────────┐
                       │       router_node       │ (Determines Research Needs)
                       └────────────┬────────────┘
                                    │
                       ┌────────────┴────────────┐
                       │                         │
            (Needs Internet Search)       (No Search Required)
                       │                         │
                       ▼                         │
            ┌─────────────────────┐              │
            │     web_search      │              │ (Tavily Search API)
            └──────────┬──────────┘              │
                       │                         │
                       └────────────┬────────────┘
                                    │
                                    ▼
                       ┌─────────────────────────┐
                       │      planning_node      │ (Generates 8-14 step build plan)
                       └────────────┬────────────┘
                                    │
                                    ▼
                       ┌─────────────────────────┐
                       │    blender_mcp_node     │ (Executes bpy scripts via MCP)
                       └────────────┬────────────┘
                                    │
                                    ▼
                       ┌─────────────────────────┐
                       │        END State        │
                       └─────────────────────────┘

```

## ⚙️ Prerequisites & Setup

### 1. External Dependencies

* **Blender 4.x** installed and running on your system.

* Node.js & `uvx` available for running the Blender MCP adapter (`mcp-for-blender`).

### 2. Environment Variables

Create a `.env` file in your working directory with the required API keys:

```
OPENAI_API_KEY=your_openai_api_key
GROQ_API_KEY=your_groq_api_key
groq2=your_secondary_groq_api_key
TAVILY_API_KEY=your_tavily_api_key

```

### 3. Python Package Installation

```
pip install python-dotenv langchain-groq langchain-openai langgraph tavily-python langchain-mcp-adapters pydantic

```

## 🚀 Running the Agent

Start the MCP server connection and execute the pipeline using Python:

```
python main.py

```

### Example Input Prompt

```
initial_state = {
    "user_prompt": (
        "Create a stylized low-poly 1994 Toyota Supra MK4 (JZA80) coupe assembly "
        "in Blender standing at z=0 with real-world metric proportions. "
        "Separate into collections: Body, Wheels, Glass, Lights, and Spoiler..."
    )
}

```

## 🛠️ MCP Blender Helpers Included

The execution prompt provides standard helper function signatures to ensure clean `bpy` script creation:

* `reset_scene()`: Purges default objects, initializes `Scene_Asset` collection, sets engine to Cycles with denoising.

* `box()`, `cylinder_between()`, `sphere_at()`, `open_cone()`: Primitive creation tools with scale application and auto-beveling.

* `pbr()` & `wood_material()`: Material generators for metallic, dielectric, emissive, and procedural node networks.

* `lighting_rig()`: Automated studio, daylight, warm interior, or dramatic 3-point light configurations.

* `frame_camera()`: Dynamically frames camera lens, elevation, and azimuth based on total subject bounding boxes.

* `gap()` & `world_bbox()`: Verification functions ensuring structural connectivity and zero overlap errors.

## 📜 License

This project is open-source under the [MIT License](LICENSE).
