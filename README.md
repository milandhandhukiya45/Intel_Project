# Sensor Fault Detection Backend

This is the Flask backend for the Scania Truck Air Pressure System Fault Detection application. It provides RESTful APIs for anomaly detection, fault classification, and root cause analysis using machine learning techniques.

## Features

- **Anomaly Detection**: Uses Z-Score method to detect sensor anomalies
- **Fault Classification**: Implements Random Forest classifier for fault categorization
- **Root Cause Analysis**: Identifies critical sensors using feature importance
- **File Upload**: Handles CSV file uploads for sensor data
- **RESTful API**: Provides clean API endpoints for frontend integration

## API Endpoints

### Health Check
- `GET /api/health` - Check if the API is running

### File Operations
- `POST /api/upload` - Upload CSV file with sensor data
- `GET /api/data-stats` - Get statistics about uploaded data

### Analysis Operations
- `POST /api/detect-anomalies` - Detect anomalies using Z-Score method
- `POST /api/classify-faults` - Classify faults using Random Forest
- `POST /api/root-cause` - Identify root cause sensors

## Setup Instructions

### Prerequisites
- Python 3.8 or higher
- pip (Python package installer)

### Installation

1. **Navigate to the backend directory:**
   ```bash
   cd backend
   ```

2. **Create a virtual environment (recommended):**
   ```bash
   python -m venv venv
   
   # On Windows
   venv\Scripts\activate
   
   # On macOS/Linux
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Generate sample data (optional):**
   ```bash
   python generate_sample_data.py
   ```

5. **Run the Flask application:**
   ```bash
   python app.py
   ```

The server will start on `http://localhost:5000`

## Data Format

The backend expects CSV files with the following format:

- **Features**: Multiple sensor columns (e.g., `aa_000`, `ab_001`, `ac_002`, etc.)
- **Target**: A `class` column with fault labels:
  - `Normal` - Normal operation
  - `Fault Class 1` - High pressure fault
  - `Fault Class 2` - Low pressure fault
  - `Fault Class 3` - Sensor malfunction

### Example CSV Structure:
```csv
aa_000,ab_001,ac_002,ad_003,ae_004,af_005,ag_005,ag_006,ah_007,ai_008,aj_009,ak_010,al_011,am_012,an_013,ao_014,class
2.3,2.4,2.1,2.5,2.2,2.3,20.5,2.2,25.0,23.0,2.4,2.1,2.3,2.5,2.2,2.3,Normal
3.5,3.6,3.2,3.7,3.4,3.5,35.0,3.4,40.0,38.0,3.6,3.2,3.5,3.7,3.4,3.5,Fault Class 1
```

## API Usage Examples

### 1. Upload File
```bash
curl -X POST -F "file=@sample_sensor_data.csv" http://localhost:5000/api/upload
```

### 2. Detect Anomalies
```bash
curl -X POST http://localhost:5000/api/detect-anomalies
```

### 3. Classify Faults
```bash
curl -X POST http://localhost:5000/api/classify-faults
```

### 4. Root Cause Analysis
```bash
curl -X POST http://localhost:5000/api/root-cause
```

## Response Formats

### Anomaly Detection Response
```json
{
  "type": "anomalies",
  "data": {
    "total_samples": 60000,
    "anomalies": 4560,
    "anomaly_rate": 7.6,
    "critical_anomalies": 1230,
    "major_anomalies": 2100,
    "minor_anomalies": 1230
  },
  "timestamp": "2024-01-15T10:30:00"
}
```

### Classification Response
```json
{
  "type": "classification",
  "data": {
    "accuracy": 94.2,
    "precision": 91.8,
    "recall": 89.5,
    "f1_score": 90.6,
    "classes": {
      "Normal": 52340,
      "Fault Class 1": 4560,
      "Fault Class 2": 2100,
      "Fault Class 3": 1000
    }
  },
  "timestamp": "2024-01-15T10:30:00"
}
```

### Root Cause Response
```json
{
  "type": "rootcause",
  "data": {
    "top_sensors": [
      {
        "name": "aa_000",
        "importance": 0.156,
        "description": "Air Pressure Sensor A"
      }
    ]
  },
  "timestamp": "2024-01-15T10:30:00"
}
```

## Configuration

### Environment Variables
You can configure the application using environment variables:

- `FLASK_ENV`: Set to `development` for debug mode
- `FLASK_PORT`: Port number (default: 5000)
- `FLASK_HOST`: Host address (default: 0.0.0.0)

### Model Parameters
The Random Forest model parameters can be adjusted in the `SensorFaultDetector` class:

- `n_estimators`: Number of trees (default: 100)
- `max_depth`: Maximum depth of trees (default: 10)
- `z_score_threshold`: Threshold for anomaly detection (default: 3.0)

## Error Handling

The API includes comprehensive error handling:

- **400 Bad Request**: Invalid file format or missing data
- **404 Not Found**: Requested resource not found
- **500 Internal Server Error**: Server-side processing errors

All error responses include a descriptive message:
```json
{
  "error": "Error description"
}
```

## Development

### Running in Development Mode
```bash
export FLASK_ENV=development
python app.py
```

### Running with Gunicorn (Production)
```bash
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

## Dependencies

- **Flask**: Web framework
- **Flask-CORS**: Cross-origin resource sharing
- **pandas**: Data manipulation and analysis
- **numpy**: Numerical computing
- **scikit-learn**: Machine learning algorithms
- **joblib**: Model persistence
- **Werkzeug**: WSGI utilities
- **gunicorn**: WSGI HTTP Server (production)

## License

MIT
