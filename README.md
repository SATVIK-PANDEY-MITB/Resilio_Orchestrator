# Resilio Orchestrator

A React and FastAPI-based operations intelligence platform for coordinating terminal, logistics, workforce, and energy workflows. The application provides a responsive dashboard, dataset management, interactive charts, scenario planning, report generation, and an AI-assisted operations chatbot.

> **Project status:** Demonstration-oriented application with sample operational KPIs and rule-based AI responses. It is not a production-ready control system or model inference service.

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Technology Stack](#technology-stack)
- [Project Architecture](#project-architecture)
- [Requirements](#requirements)
- [Quick Start](#quick-start)
- [Manual Setup](#manual-setup)
- [Usage](#usage)
- [API Reference](#api-reference)
- [Project Structure](#project-structure)
- [Validation](#validation)
- [Troubleshooting](#troubleshooting)
- [Known Limitations](#known-limitations)
- [Contributing](#contributing)
- [License](#license)

## Overview

Resilio Orchestrator supports four operational domains:

| Domain | Primary purpose | Interface |
| --- | --- | --- |
| Terminal operations | Container, cargo, and facility coordination | Control Center and simulator |
| Courier operations | Delivery movement and route management | Courier Hub |
| Workforce operations | Staffing, capacity, and human-resource analytics | Workforce View |
| Energy operations | Power-system monitoring and sustainability metrics | Energy View |

The application supports **4 operation types**, **10 uploaded files per submission**, **4 primary dashboard modes**, and responsive layouts for desktop, tablet, and mobile devices.

## Features

### Operations and analytics

- 4 operational workflows: terminal, courier, workforce, and energy
- Dashboard, simulator, what-if analysis, and reporting tools
- Real-time KPI cards and chart rendering
- Dataset upload, listing, deletion, and metadata inspection
- Scenario planning with adjustable operational parameters
- PDF report generation
- Activity monitoring and navigation logs

### AI and data processing

- Operation-aware rule-based chatbot responses
- Optional GGUF-model integration through the configured model path
- CSV, JSON, and XLSX file support
- Automatic file metadata extraction
- Dataset filtering by operation type
- Graceful fallback when the AI model or pandas dependency is unavailable

### User experience

- Secure demo login flow and persistent session handling
- Light and dark theme support
- Responsive interface components
- Bottom navigation and workflow switching

## Technology Stack

| Layer | Technology | Purpose |
| --- | --- | --- |
| Frontend | React 18.3.1 | Component-based interface |
| Frontend tooling | TypeScript 5.9.2, Vite 6.3.5 | Development, type checking, and production build |
| Interface components | Radix UI, Lucide React | Accessible UI primitives and icons |
| Visualization | Recharts | Charts and analytics views |
| Backend | FastAPI, Uvicorn | REST API and asynchronous server |
| Data processing | Python standard library, pandas, openpyxl | File inspection and structured data processing |
| AI integration | GGUF-compatible interface | Optional local model integration |

## Project Architecture

```text
Browser
  |
  v
React + TypeScript frontend
  |
  +-- REST API: http://localhost:8002
        |
        +-- FastAPI application
        +-- Optional local GGUF model
        +-- data/ uploaded datasets
```

The frontend uses Vite on port **3000**. The backend uses port **8002**. The production frontend build is generated in the `build/` directory.

## Requirements

- Python **3.8 or newer**
- Node.js **18 or newer** recommended
- npm
- 2 GB or more of available RAM for frontend dependency installation and local development
- A local GGUF model is optional and not required for demo mode

Check the installed versions:

```bash
python --version
node --version
npm --version
```

## Quick Start

### Windows

Run the project from the repository root:

```bat
startup.bat
```

The startup script creates the `data/` directory when needed, installs missing dependencies, starts the backend, launches Vite, and opens the frontend.

### macOS and Linux

```bash
chmod +x start.sh
./start.sh
```

### Default application URLs

- Frontend: http://localhost:3000
- Backend: http://localhost:8002
- API documentation: http://localhost:8002/docs
- Root health check: http://localhost:8002/

### Stop the application

- Stop the Windows startup command windows, or
- Press `Ctrl+C` in the terminal running the frontend and backend.

## Manual Setup

### 1. Create a Python environment

```bash
python -m venv .venv

# Windows PowerShell
.venv\Scripts\Activate.ps1

# Windows Command Prompt
.venv\Scripts\activate.bat

# macOS/Linux
source .venv/bin/activate
```

### 2. Install Python dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 3. Install frontend dependencies

```bash
npm install
```

### 4. Start the backend

The active backend entry point is `simple_server.py`, which runs on port **8002**.

```bash
python simple_server.py
```

### 5. Start the frontend

Open a second terminal and run:

```bash
npm run dev
```

The Vite development server uses port **3000** and opens the browser when available.

## Usage

### Select an operation

1. Open the application at http://localhost:3000.
2. Complete the demo authentication flow.
3. Select one of the supported operation types:
   - Terminal
   - Courier
   - Workforce
   - Energy

### Upload data

1. Open **Settings**.
2. Select one or more files using the upload manager.
3. Use CSV, JSON, or XLSX files.
4. Upload a maximum of **10 files** per submission.
5. The dataset metadata is stored in the `data/` directory.

### Explore dashboards

- View KPI cards and charts.
- Modify dashboard components.
- Run the simulator.
- Create scenario forecasts using the What-If Analysis screen.
- Generate a PDF report.

### Use the chatbot

Send operation-specific questions such as:

```text
Analyze my terminal performance.
Show optimization opportunities for workforce operations.
Switch to the energy dashboard.
```

If no GGUF model is available, the chatbot returns rule-based responses and suggestions.

## API Reference

All API endpoints are available through the backend at http://localhost:8002.

### Health check

```http
GET /
```

Example response:

```json
{
  "status": "running",
  "message": "Honeywell Terminal Manager API",
  "model_loaded": false,
  "data_folder": "./data",
  "version": "1.0.0"
}
```

### Chat

```http
POST /api/chat
Content-Type: application/json
```

Request body:

```json
{
  "message": "Analyze my terminal performance",
  "operation_type": "terminal",
  "context": {}
}
```

Response:

```json
{
  "response": "string",
  "insights": ["string"],
  "suggestions": ["string"],
  "data_analysis": {}
}
```

### Upload datasets

```http
POST /api/upload-data
Content-Type: multipart/form-data
```

Form fields:

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `files` | file[] | Yes | Up to 10 CSV, JSON, or XLSX files |
| `operation_type` | string | No | Default: `terminal` |

### List datasets

```http
GET /api/datasets?operation_type=terminal
```

### Delete a dataset

```http
DELETE /api/datasets/{dataset_id}
```

### Analyze a dataset

```http
GET /api/analyze-dataset/{dataset_id}
```

### Operation KPIs

```http
GET /api/operation-data/{operation_type}
```

Supported operation types:

- `terminal`
- `courier`
- `workforce`
- `energy`

## Project Structure

```text
.
├── main.py                     # Legacy FastAPI backend entry point
├── simple_server.py            # Active backend entry point on port 8002
├── requirements.txt            # Python dependencies
├── package.json                # Frontend dependencies and scripts
├── vite.config.ts              # Vite development and build configuration
├── startup.bat                  # Windows bootstrap script
├── start.bat                    # Manual Windows startup script
├── start.sh                    # macOS/Linux startup script
├── test_server.py               # Backend smoke test
├── validate_system.py           # Environment and API validation script
├── src/                        # React application source
│   ├── components/             # UI, dashboard, workflow, and report components
│   ├── services/               # API client
│   ├── hooks/                  # React hooks
│   └── styles/                 # Global styles
├── data/                       # Uploaded files and dataset storage
├── gemma-3-4b-it-Q8_0.gguf     # Optional local GGUF model
└── build/                      # Generated production frontend output
```

The `gemma-3-4b-it-Q8_0.gguf` model is optional. If it is not present, the application uses built-in rule-based responses.

## Validation

Run the frontend production build:

```bash
npm run build
```

Run the backend smoke test:

```bash
python test_server.py
```

Run the full system validation script:

```bash
python validate_system.py
```

The validation script checks the backend health endpoint and reports whether the frontend and backend can be reached on their configured ports.

## Troubleshooting

### Port already in use

Check which process owns the required port:

```powershell
netstat -ano | findstr :3000
netstat -ano | findstr :8002
```

Stop the old process, or change the port in the affected configuration.

### Python dependencies fail

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

If the backend reports that pandas is unavailable, install the full requirements file again and restart the server.

### Frontend dependency errors

```bash
rm -rf node_modules package-lock.json
npm install
npm run build
```

On Windows, use `Remove-Item -Recurse -Force node_modules, package-lock.json` before reinstalling.

### API requests fail

1. Confirm that the backend is running.
2. Check the browser console and backend terminal output.
3. Verify that the frontend API base URL is `http://localhost:8002`.
4. Confirm that the backend and frontend are running on different ports.

### GGUF model not loaded

The backend prints whether the model file was found. The application continues to operate with rule-based responses if the model is absent. No additional configuration is required for the demo workflow.

## Known Limitations

- AI responses are currently rule-based; no real GGUF inference is implemented.
- KPI values and charts are sample data when no dataset-specific calculation is implemented.
- Excel processing is partially simulated for certain file types.
- The API client is hard-coded to `http://localhost:8002`.
- `main.py` is a legacy backend entry point and is not the active default server path.
- Multipart uploads are not validated for large-file size limits.
- Authentication is demo-oriented and is not a secure production identity system.
- Uploaded datasets are stored locally in the `data/` directory.

## Contributing

1. Fork the repository.
2. Create a feature branch.
3. Implement a focused change.
4. Run the frontend build and backend validation.
5. Submit a pull request with a clear summary and testing evidence.

## License

This project is licensed under the terms of the [LICENSE](LICENSE).

## Support

For technical questions or application issues, review the logs from both terminals and run the validation command before reporting a bug.
