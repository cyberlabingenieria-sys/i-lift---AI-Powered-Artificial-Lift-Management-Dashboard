# Data Processing Pipeline Guide

The data processing pipeline is designed to process, in real time, data received from Kafka and store it in the database.

## Pipeline Architecture

```
[Kafka] → [Consumer] → [Stream Processor] → [Feature Engineer] → [Database Writer] → [TimescaleDB]
            ↓                ↓                      ↓                    ↓
        Message        Sliding Window        Statistical Features    Batch Insert
        Queue          Statistics            Time Features           Optimized
```

## Processing Stages

### 1. Kafka Consumer
- Receiving data from the Kafka topic `sensor-data`
- Deserialization JSON
- Error handling and retry

### 2. Stream Processing
- Real-time processing with a sliding window
- Calculating statistics (mean, std, min, max)
- Anomaly detection with z-score
- Calculating rate of change

### 3. Feature Engineering
- Time-based features (hour, day, month, cyclical)
- Statistical features (mean, std, percentiles)
- Trend features (slope, acceleration, volatility)
- Historical window analysis

### 4. Database Writer
- Batch writing for optimization
- Bulk insert into TimescaleDB
- Error handling and retry

## Usage

### Starting the Pipeline

#### Via API:
```bash
# Start pipeline
curl -X POST "http://localhost:8000/api/v1/processing/start" \
  -H "Authorization: Bearer ADMIN_TOKEN"

# Check stats
curl -X GET "http://localhost:8000/api/v1/processing/stats" \
  -H "Authorization: Bearer TOKEN"

# Health check
curl -X GET "http://localhost:8000/api/v1/processing/health" \
  -H "Authorization: Bearer TOKEN"

# Stop pipeline
curl -X POST "http://localhost:8000/api/v1/processing/stop" \
  -H "Authorization: Bearer ADMIN_TOKEN"
```

#### Via Script:
```bash
cd backend
python scripts/start_processing_pipeline.py
```

### Controlling the Pipeline

```bash
# Pause
curl -X POST "http://localhost:8000/api/v1/processing/pause" \
  -H "Authorization: Bearer ADMIN_TOKEN"

# Resume
curl -X POST "http://localhost:8000/api/v1/processing/resume" \
  -H "Authorization: Bearer ADMIN_TOKEN"
```

## Calculated Features

### Stream Processing Features
- `window_size`: number of readings in the window
- `mean`: average
- `median`: median
- `std`: standard deviation
- `min/max`: minimum/maximum
- `range`: range
- `change_from_mean`: change from the mean
- `change_percent`: percentage change
- `rate_of_change`: rate of change
- `z_score`: Z-score
- `is_anomaly`: anomaly detection

### Feature Engineering Features

#### Time Features:
- `hour`, `day_of_week`, `day_of_month`, `month`
- `is_weekend`
- `hour_sin/cos`, `day_sin/cos` (cyclical encoding)

#### Statistical Features:
- `mean`, `std`, `min`, `max`, `median`
- `percentile_25`, `percentile_75`
- `iqr` (Interquartile Range)

#### Trend Features:
- `trend`: slope of the trend line
- `acceleration`: acceleration of change
- `volatility`: volatility

#### Cyclical Features:
- `day_of_year`, `week_of_year`, `quarter`
- `day_of_year_sin/cos`

## Configuration

### Environment Variables:
```env
# Kafka
KAFKA_BOOTSTRAP_SERVERS=localhost:9092
KAFKA_TOPIC_SENSOR_DATA=sensor-data

# Processing
BATCH_SIZE=100
WINDOW_SIZE=60
```

### Pipeline Parameters:
```python
pipeline = DataProcessingPipeline(
    batch_size=100,      # number of records per batch
    window_size=60,      # window size in seconds
)
```

## Monitoring

### Statistics Response:
```json
{
  "total_processed": 10000,
  "total_errors": 5,
  "processing_rate": 50.5,
  "last_processed_time": 1234567890.123,
  "is_running": true,
  "database_writer": {
    "total_written": 10000,
    "total_errors": 0,
    "buffer_size": 45,
    "last_write_time": "2024-01-01T12:00:00Z"
  },
  "kafka_consumer_running": true
}
```

### Health Check Response:
```json
{
  "status": "healthy",
  "is_running": true,
  "kafka_connected": true,
  "total_processed": 10000,
  "total_errors": 5,
  "error_rate": 0.0005
}
```

## Performance Optimization

### Batch Writing
- Data is collected in a buffer
- When the buffer is full or flushed, a bulk insert is performed
- Reduces the number of write operations

### Sliding Window
- Only recent data is kept
- Limited memory with maxlen
- Fast computation

### Error Handling
- Errors are logged
- Processing continues
- Error statistics are kept

## Troubleshooting

### Pipeline Not Processing
1. Check that the Kafka consumer is connected
2. Check that the Kafka topic exists
3. Check the logs for errors

### High Error Rate
1. Check the data format
2. Check the database connection
3. Check validation errors

### Slow Processing
1. Increase batch_size
2. Decrease window_size
3. Check database performance

## Best Practices

1. **Monitoring**: Check pipeline statistics regularly
2. **Error Handling**: Log and monitor errors
3. **Batch Size**: Set batch_size based on throughput
4. **Window Size**: Set window_size based on analytics needs
5. **Health Checks**: Run health checks regularly

## Integration with Other Components

### With Ingestion:
- Validated data from ingestion goes to Kafka
- The pipeline reads from Kafka

### With Database:
- Processed data is stored in TimescaleDB
- Hypertables are used for optimization

### With ML Services:
- Engineered features are ready for ML models
- They can be used for training and prediction

