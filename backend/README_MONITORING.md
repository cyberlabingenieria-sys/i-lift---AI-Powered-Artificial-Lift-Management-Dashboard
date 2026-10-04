# Monitoring and Observability

Complete guide to Monitoring and Observability features

## Overview

The Monitoring and Observability system includes:
- **Metrics Collection**: Collecting metrics with Prometheus
- **Health Checks**: Service health checks
- **System Monitoring**: System resource monitoring
- **Business Metrics**: Business-related metrics
- **Logging**: Recording and managing logs
- **Tracing**: Request tracing

## Metrics Collection

### Prometheus Integration

The system uses Prometheus to collect metrics.

#### HTTP Metrics:
- `http_requests_total`: total number of HTTP requests
- `http_request_duration_seconds`: duration of HTTP requests
- `http_request_size_bytes`: size of HTTP requests

#### Business Metrics:
- `sensor_readings_total`: total number of sensor readings
- `alerts_total`: total number of alerts
- `ml_predictions_total`: total number of ML predictions

#### System Metrics:
- `active_wells`: number of active wells
- `active_sensors`: number of active sensors
- `active_alerts`: number of active alerts
- `data_ingestion_rate`: data ingestion rate

#### Database Metrics:
- `db_query_duration_seconds`: duration of database queries
- `db_connections_active`: active database connections
- `db_connections_idle`: idle database connections

#### Kafka Metrics:
- `kafka_messages_produced`: number of produced messages
- `kafka_messages_consumed`: number of consumed messages
- `kafka_consumer_lag`: Kafka consumer lag

#### Redis Metrics:
- `redis_operations_total`: total number of Redis operations
- `redis_cache_hits`: number of cache hits
- `redis_cache_misses`: number of cache misses

### Accessing Metrics

To access Prometheus metrics:

```bash
GET /api/v1/monitoring/metrics
```

This endpoint returns metrics in the Prometheus text format.

## Health Checks

### Health Check Endpoints

#### Basic Health Check (Public):
```bash
GET /api/v1/monitoring/health
```

#### Detailed Health Check:
```bash
GET /api/v1/monitoring/health/detailed?include_system=true
```

This endpoint checks the health of all services:
- Database
- Redis
- Kafka
- ML Services (optional)
- System Resources (optional)

#### Health Summary:
```bash
GET /api/v1/monitoring/health/summary
```

For the dashboard - system health summary

### Health Check Response

```json
{
  "status": "healthy",
  "timestamp": "2024-01-01T00:00:00Z",
  "checks": {
    "database": {
      "status": "healthy",
      "response_time_ms": 2.5,
      "connection_pool": {
        "size": 10,
        "checked_in": 8,
        "checked_out": 2,
        "overflow": 0
      }
    },
    "redis": {
      "status": "healthy",
      "response_time_ms": 1.2,
      "connected_clients": 5,
      "used_memory_human": "10MB",
      "redis_version": "7.0.0"
    },
    "kafka": {
      "status": "healthy",
      "response_time_ms": 5.0,
      "topics_count": 3
    }
  }
}
```

## System Monitoring

### System Resources

The system monitors the following resources:
- **CPU**: CPU usage percentage
- **Memory**: Memory usage
- **Disk**: Disk usage

### Accessing System Metrics

```bash
GET /api/v1/monitoring/metrics/system
```

**Response:**
```json
{
  "status": "healthy",
  "cpu": {
    "percent": 45.2,
    "count": 8
  },
  "memory": {
    "total_gb": 16.0,
    "available_gb": 8.5,
    "used_percent": 46.9
  },
  "disk": {
    "total_gb": 500.0,
    "free_gb": 300.0,
    "used_percent": 40.0
  }
}
```

## Business Metrics

### Business Metrics Endpoint

```bash
GET /api/v1/monitoring/metrics/business?hours=24
```

**Response:**
```json
{
  "sensor_readings": 10000,
  "alerts": 25,
  "ml_predictions": 500,
  "active_wells": 10,
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-02T00:00:00Z"
  }
}
```

### Metrics Summary

```bash
GET /api/v1/monitoring/metrics/summary
```

**Response:**
```json
{
  "active_wells": 10,
  "active_sensors": 50,
  "active_alerts_by_severity": {
    "critical": 2,
    "warning": 5,
    "info": 10
  },
  "sensor_readings_last_hour": 1000,
  "alerts_last_hour": 5,
  "timestamp": "2024-01-01T00:00:00Z"
}
```

## Logging

### Structured Logging

The system uses structured logging with structlog.

### Log Levels

- **DEBUG**: Debug information
- **INFO**: General information
- **WARNING**: Warnings
- **ERROR**: Errors
- **CRITICAL**: Critical errors

### Accessing Logs

```bash
GET /api/v1/monitoring/logs?level=ERROR&hours=24&limit=100
```

**Query Parameters:**
- `level`: Filter by log level (optional)
- `hours`: Hours to look back (default: 24)
- `limit`: Maximum number of logs (default: 100)

## Tracing

### Distributed Tracing

The system uses distributed tracing to trace requests.

### Accessing Traces

```bash
GET /api/v1/monitoring/tracing?service=backend&hours=1&limit=50
```

**Query Parameters:**
- `trace_id`: Filter by trace ID (optional)
- `service`: Filter by service (optional)
- `hours`: Hours to look back (default: 1)
- `limit`: Maximum number of traces (default: 50)

## Integration with Middleware

### Metrics Middleware

`LoggingMiddleware` automatically records metrics:
- HTTP request metrics
- Response time
- Status codes
- Request sizes

### Usage in Code

```python
from app.services.metrics_service import metrics_service

# Record sensor reading
metrics_service.record_sensor_reading(
    well_id="well-1",
    sensor_type="temperature",
    status="success"
)

# Record alert
metrics_service.record_alert(
    severity="critical",
    alert_type="temperature_high",
    well_id="well-1"
)

# Record ML prediction
metrics_service.record_ml_prediction(
    model_type="anomaly_detection",
    well_id="well-1",
    status="success"
)
```

## Prometheus Configuration

### Prometheus Scrape Config

```yaml
scrape_configs:
  - job_name: 'backend-api'
    static_configs:
      - targets: ['backend:8000']
    metrics_path: '/api/v1/monitoring/metrics'
```

## Grafana Dashboards

### Recommended Dashboards

1. **System Health Dashboard**
   - Overall system status
   - Service health checks
   - System resources

2. **Business Metrics Dashboard**
   - Sensor readings
   - Alerts
   - ML predictions
   - Active wells/sensors

3. **Performance Dashboard**
   - HTTP request metrics
   - Response times
   - Error rates
   - Throughput

4. **Infrastructure Dashboard**
   - Database metrics
   - Redis metrics
   - Kafka metrics
   - Connection pools

## Alerting

### Metrics-Based Alerts

You can create Prometheus alerts based on metrics:

```yaml
groups:
  - name: system_alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
        for: 5m
        annotations:
          summary: "High error rate detected"
      
      - alert: HighResponseTime
        expr: http_request_duration_seconds{quantile="0.95"} > 1
        for: 5m
        annotations:
          summary: "High response time detected"
      
      - alert: DatabaseConnectionPoolExhausted
        expr: db_connections_active / db_connections_idle > 0.9
        for: 5m
        annotations:
          summary: "Database connection pool nearly exhausted"
```

## Best Practices

1. **Metrics Collection**: Instrument all important operations
2. **Health Checks**: Check health check endpoints regularly
3. **Alerting**: Set up appropriate alerts for important metrics
4. **Dashboard**: Create Grafana dashboards for visualization
5. **Logging**: Use structured logging
6. **Tracing**: Use distributed tracing for debugging

## Monitoring Stack

### Recommended Stack

- **Metrics**: Prometheus
- **Visualization**: Grafana
- **Logging**: ELK Stack or Loki
- **Tracing**: Jaeger or Zipkin
- **Alerting**: Alertmanager

## Performance Considerations

1. **Metrics Collection**: Minimize metrics collection overhead
2. **Sampling**: Use sampling for high-volume metrics
3. **Retention**: Set an appropriate retention policy for metrics
4. **Cardinality**: Avoid high-cardinality labels

