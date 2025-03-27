# SDLC_Workflow_Langgraph
LLMs via LangChain SDLC workflow

---
title: SDLC_Workflow_Langgraph
emoji: 💻
colorFrom: pink
colorTo: red
sdk: streamlit
sdk_version: 1.44.0
app_file: app.py
pinned: false
license: apache-2.0
short_description: LLMs via LangChain SDLC workflow
---

Check out the configuration reference at https://huggingface.co/docs/hub/spaces-config-reference


# AI-Powered SDLC Orchestrator (Streamlit App)

## Description

This application leverages Large Language Models (LLMs) via LangChain to orchestrate a simplified Software Development Lifecycle (SDLC) workflow within a Streamlit user interface. It guides the user through various stages, from requirements gathering to deployment planning, using AI to generate artifacts and providing feedback loops involving both AI and human input.

The application is designed to be modular, with the Streamlit UI (`app.py`) handling user interaction and state management, while the core AI logic, state definitions, and artifact saving are encapsulated in the backend module (`newSDLC.py`).

## Features

*   **Multi-Stage SDLC Simulation:** Covers key stages:
    *   Requirements Gathering (Iterative Q&A)
    *   User Story Generation & Refinement
    *   Product Owner Review & Refinement
    *   Design Document Generation & Refinement
    *   UML Diagram Selection, Generation (PlantUML), & Refinement
    *   Code Generation (based on design/UML) & Refinement (with Web Search via Tavily)
    *   Code Review & Security Analysis
    *   Test Case Generation & Code Refinement (based on test pass/fail)
    *   Quality Analysis Report Generation & Refinement
    *   Deployment Plan Generation & Refinement
*   **Interactive Feedback Loops:** Incorporates both AI-generated feedback and user input at various stages for iterative refinement.
*   **Dynamic LLM Configuration:** Allows users to select LLM providers (OpenAI, Groq, extensible) and specific models via the UI. API keys are entered securely.
*   **Tavily Web Search Integration:** Uses Tavily API for web searches during the code generation cycle to find relevant solutions or fix errors based on user feedback.
*   **Artifact Generation & Saving:** Creates text files (.md, .txt), PlantUML files (.puml), and attempts PNG generation for diagrams. Saves code files organized into stage-specific folders.
*   **Downloadable Artifacts:** Provides download links in the sidebar for key documents, UML diagrams (PNGs if generated), and code snapshots (ZIP archives) from critical stages (Review, Testing, Final).
*   **Workflow Visualization:** Includes a graph diagram (`SDLC_Workflow_Graph_Diagram.png`) visualizing the application's state transitions (Note: This static image doesn't update dynamically).
*   **State Management:** Uses Streamlit's session state to maintain progress throughout the workflow.
*   **Error Handling & Retry:** Incorporates basic retry logic (using Tenacity) for potentially flaky LLM calls and provides UI feedback on errors.

## Workflow Diagram

![SDLC Workflow Diagram](SDLC_Workflow_Graph_Diagram.png)
*(This diagram illustrates the general flow of stages and decision points within the application)*

## Technology Stack

*   **Frontend:** Python, Streamlit
*   **Backend Logic:** Python
*   **AI Orchestration:** LangChain (`langchain`, `langchain-core`, `langchain-openai`, `langchain-groq`)
*   **LLM APIs:** OpenAI API, Groq API (extensible)
*   **Web Search:** Tavily API
*   **UML Generation:** `plantuml` library (requires Java runtime)
*   **Data Validation:** Pydantic
*   **Retry Logic:** Tenacity
*   **Environment Mgmt:** `python-dotenv`

## Setup & Local Run

### Prerequisites

*   Python 3.9+
*   Git
*   Java Runtime Environment (JRE/JDK) installed and added to your system's PATH (required for PlantUML PNG generation). You can often test this by running `java -version` in your terminal.

### Steps

1.  **Clone the Repository:**
    ```bash
    git clone <your-repo-url>
    cd <your-repo-name>
    ```

2.  **Create Virtual Environment (Recommended):**
    ```bash
    python -m venv env
    # Activate it:
    # Windows:
    .\env\Scripts\activate
    # macOS/Linux:
    source env/bin/activate
    ```

3.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Configure API Keys (.env file):**
    *   Create a file named `.env` in the project's root directory.
    *   Add your API keys to this file. **Important:** The Streamlit app now takes keys via the UI, but `newSDLC.py` *might* still load other variables from `.env` if you added any. For local testing *without* the UI configuration, you might populate these (but the UI method is preferred for deployment):
        ```dotenv
        # --- LLM Configuration (NOW HANDLED BY UI, but keep for reference) ---
        # MODEL_PROVIDER="OpenAI" # or "Groq"
        # OPENAI_MODEL_NAME="gpt-4o"
        # OPENAI_API_KEY="sk-..."
        # GROQ_MODEL_NAME="llama3-70b-8192"
        # GROQ_API_KEY="gsk_..."

        # --- Tavily Configuration (NOW HANDLED BY UI, but keep for reference) ---
        # TAVILY_API_KEY="tvly-..."

        # --- Optional: LangSmith Tracing ---
        # LANGCHAIN_TRACING_V2="true"
        # LANGSMITH_API_KEY="ls__..."
        # LANGCHAIN_ENDPOINT="https://api.smith.langchain.com"
        # LANGCHAIN_PROJECT="Your-Project-Name" # Replace with your LangSmith project
        ```
    *   **Never commit your `.env` file to Git!** Add `.env` to your `.gitignore` file.

5.  **Run the Streamlit App:**
    ```bash
    streamlit run app.py
    ```

6.  **Configure in UI:** Open the app in your browser. Use the "Configuration" section in the sidebar to select your LLM provider, model, and enter the corresponding LLM API key and your Tavily API key. Click "Apply Configuration".

7.  **Start Workflow:** Once configuration is applied successfully, the initial project setup form will appear. Fill it in and click "Start Workflow".

## Configuration (API Keys & Models)

*   **LLM Provider & Model:** Select your desired LLM provider (e.g., OpenAI, Groq) and the specific model from the dropdowns in the Streamlit sidebar's "Configuration" section.
*   **LLM API Key:** Enter the API key corresponding to the selected provider in the password input field within the sidebar configuration.
*   **Tavily API Key:** Enter your Tavily API key in the password input field for web search functionality. This is optional; if left blank, web search steps will be skipped.
*   **Apply:** Click "Apply Configuration" to initialize the LLM and Tavily clients. The main workflow UI will only become active after successful configuration.

**Important API Key Notes:**

*   **Security:** API keys entered via the UI are stored in Streamlit's session state *for the duration of the session*. They are **not** saved permanently in any file by the application itself. However, be mindful when deploying, especially to public platforms. Use Hugging Face Secrets (see Deployment section).
*   **Provider Requirements:**
    *   **Groq:** Requires a GroqCloud API key. Free tiers might have rate limits or context window limitations that could affect complex generations.
    *   **OpenAI:** Requires an OpenAI API key with billing set up. Costs will be incurred based on usage.
    *   **Tavily:** Requires a Tavily API key. Check their pricing for usage limits.
    *   Other providers (if added) will have their own key requirements and potential costs/limitations.

## Hugging Face Spaces Deployment Guide

You can deploy this application as a Streamlit app on Hugging Face Spaces.

### Steps

1.  **Hugging Face Account:** Ensure you have a Hugging Face account.
2.  **Create New Space:**
    *   Go to [huggingface.co/new-space](https://huggingface.co/new-space).
    *   Choose an **Owner** (your user or an org).
    *   Enter a **Space name** (e.g., `ai-sdlc-orchestrator`).
    *   Select **Streamlit** as the Space SDK.
    *   Choose hardware (CPU basic is likely sufficient to start).
    *   Choose **Public** or **Private** visibility. (Use Private if testing with sensitive keys initially).
    *   Click **Create Space**.
3.  **Upload Files:** You have two main options:
    *   **(Recommended) Use Git:** Clone the Space repository locally, add your files, commit, and push.
        ```bash
        # Clone your newly created space repo
        git clone https://huggingface.co/spaces/<your-username>/<your-space-name>
        cd <your-space-name>

        # Copy your project files here:
        # app.py, newSDLC.py, requirements.txt, SDLC_Workflow_Graph_Diagram.png

        # Add, commit, and push
        git add .
        git commit -m "Add initial application files"
        git push
        ```
    *   **Upload via UI:** Go to the "Files" tab in your Space settings and upload `app.py`, `newSDLC.py`, `requirements.txt`, and optionally `SDLC_Workflow_Graph_Diagram.png`.

4.  **Configure Secrets (CRITICAL):**
    *   **DO NOT put API keys directly in your code or `requirements.txt` or `.env` when deploying!**
    *   Go to your Space settings (click the gear icon or navigate to `https://huggingface.co/spaces/<your-username>/<your-space-name>/settings`).
    *   Scroll down to the **"Secrets"** section.
    *   Click **"New secret"**.
    *   Add secrets for your API keys. **Crucially, the "Secret name" must exactly match the variable names your UI configuration expects to retrieve or the names used if `newSDLC.py` were loading them directly (though it shouldn't be for deployment).** Since the UI handles input, you might not *need* secrets if users always enter them, *but it's much safer and more convenient to use secrets*. Let's assume you *want* to pre-fill the keys from secrets:
        *   Name: `LLM_API_KEY` -> Value: `<Your-OpenAI-or-Groq-Key>` (Enter the key you'll use most often, or leave blank if users *must* enter it).
        *   Name: `TAVILY_API_KEY` -> Value: `<Your-Tavily-Key>` (Or leave blank).
        *   *Modify `app.py` (Optional):* If you use secrets, you could pre-fill the `st.text_input` `value` fields by reading the secrets:
          ```python
          # Inside sidebar configuration expander in app.py
          llm_key_secret = os.environ.get("LLM_API_KEY", "") # Read from env (set by HF secrets)
          tavily_key_secret = os.environ.get("TAVILY_API_KEY", "")
          llm_api_key_input = st.text_input(..., value=st.session_state.get("llm_api_key", llm_key_secret))
          tavily_api_key_input = st.text_input(..., value=st.session_state.get("tavily_api_key", tavily_key_secret))
          ```
    *   Secrets are injected as environment variables into your Space container.

5.  **PlantUML / Java Dependency (Limitation):**
    *   The default Streamlit SDK environment on Hugging Face Spaces **does not include Java**.
    *   Therefore, the `plantuml` library **will likely fail** when trying to generate PNG images (`save_final_uml_diagrams` will log errors for PNG generation).
    *   **Workarounds:**
        *   **Accept Limitation:** The app will still function, and `.puml` files will be saved. Users just won't get PNG download buttons in the sidebar. This is the simplest approach. You might want to update the sidebar caption in `app.py` for UML downloads to explicitly state PNGs are unavailable on this deployment.
        *   **Modify `newSDLC.py`:** Comment out or remove the PNG generation block within `save_final_uml_diagrams` to prevent errors in the logs.
        *   **(Advanced) Use Docker:** Create a custom Dockerfile for your Space that starts from a base image including Python and Java, installs PlantUML, copies your app files, and runs Streamlit. This requires Docker knowledge.

6.  **Check Build & Runtime Logs:**
    *   Go to your Space page. Hugging Face will automatically try to build the environment based on `requirements.txt`.
    *   Monitor the "Build logs" and "Runtime logs" for any errors during installation or application startup. Fix any dependency issues in `requirements.txt` if needed.

7.  **Access Your App:** Once the build is complete, your Streamlit app should be running at `https://huggingface.co/spaces/<your-username>/<your-space-name>`.

## How it Works

1.  **Configuration (`app.py` Sidebar):** The user selects the LLM provider/model and enters API keys. Clicking "Apply" calls `initialize_llm_clients` in `newSDLC.py`.
2.  **Initialization (`newSDLC.py`):** `initialize_llm_clients` attempts to create LangChain LLM and Tavily client instances using the provided keys and settings. It returns the instances (or None) and any error messages.
3.  **State Setup (`app.py`):** If initialization succeeds, the instances are stored in `st.session_state`. When the user starts the workflow via the "Project Configuration" form, these instances are copied into the main `st.session_state.workflow_state` dictionary under the keys `llm_instance` and `tavily_instance`.
4.  **Workflow Execution (`app.py` + `newSDLC.py`):**
    *   `app.py` uses the `current_stage` in session state to determine what UI elements to show (input boxes, buttons).
    *   User actions trigger transitions to "run\_..." stages.
    *   `app.py`'s `run_workflow_step` function looks up the appropriate backend function from `newSDLC.py` in the `workflow_map`.
    *   It calls the backend function, passing the entire `workflow_state` dictionary (which includes the LLM/Tavily instances).
    *   Backend functions in `newSDLC.py` access `state['llm_instance']` or `state['tavily_instance']` to perform their tasks (LLM calls, searches). They perform checks to ensure the instance exists before using it.
    *   Backend functions update the `state` dictionary and return it.
    *   `run_workflow_step` updates the session state, determines the next display content, sets the next UI stage, and triggers a Streamlit rerun.
5.  **Artifact Saving (`newSDLC.py`):** `save_...` functions write artifacts to subdirectories within the `project_folder` defined during setup. Path keys are added to the state for `app.py` to create download links/ZIPs.

## Limitations & Future Work

*   **PlantUML on Spaces:** PNG generation for UML diagrams is unlikely to work on default HF Spaces due to the missing Java dependency. Only `.puml` files are reliably saved.
*   **Error Handling:** Basic error handling exists, but complex LLM errors or unexpected workflow states might require a manual restart. UI feedback on specific errors could be improved.
*   **No Persistence:** Project state is lost when the Streamlit session ends or the app restarts. Implementing database storage or saving/loading state would be needed for persistence.
*   **Context Window Limits:** Code context sent to the LLM is truncated to avoid errors, which might limit the AI's understanding in complex refinement steps.
*   **Model Support:** Only OpenAI and Groq are currently implemented in `initialize_llm_clients`. Adding more providers requires updating `newSDLC.py` and `app.py`.
*   **UI for UML Feedback:** Currently, UML feedback is collected via a single text box (`collect_uml_human_feedback`), not per-diagram.

## Contributing

(Optional: Add guidelines if you expect contributions)
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

(Optional: Specify a license, e.g., MIT, Apache 2.0)
[MIT](https://choosealicense.com/licenses/mit/)
