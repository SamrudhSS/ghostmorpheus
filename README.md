# Sentinel 4 - Industrial AI Monitoring System (Production v1.6.0)

Sentinel 4 is a high-fidelity industrial monitoring system designed for the **Hack Malnadu 2026** competition. It transforms raw sensor data into actionable intelligence through a combination of statistical anomaly detection, domain-expert failure modeling, and LLM-powered diagnostics.

## 🚀 Key Features

- **Tactical Mission Control**: A professional-grade "Industrial Neon Noir" dashboard providing sub-second telemetry visualization and incident streaming.
- **Domain-Expert AI Brain**:
    - **Specific Pattern Recognition**: Pre-calibrated to identify machine-specific failures (Bearing Wear, Thermal Runaway, Belt Slippage, and Cavitation).
    - **Multi-Sensor Correlation**: Only triggers high-priority alerts when multiple sensors confirm a consistent mechanical failure.
    - **Dynamic Baseline Calibration**: Fixed history-key ingestion to learn "normal" operating ranges from real 7-day simulation data.
- **Twilio Voice Broadcast System**:
    - **Emergency Calling**: Automatically places phone calls to supervisors when critical correlated faults are detected.
    - **Global & Local Cooldowns**: Intelligent suppression logic to prevent alert fatigue and redundant calling.
- **Dual-Layer Explanation**: Powered by Groq/Llama 3.1, providing concise 1-sentence alerts for the web and detailed 3-sentence expert briefings for voice calls.

## 🛠️ System Architecture

### Backend Agent (Python)
- `main.py`: The production entry point managing real-time loops, cooldowns, and alert synchronization.
- `app/anomaly.py`: The domain-expert engine containing hardcoded mechanical failure signatures.
- `app/voice_broadcaster.py`: Twilio Voice API wrapper for automated emergency briefings.
- `app/llm_explainer.py`: Multi-modal prompt engine for concise vs. detailed AI diagnostics.

### Tactical Dashboard (Frontend)
- `index.html`: Multi-view tactical shell (Overview, Diagnostics, Maintenance Matrix).
- `app.js`: Real-time data binding to SSE streams with integrated simulation server alert polling.

## 🚦 Setup & Launch

### 1. Prerequisites
- Python 3.11+
- [Node.js](https://nodejs.org/) (for the simulation server)
- [Groq API Key](https://console.groq.com/)
- [Twilio Account](https://www.twilio.com/console) (Optional, for voice alerts)

### 2. Configuration
Copy `.env.example` to `.env` and fill in your credentials:
```env
GROQ_API_KEY=your_key_here
TWILIO_ACCOUNT_SID=your_sid_here
TWILIO_AUTH_TOKEN=your_token_here
TWILIO_FROM_NUMBER=+1234567890
TWILIO_TO_NUMBER=+your_phone_number
```

### 3. Launch Sequence
1.  **Start Simulation Server**:
    ```bash
    cd malendau-hackathon
    npm install
    npm start
    ```
2.  **Start Sentinel 4 Agent**:
    ```bash
    cd ..
    pip install -r requirements.txt
    python main.py
    ```
3.  **Access Dashboard**:
    Open `frontend/index.html` in your browser.

## ⚙️ Intelligent Alerting Logic
The system differentiates between **Warnings** and **Emergency Faults**:
- **Warning (Dashboard Only)**: Occurs when a single sensor shows variance (Z-score > 3.0).
- **Emergency (Voice Call + Dashboard)**: Occurs when the expert engine detects **correlated anomalies** matching a known mechanical failure pattern.

---
**Mission Status**: Production Ready. Optimized for accuracy, performance, and minimal alert fatigue.
