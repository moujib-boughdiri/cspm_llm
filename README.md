# CSPM LLM Assistant

A prototype **Cloud Security Posture Management (CSPM) assistant** that presents detected cloud-security findings, uses a Groq-hosted LLM to explain risks and suggest remediation, supports a security-focused chatbot, and generates downloadable security reports.

> **Project status:** The uploaded source code is an early prototype. The current threat receiver returns sample findings, not results from a live cloud scanner. The uploaded code does not currently show an Azure integration or a Retrieval-Augmented Generation (RAG) pipeline, despite those technologies appearing in the repository description. Update this README as those components are implemented.

## Features

- **Threat analysis:** Select a detected finding and request an LLM-generated explanation.
- **Remediation guidance:** Generate three actionable remediation steps for a selected finding.
- **Security chatbot:** Ask questions about cloud-security risks and the findings in the current session.
- **Session memory:** Summarize a conversation and save up to the five most recent session summaries locally.
- **Report generation:** Generate a structured security report and download it as a text file.
- **Streamlit interface:** Access the prototype through a browser-based dashboard.

## Current demonstration data

The function `get_threats_from_model()` in `api/receiver.py` currently returns three hard-coded sample findings:

- `UnauthorizedRootAccess`
- `OpenS3BucketExposed`
- `WeakIAMPolicy`

Each finding includes a confidence score. Replace this mock receiver with a connection to your actual detection model or API before using the application for real assessments.

## Architecture

```text
                   Streamlit dashboard
                       (ui/app.py)
                            |
             +--------------+--------------+
             |              |              |
        Threat analysis  Security chat  Report generation
             |              |              |
       explainer.py     chatbot.py     report_gen.py
       remediator.py        |
                        memory.py
             |
      api/receiver.py
       (sample findings)
             |
       Groq API
   (Llama 3.3 70B Versatile)
```

### Main components

| Path | Responsibility |
|---|---|
| `main.py` | Starts the Streamlit application. |
| `ui/app.py` | Defines the dashboard and its three tabs. |
| `api/receiver.py` | Supplies sample threat findings; intended to be replaced by a real model/API connection. |
| `llm/explainer.py` | Requests plain-language explanations of findings from the LLM. |
| `llm/remediator.py` | Requests remediation steps for a finding. |
| `llm/chatbot.py` | Implements the cloud-security chatbot and adds current findings to its system context. |
| `llm/memory.py` | Summarizes and stores recent chatbot sessions locally. |
| `llm/report_gen.py` | Generates and saves security reports. |
| `requirements.txt` | Lists the project's declared Python dependencies. |

## Technology stack

- Python
- Streamlit
- Groq Python SDK
- Llama 3.3 70B Versatile (through the Groq API)
- python-dotenv for loading environment variables

## Prerequisites

- Python installed on your machine.
- A Groq API key.
- An internet connection for requests to the Groq API.

## Installation and setup

### 1. Clone the repository

```bash
git clone https://github.com/moujib-boughdiri/cspm_llm.git
cd cspm_llm
```

Alternatively, download the repository as a ZIP file and extract it.

### 2. Create and activate a virtual environment (recommended)

**Windows PowerShell:**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

If PowerShell blocks activation, you can use Command Prompt instead:

```bat
.venv\Scripts\activate.bat
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
pip install python-dotenv
```

`python-dotenv` is imported by the LLM modules but is not currently listed in the uploaded `requirements.txt`.

### 4. Configure the Groq API key

Create a file named `.env` in the project root (the same folder as `main.py`) and add:

```env
GROQ_API_KEY=your_groq_api_key_here
```

Get an API key from the [Groq Console](https://console.groq.com/).

**Security:** Never commit `.env`, API keys, private keys, certificates, or personal session data to GitHub. Keep `.env` local; the repository's `.gitignore` is intended to exclude it.

### 5. Run the application

From the project root:

```bash
python main.py
```

Or launch Streamlit directly:

```bash
streamlit run ui/app.py
```

Streamlit will display a local URL in the terminal (usually `http://localhost:8501`). Open that address in your browser.

## Using the dashboard

1. **Threat Analysis:** Choose a sample finding and click **Analyze** to request an explanation and remediation guidance.
2. **Security Chatbot:** Ask a question about cloud security or the current sample findings.
3. **End Session & Save Memory:** Save an LLM-generated summary of the current chat locally.
4. **Generate Report:** Create a security report and download it from the dashboard. Generated report files are stored in the `reports/` directory.

## Configuration and data notes

- The application expects `GROQ_API_KEY` to be available as an environment variable or in the root `.env` file.
- LLM requests use the `llama-3.3-70b-versatile` model through Groq.
- Session summaries are stored in `memory/session_memory.json`.
- Generated reports are written to `reports/`.
- The current threat data is simulated. It must not be interpreted as a live cloud security assessment.
- The current source should be reviewed and tested before production use. In particular, connect a real detection source, validate findings, and add appropriate authentication, authorization, logging, and error handling before deployment.

## Security notice

This repository is a development prototype, not a production-ready security scanner. Do not put real credentials, private certificates, production logs, or sensitive session data in the repository. Rotate any credential that may have been committed or shared accidentally.

## Contributors

- **Moujib Boughdiri** — [GitHub](https://github.com/moujib-boughdiri)

## License

No license is currently specified. Unless a license is added, normal copyright restrictions apply; reuse and redistribution are not automatically granted.
