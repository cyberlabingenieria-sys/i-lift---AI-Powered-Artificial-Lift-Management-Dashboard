# API Endpoints for Analytics

Complete guide to API Endpoints for Analytics and KPI

## Base URL
```
http://localhost:8000/api/v1/analytics
```

## Authentication
All endpoints require authentication:
```
Authorization: Bearer YOUR_ACCESS_TOKEN
```

## Endpoints

### 1. Get KPI Analytics

**GET** `/api/v1/analytics/kpi`

Get KPI analytics for wells

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `start_time` (optional): start time (ISO format)
- `end_time` (optional): end time (ISO format)

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/analytics/kpi?well_id=Well_01" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "kpis": {
    "total_readings": 10000,
    "active_wells": 5,
    "time_range": {
      "start": "2024-01-01T00:00:00Z",
      "end": "2024-01-31T23:59:59Z"
    },
    "motor_temperature_avg": 75.2,
    "motor_temperature_min": 65.0,
    "motor_temperature_max": 95.0,
    "intake_pressure_avg": 525.5,
    "flow_rate_avg": 2000.0,
    "average_efficiency": 40.5,
    "data_quality_percentage": 95.2,
    "average_pressure_differential": 475.0
  }
}
```

### 2. Get Trend Analysis

**GET** `/api/v1/analytics/trends`

Trend analysis for a metric

**Query Parameters:**
- `well_id` (required): Well ID
- `metric` (required): metric name (temperature, pressure, flow, vibration, current)
- `days` (default: 30, max: 365): number of days

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/analytics/trends?well_id=Well_01&metric=temperature&days=30" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "well_id": "Well_01",
  "metric": "temperature",
  "sensor_type": "motor_temperature",
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-31T23:59:59Z",
    "days": 30
  },
  "data_points": [
    {
      "date": "2024-01-01T00:00:00Z",
      "avg": 75.2,
      "min": 72.0,
      "max": 78.5,
      "count": 24
    }
  ],
  "trend": {
    "direction": "increasing",
    "slope": 0.15,
    "strength": 0.8,
    "intercept": 74.5
  },
  "statistics": {
    "current": 76.5,
    "average": 75.2,
    "change_percent": 1.7
  }
}
```

### 3. Compare Wells (Comparison)

**GET** `/api/v1/analytics/comparison`

Compare several wells for a metric

**Query Parameters:**
- `well_ids` (required): list of well IDs (2-10 wells)
- `metric` (required): metric to compare
- `start_time` (optional): start time
- `end_time` (optional): end time

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/analytics/comparison?well_ids=Well_01&well_ids=Well_02&well_ids=Well_03&metric=flow" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "metric": "flow",
  "sensor_type": "flow_rate",
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-31T23:59:59Z"
  },
  "wells": {
    "Well_01": {
      "avg": 2100.5,
      "min": 1800.0,
      "max": 2400.0,
      "std": 150.2,
      "count": 720
    },
    "Well_02": {
      "avg": 1950.0,
      "min": 1700.0,
      "max": 2200.0,
      "std": 120.5,
      "count": 720
    }
  },
  "rankings": {
    "Well_01": 1,
    "Well_02": 2
  },
  "best_performer": "Well_01",
  "worst_performer": "Well_02"
}
```

### 4. Get Performance Metrics

**GET** `/api/v1/analytics/performance`

Get performance metrics

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `start_time` (optional): start time
- `end_time` (optional): end time

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/analytics/performance" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "total_readings": 50000,
  "active_wells": 10,
  "data_quality_score": 95.5,
  "average_efficiency": 40.2,
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-31T23:59:59Z"
  },
  "well_performance": {
    "Well_01": {
      "readings_count": 5000,
      "sensor_types": ["motor_temperature", "intake_pressure", "flow_rate"]
    }
  }
}
```

### 5. Get Time Series Data

**GET** `/api/v1/analytics/timeseries`

Get time series data for visualization

**Query Parameters:**
- `well_id` (required): Well ID
- `sensor_type` (required): sensor type
- `start_time` (required): start time
- `end_time` (required): end time
- `aggregation` (default: "hourly"): aggregation level (raw, hourly, daily)
- `limit` (default: 1000, max: 10000): maximum number of data points

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/analytics/timeseries?well_id=Well_01&sensor_type=motor_temperature&start_time=2024-01-01T00:00:00Z&end_time=2024-01-07T00:00:00Z&aggregation=daily" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "metric": "motor_temperature",
  "sensor_type": "motor_temperature",
  "data_points": [
    {
      "timestamp": "2024-01-01T00:00:00Z",
      "value": 75.2,
      "well_id": "Well_01"
    }
  ],
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-07T00:00:00Z"
  }
}
```

### 6. Get Analytics Summary

**GET** `/api/v1/analytics/summary`

Get a complete analytics summary

**Query Parameters:**
- `well_id` (optional): filter by well ID
- `days` (default: 30, max: 365): number of days

**Example:**
```bash
curl -X GET "http://localhost:8000/api/v1/analytics/summary?days=7" \
  -H "Authorization: Bearer TOKEN"
```

**Response:**
```json
{
  "kpis": {...},
  "performance": {...},
  "time_range": {
    "start": "2024-01-25T00:00:00Z",
    "end": "2024-02-01T00:00:00Z",
    "days": 7
  }
}
```

## Supported Metrics

- `temperature` → motor_temperature
- `pressure` → intake_pressure
- `flow` → flow_rate
- `vibration` → vibration
- `current` → current

## Calculated KPIs

### Basic KPIs:
- `total_readings`: total number of readings
- `active_wells`: number of active wells
- `data_quality_percentage`: data quality percentage

### Sensor-specific KPIs:
- `{sensor_type}_avg`: average
- `{sensor_type}_min`: minimum
- `{sensor_type}_max`: maximum
- `{sensor_type}_std`: standard deviation

### Derived KPIs:
- `average_efficiency`: average efficiency (flow_rate / current)
- `average_pressure_differential`: pressure differential (discharge - intake)

## Trend Analysis

### Trend Direction:
- `increasing`: upward trend
- `decreasing`: downward trend
- `stable`: stable trend

### Trend Strength:
- Calculated based on slope and standard deviation
- The higher it is, the stronger the trend

## Permissions

- `VIEW_ANALYTICS`: to view analytics
- `EXPORT_ANALYTICS`: to export data

## Best Practices

1. **Time Ranges**: Limit the time range (max 1 year)
2. **Aggregation**: Use aggregation for historical data
3. **Caching**: Cache analytics results
4. **Pagination**: Use limit for time series
5. **Comparison**: Compare at most 10 wells at once

## Performance Tips

- Use aggregation to reduce data volume
- Use the cache for real-time analytics
- Limit the time range
- Use database indexes

