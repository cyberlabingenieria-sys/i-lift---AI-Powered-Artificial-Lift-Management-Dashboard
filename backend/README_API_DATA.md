# API Endpoints for Data

Complete guide to API Endpoints for working with sensor data

## Base URL
```
http://localhost:8000/api/v1/sensors
```

## Authentication
All endpoints require authentication:
```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Endpoints

### 1. Get the Sensor Readings List

**GET** `/api/v1/sensors/`

Get the list of readings with filtering and pagination

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `sensor_type` (optional): filter by sensor type
- `start_time` (optional): start time (ISO format)
- `end_time` (optional): end time (ISO format)
- `limit` (default: 100, max: 1000): number of records
- `offset` (default: 0): offset for pagination

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/sensors/?well_id=Well_01&sensor_type=motor_temperature&limit=50" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "readings": [
    {
      "reading_id": "uuid",
      "well_id": "Well_01",
      "sensor_type": "motor_temperature",
      "sensor_value": 75.5,
      "measurement_unit": "C",
      "data_quality": 95,
      "timestamp": "2024-01-01T12:00:00Z",
      "created_at": "2024-01-01T12:00:01Z"
    }
  ],
  "total": 1000,
  "offset": 0,
  "limit": 50
}
```

### 2. Get Latest Readings

**GET** `/api/v1/sensors/latest`

Get the latest reading for each sensor

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `sensor_type` (optional): filter by sensor type

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/sensors/latest?well_id=Well_01" \
  -H "Authorization: Bearer TOKEN"
```

### 3. Get Real-time Data

**GET** `/api/v1/sensors/realtime`

Get real-time data (cached, updated every 5 seconds)

**Query Parameters:**
- `well_id` (optional): filter by well ID

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/sensors/realtime" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "data": {
    "Well_01": {
      "motor_temperature": {
        "value": 75.5,
        "unit": "C",
        "timestamp": "2024-01-01T12:00:00Z",
        "quality": 95
      },
      "intake_pressure": {
        "value": 500.0,
        "unit": "psi",
        "timestamp": "2024-01-01T12:00:00Z",
        "quality": 98
      }
    }
  },
  "timestamp": "2024-01-01T12:00:05Z"
}
```

### 4. Create a Sensor Reading

**POST** `/api/v1/sensors/`

Create a new reading

**Request Body:**
```json
{
  "well_id": "Well_01",
  "sensor_type": "motor_temperature",
  "sensor_value": 75.5,
  "measurement_unit": "C",
  "data_quality": 95,
  "timestamp": "2024-01-01T12:00:00Z"
}
```

**Example:**
```bash
curl -X POST "http://localhost:8000/api/v1/sensors/" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "well_id": "Well_01",
    "sensor_type": "motor_temperature",
    "sensor_value": 75.5,
    "measurement_unit": "C"
  }'
```

### 5. Get Aggregated Data

**GET** `/api/v1/sensors/aggregated`

Get aggregated data (hourly, daily, weekly)

**Query Parameters:**
- `well_id` (required): Well ID
- `sensor_type` (required): sensor type
- `start_time` (required): start time
- `end_time` (required): end time
- `aggregation` (default: "hourly"): aggregation type (hourly, daily, weekly)

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/sensors/aggregated?well_id=Well_01&sensor_type=motor_temperature&start_time=2024-01-01T00:00:00Z&end_time=2024-01-07T00:00:00Z&aggregation=daily" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
[
  {
    "timestamp": "2024-01-01T00:00:00Z",
    "well_id": "Well_01",
    "sensor_type": "motor_temperature",
    "avg_value": 75.2,
    "min_value": 72.0,
    "max_value": 78.5,
    "std_value": 1.5,
    "count": 24
  }
]
```

### 6. Get Statistics

**GET** `/api/v1/sensors/statistics`

Get statistics for sensor data

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `sensor_type` (optional): filter by sensor type
- `start_time` (optional): start time
- `end_time` (optional): end time

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/sensors/statistics?well_id=Well_01" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "statistics": {
    "Well_01_motor_temperature": {
      "well_id": "Well_01",
      "sensor_type": "motor_temperature",
      "count": 10000,
      "avg": 75.2,
      "min": 65.0,
      "max": 95.0,
      "std": 3.5
    }
  }
}
```

### 7. Export Data

**GET** `/api/v1/sensors/export`

Export data in various formats

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `sensor_type` (optional): filter by sensor type
- `start_time` (optional): start time
- `end_time` (optional): end time
- `format` (default: "csv"): export format (csv, json, parquet)
- `limit` (default: 10000, max: 100000): number of records

**Example:**
```bash
# Export to CSV
curl -X GET "http://localhost:8000/api/v1/sensors/export?well_id=Well_01&format=csv" \
  -H "Authorization: Bearer TOKEN" \
  -o sensor_data.csv

# Export to JSON
curl -X GET "http://localhost:8000/api/v1/sensors/export?well_id=Well_01&format=json" \
  -H "Authorization: Bearer TOKEN" \
  -o sensor_data.json

# Export to Parquet
curl -X GET "http://localhost:8000/api/v1/sensors/export?well_id=Well_01&format=parquet" \
  -H "Authorization: Bearer TOKEN" \
  -o sensor_data.parquet
```

## Sensor Types

Supported sensors:
- `motor_temperature` - motor temperature (°C)
- `intake_pressure` - intake pressure (psi)
- `discharge_pressure` - discharge pressure (psi)
- `vibration` - vibration (g)
- `current` - current (A)
- `flow_rate` - flow rate (bpd)

## Permissions

- `VIEW_SENSOR_DATA`: to view data
- `CREATE_SENSOR_DATA`: to create a new reading

## Error Responses

### 403 Forbidden
```json
{
  "detail": "Permission denied"
}
```

### 404 Not Found
```json
{
  "detail": "No data found"
}
```

### 400 Bad Request
```json
{
  "detail": "End time must be after start time"
}
```

## Best Practices

1. **Pagination**: Use `limit` and `offset` for large datasets
2. **Caching**: Use the `/realtime` endpoint, which is cached
3. **Aggregation**: Use `/aggregated` for historical data
4. **Export**: Use Parquet for exporting large volumes
5. **Time Ranges**: Limit the time range (max 1 year for aggregated)

## Rate Limiting

- Real-time endpoint: 5 second cache
- Other endpoints: No specific rate limit (depends on server config)

