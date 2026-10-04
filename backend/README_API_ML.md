# API Endpoints for ML & Predictions

Complete guide to API Endpoints for Machine Learning and Predictions

## Base URL
```
http://localhost:8000/api/v1/ml
```

## Authentication
All endpoints require authentication:
```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Endpoints

### 1. Get Predictions

**GET** `/api/v1/ml/predictions`

Get the list of predictions

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `model_type` (optional): filter by model type
- `limit` (default: 100, max: 1000): number of predictions

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/ml/predictions?well_id=Well_01&model_type=predictive_maintenance" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
[
  {
    "prediction_id": "uuid",
    "well_id": "Well_01",
    "model_type": "predictive_maintenance",
    "prediction_value": 0.75,
    "confidence_score": 0.85,
    "prediction_type": "failure_probability",
    "timestamp": "2024-01-01T12:00:00Z",
    "features": {
      "motor_temperature_mean": 78.5,
      "vibration_mean": 2.5
    }
  }
]
```

### 2. Create a New Prediction

**POST** `/api/v1/ml/predict`

Create a new prediction

**Request Body:**
```json
{
  "well_id": "Well_01",
  "model_type": "predictive_maintenance",
  "features": {
    "additional_feature": 123.45
  }
}
```

**Model Types:**
- `anomaly_detection`: anomaly detection
- `predictive_maintenance`: failure prediction
- `production_optimization`: production optimization

**Example:**
```bash
curl -X POST "http://localhost:8000/api/v1/ml/predict" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "well_id": "Well_01",
    "model_type": "predictive_maintenance"
  }'
```

### 3. Get Anomalies

**GET** `/api/v1/ml/anomalies`

Get detected anomalies

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `start_time` (optional): start time
- `end_time` (optional): end time
- `threshold` (default: 0.5): anomaly score threshold

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/ml/anomalies?well_id=Well_01&threshold=0.7" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
[
  {
    "prediction_id": "uuid",
    "well_id": "Well_01",
    "anomaly_score": 0.85,
    "confidence": 0.92,
    "timestamp": "2024-01-01T12:00:00Z",
    "features": {
      "latest_motor_temperature": 98.5,
      "latest_vibration": 4.5
    }
  }
]
```

### 4. Get Real-time Anomalies

**GET** `/api/v1/ml/anomalies/realtime`

Get anomalies from the past hour

**Query Parameters:**
- `well_id` (optional): filter by well ID

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/ml/anomalies/realtime" \
  -H "Authorization: Bearer TOKEN"
```

### 5. Get Latest Predictions

**GET** `/api/v1/ml/predictions/latest`

Get the latest prediction for each well/model

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `model_type` (optional): filter by model type

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/ml/predictions/latest" \
  -H "Authorization: Bearer TOKEN"
```

### 6. Get the Models List

**GET** `/api/v1/ml/models`

Get the list of available models

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/ml/models" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
[
  {
    "model_type": "anomaly_detection",
    "model_name": "Anomaly Detection Model",
    "version": "1.0.0",
    "status": "active",
    "accuracy": 0.92,
    "last_trained": "2024-01-01T00:00:00Z",
    "description": "Detects anomalies in sensor data"
  }
]
```

### 7. Get Model Performance

**GET** `/api/v1/ml/models/{model_type}/performance`

Get model performance metrics

**Path Parameters:**
- `model_type`: model type

**Query Parameters:**
- `start_time` (optional): start time
- `end_time` (optional): end time

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/ml/models/predictive_maintenance/performance" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "model_type": "predictive_maintenance",
  "total_predictions": 1000,
  "average_confidence": 0.85,
  "predictions_by_well": {
    "Well_01": 250,
    "Well_02": 300
  },
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-31T23:59:59Z"
  }
}
```

### 8. Training Model

**POST** `/api/v1/ml/models/train`

Train a new model (admin only)

**Request Body:**
```json
{
  "model_type": "anomaly_detection",
  "well_ids": ["Well_01", "Well_02"],
  "start_date": "2024-01-01T00:00:00Z",
  "end_date": "2024-06-30T23:59:59Z",
  "parameters": {
    "n_estimators": 100,
    "contamination": 0.1
  }
}
```

**Example:**
```bash
curl -X POST "http://localhost:8000/api/v1/ml/models/train" \
  -H "Authorization: Bearer ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "model_type": "anomaly_detection",
    "well_ids": ["Well_01", "Well_02"]
  }'
```

**Response:**
```json
{
  "training_id": "uuid",
  "model_type": "anomaly_detection",
  "status": "started",
  "message": "Training started for anomaly_detection model",
  "started_at": "2024-01-01T12:00:00Z",
  "completed_at": null,
  "metrics": null
}
```

## Model Types

### 1. Anomaly Detection
- **Type**: `anomaly_detection`
- **Output**: Anomaly score (0-1)
- **Use Case**: Detecting anomalies in sensor data

### 2. Predictive Maintenance
- **Type**: `predictive_maintenance`
- **Output**: Failure probability (0-1)
- **Use Case**: Predicting the probability of equipment failure

### 3. Production Optimization
- **Type**: `production_optimization`
- **Output**: Optimal value (e.g., optimal flow rate)
- **Use Case**: Optimizing production parameters

## Prediction Types

- `anomaly_score`: anomaly score (0-1)
- `failure_probability`: failure probability (0-1)
- `optimal_value`: optimal value

## Features

Features are extracted automatically from the latest sensor readings:
- Statistical features (mean, min, max, std)
- Latest sensor values
- Trend features

You can also send additional features in the request.

## Permissions

- `VIEW_ML_PREDICTIONS`: to view predictions
- `CREATE_ML_PREDICTIONS`: to create a prediction
- `TRAIN_MODELS`: to train models (admin only)

## Best Practices

1. **Caching**: Use `/predictions/latest` for real-time
2. **Threshold**: Set an appropriate threshold for anomalies
3. **Features**: Send additional features if needed
4. **Monitoring**: Check model performance regularly
5. **Training**: Retrain models periodically

## Error Responses

### 403 Forbidden
```json
{
  "detail": "Permission denied: TRAIN_MODELS required"
}
```

### 400 Bad Request
```json
{
  "detail": "Invalid model_type. Must be one of: anomaly_detection, predictive_maintenance, production_optimization"
}
```

### 404 Not Found
```json
{
  "detail": "No sensor data found for well Well_01"
}
```

