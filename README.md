# 🏭 Predictive Maintenance System for Machine Health Monitoring

A complete final-year engineering project that uses machine learning to predict machine failures
from sensor data (temperature, vibration, pressure, RPM).

---

## 📁 Project Structure

```
predictive_maintenance/
├── app.py                  ← Flask backend (all API endpoints)
├── model.py                ← ML training script (XGBoost + Logistic Regression)
├── requirements.txt        ← Python dependencies
│
├── dataset/
│   └── sensor_data.csv     ← Auto-generated synthetic sensor data (3000 rows)
│
├── saved_model/
│   ├── xgb_model.pkl       ← Trained XGBoost model
│   ├── scaler.pkl          ← Fitted StandardScaler
│   └── metrics.json        ← Accuracy & training metrics
│
├── templates/
│   └── index.html          ← Main HTML dashboard
│
├── static/
│   ├── css/style.css       ← Stylesheet (industrial dark theme)
│   ├── js/app.js           ← Frontend JavaScript
│   └── img/                ← Auto-generated visualisation plots
│       ├── temperature_dist.png
│       ├── vibration_trends.png
│       ├── confusion_matrix.png
│       ├── feature_importance.png
│       └── model_comparison.png
│
└── logs/
    └── maintenance.db      ← SQLite database (auto-created)
```

---

## ⚙️ Setup & Installation

### 1. Clone / Download the project
```bash
cd predictive_maintenance
```

### 2. Create a virtual environment (recommended)
```bash
python -m venv venv
source venv/bin/activate        # Linux / macOS
venv\Scripts\activate           # Windows
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Train the ML models
```bash
python model.py
```
This will:
- Generate `dataset/sensor_data.csv` (3000 labelled sensor records)
- Train XGBoost and Logistic Regression classifiers
- Print accuracy, classification report
- Save plots to `static/img/`
- Save model artifacts to `saved_model/`

Expected output:
```
✓ XGBoost Accuracy        : 99.83%
✓ Logistic Reg. Accuracy  : 99.17%
```

### 5. Start the Flask server
```bash
python app.py
```

### 6. Open in browser
```
http://localhost:5000
```

---

## 🖥️ Dashboard Features

| Tab | Description |
|-----|-------------|
| **Dashboard** | KPI overview, model performance, sensor thresholds |
| **Analyse** | Enter sensor values → get ML prediction + RCA |
| **Digital Twin** | 24-hour degradation simulation with charts |
| **AI Assistant** | Rule-based chatbot for maintenance Q&A |
| **History** | All predictions stored in SQLite |
| **Charts** | Visualisation plots from training |

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Main dashboard UI |
| POST | `/api/predict` | Predict machine condition |
| POST | `/api/chat` | AI chatbot |
| POST | `/api/digital-twin` | Run 24h simulation |
| GET | `/api/history` | Recent predictions |
| GET | `/api/stats` | System statistics |
| GET | `/api/metrics` | Model metrics |

### POST /api/predict — Example
```bash
curl -X POST http://localhost:5000/api/predict \
  -H "Content-Type: application/json" \
  -d '{"temperature": 95, "vibration": 1.9, "pressure": 58, "rpm": 870}'
```

**Response:**
```json
{
  "success": true,
  "result": {
    "condition": "Failure",
    "confidence": 99.7,
    "probabilities": { "Healthy": 0.1, "Warning": 0.2, "Failure": 99.7 },
    "root_causes": [
      {
        "cause": "Overheating",
        "sensor": "Temperature",
        "value": "95.0°C",
        "explanation": "Motor/bearing temperature exceeds safe limits...",
        "action": "Shut down immediately. Inspect cooling system..."
      }
    ],
    "threshold_alerts": [
      { "sensor": "temperature", "level": "FAILURE", "msg": "Critical overheating: 95.0°C" }
    ]
  }
}
```

### POST /api/chat — Example
```bash
curl -X POST http://localhost:5000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What is the machine status?"}'
```

---

## 📊 Sample Sensor Values

| Scenario | Temperature | Vibration | Pressure | RPM |
|----------|------------|-----------|----------|-----|
| ✅ Healthy | 52°C | 0.28g | 102 PSI | 1510 |
| ⚠️ Warning | 74°C | 0.72g | 84 PSI | 1260 |
| 🔴 Failure | 102°C | 1.85g | 55 PSI | 880 |

---

## 🧠 How the Model Works

1. **Data Generation**: 3,000 synthetic sensor readings labelled as Healthy/Warning/Failure
2. **Preprocessing**: Missing values → median fill; Features → StandardScaler normalisation
3. **Training**: XGBoost (200 trees, depth 5) + Logistic Regression (baseline)
4. **Prediction**: New readings are scaled and fed to the XGBoost classifier
5. **Confidence**: `predict_proba()` returns probability for each class
6. **RCA**: Rule-based engine maps sensor thresholds to probable causes

### Machine Condition Classes
- **0 = Healthy** — All sensors in normal range
- **1 = Warning** — One or more sensors approaching critical limits
- **2 = Failure** — Critical sensor readings, immediate action required

---

## 🗄️ Database Schema (SQLite)

```sql
-- Predictions log
CREATE TABLE predictions (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    temperature REAL,
    vibration   REAL,
    pressure    REAL,
    rpm         REAL,
    condition   TEXT,
    confidence  REAL,
    timestamp   TEXT
);

-- Threshold alerts
CREATE TABLE alerts (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    machine_id  TEXT,
    alert_type  TEXT,
    message     TEXT,
    timestamp   TEXT
);

-- Chat history
CREATE TABLE chat_history (
    id        INTEGER PRIMARY KEY AUTOINCREMENT,
    role      TEXT,
    message   TEXT,
    timestamp TEXT
);
```

---

## 🤖 Chatbot Capabilities

The AI Assistant can answer:
- *"What is the machine status?"*
- *"What is the failure probability?"*
- *"What is the temperature / vibration / pressure / RPM?"*
- *"Are there any anomalies?"*
- *"What maintenance action should I take?"*
- *"What are the maintenance recommendations?"*

---

## 📦 Tech Stack

| Layer | Technology |
|-------|-----------|
| ML | XGBoost, Scikit-learn |
| Backend | Flask, Flask-CORS |
| Database | SQLite (via sqlite3) |
| Data | Pandas, NumPy |
| Visualisation | Matplotlib, Seaborn |
| Frontend | HTML5, CSS3, Vanilla JS, Chart.js |
| Model Persistence | Joblib |

---

*Built as a Final Year Engineering Project — Predictive Maintenance using Machine Learning*

