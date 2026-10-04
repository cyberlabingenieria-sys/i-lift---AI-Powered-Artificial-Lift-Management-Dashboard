# API Endpoints for Alerts

Complete guide to API Endpoints for managing Alerts

## Base URL
```
http://localhost:8000/api/v1/alerts
```

## Authentication
All endpoints require authentication:
```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Endpoints

### 1. Get the Alerts List

**GET** `/api/v1/alerts/`

Get the list of alerts with filtering and pagination

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `severity` (optional): filter by severity (low, medium, high, critical)
- `resolved` (optional): filter by resolved status (true/false)
- `alert_type` (optional): filter by alert type
- `start_time` (optional): start time
- `end_time` (optional): end time
- `limit` (default: 100, max: 1000): number of alerts
- `offset` (default: 0): offset for pagination

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/alerts/?well_id=Well_01&severity=critical&resolved=false" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "alerts": [
    {
      "alert_id": "uuid",
      "well_id": "Well_01",
      "alert_type": "temperature_high",
      "severity": "critical",
      "message": "Motor temperature exceeded critical threshold: 110°C",
      "sensor_type": "motor_temperature",
      "resolved": false,
      "created_at": "2024-01-01T12:00:00Z",
      "resolved_at": null
    }
  ],
  "total": 50,
  "offset": 0,
  "limit": 100
}
```

### 2. Get Unresolved Alerts

**GET** `/api/v1/alerts/unresolved`

Get unresolved alerts (cached, 30 seconds)

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `severity` (optional): filter by severity

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/alerts/unresolved?severity=critical" \
  -H "Authorization: Bearer TOKEN"
```

### 3. Get Critical Alerts

**GET** `/api/v1/alerts/critical`

Get unresolved critical alerts

**Query Parameters:**
- `well_id` (optional): filter by well ID

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/alerts/critical" \
  -H "Authorization: Bearer TOKEN"
```

### 4. Get an Alert by ID

**GET** `/api/v1/alerts/{alert_id}`

Get a specific alert

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/alerts/uuid" \
  -H "Authorization: Bearer TOKEN"
```

### 5. Create a New Alert

**POST** `/api/v1/alerts/`

Create a new alert

**Request Body:**
```json
{
  "well_id": "Well_01",
  "alert_type": "temperature_high",
  "severity": "critical",
  "message": "Motor temperature exceeded critical threshold",
  "sensor_type": "motor_temperature"
}
```

**Example:**
```bash
curl -X POST "http://localhost:8000/api/v1/alerts/" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "well_id": "Well_01",
    "alert_type": "temperature_high",
    "severity": "critical",
    "message": "Temperature too high"
  }'
```

### 6. Resolve Alert

**POST** `/api/v1/alerts/{alert_id}/resolve`

Resolve an alert

**Example:**
```bash
curl -X POST "http://localhost:8000/api/v1/alerts/uuid/resolve" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "alert_id": "uuid",
  "well_id": "Well_01",
  "alert_type": "temperature_high",
  "severity": "critical",
  "message": "Motor temperature exceeded critical threshold",
  "resolved": true,
  "created_at": "2024-01-01T12:00:00Z",
  "resolved_at": "2024-01-01T13:30:00Z"
}
```

### 7. Bulk Resolve Alerts

**POST** `/api/v1/alerts/bulk-resolve`

Resolve several alerts at once (max 100)

**Request Body:**
```json
{
  "alert_ids": ["uuid1", "uuid2", "uuid3"],
  "resolved_by": "username"
}
```

**Example:**
```bash
curl -X POST "http://localhost:8000/api/v1/alerts/bulk-resolve" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "alert_ids": ["uuid1", "uuid2"]
  }'
```

**Response:**
```json
{
  "total": 2,
  "resolved": 2,
  "failed": 0,
  "failed_ids": []
}
```

### 8. Delete an Alert

**DELETE** `/api/v1/alerts/{alert_id}`

Delete an alert (admin only)

**Example:**
```bash
curl -X DELETE "http://localhost:8000/api/v1/alerts/uuid" \
  -H "Authorization: Bearer ADMIN_TOKEN"
```

### 9. Get Alert Statistics

**GET** `/api/v1/alerts/statistics/summary`

Get alert statistics

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `start_time` (optional): start time
- `end_time` (optional): end time
- `days` (default: 30, max: 365): number of days

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/alerts/statistics/summary?days=7" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "total_alerts": 150,
  "resolved_count": 120,
  "unresolved_count": 30,
  "resolution_rate": 80.0,
  "severity_distribution": {
    "critical": 10,
    "high": 25,
    "medium": 50,
    "low": 65
  },
  "type_distribution": {
    "temperature_high": 40,
    "pressure_low": 30,
    "vibration_high": 20
  },
  "well_distribution": {
    "Well_01": 50,
    "Well_02": 40
  },
  "average_resolution_time_hours": 2.5,
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-31T23:59:59Z"
  }
}
```

### 10. Get Real-time Alert Count

**GET** `/api/v1/alerts/realtime/count`

Get the number of alerts in real time (cached)

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `severity` (optional): filter by severity

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/alerts/realtime/count" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "total": 15,
  "by_severity": {
    "critical": 3,
    "high": 5,
    "medium": 4,
    "low": 3
  },
  "timestamp": "2024-01-01T12:00:00Z"
}
```

## Alert Types

Supported alert types:
- `temperature_high`: high temperature
- `temperature_low`: low temperature
- `pressure_high`: high pressure
- `pressure_low`: low pressure
- `vibration_high`: high vibration
- `current_high`: high current
- `flow_low`: low flow
- `anomaly_detected`: anomaly detected
- `equipment_failure`: equipment failure
- `maintenance_due`: maintenance due

## Severity Levels

- `low`: low (needs attention)
- `medium`: medium (needs review)
- `high`: high (needs immediate action)
- `critical`: critical (needs immediate action)

## Permissions

- `VIEW_ALERTS`: to view alerts
- `CREATE_ALERTS`: to create an alert
- `RESOLVE_ALERTS`: to resolve alerts
- Admin: to delete alerts

## Best Practices

1. **Caching**: Use the `/unresolved` endpoint, which is cached
2. **Filtering**: Use filters to reduce data volume
3. **Bulk Operations**: Use the bulk endpoint to resolve several alerts
4. **Monitoring**: Check alert statistics regularly
5. **Real-time**: Use `/realtime/count` for the dashboard

## Error Responses

### 403 Forbidden
```json
{
  "detail": "Permission denied: RESOLVE_ALERTS required"
}
```

### 404 Not Found
```json
{
  "detail": "Alert not found"
}
```

### 400 Bad Request
```json
{
  "detail": "Invalid severity. Must be one of: low, medium, high, critical"
}
```

## Alert Lifecycle

1. **Created**: The alert is created
2. **Active**: The alert is active (unresolved)
3. **Resolved**: The alert is resolved (resolved_at set)
4. **Deleted**: The alert is deleted (admin only)

## Integration with Other Components

### With ML Service:
- ML predictions can create alerts
- Anomaly detection alerts are created automatically

### With Sensor Service:
- Sensor readings can be triggers for alerts
- Threshold-based alerts use sensor values

