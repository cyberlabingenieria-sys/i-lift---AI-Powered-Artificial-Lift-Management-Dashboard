# Data Ingestion Guide

The Data Ingestion system is designed to receive real-time data from various sources.

## Supported Data Sources

### 1. MQTT (IoT Sensors)
For receiving data from IoT sensors that use the MQTT protocol.

### 2. OPC-UA (Industrial Systems)
For connecting to industrial systems that use the OPC-UA protocol.

### 3. REST API
For receiving data via HTTP REST API.

## Architecture

```
[Data Sources] → [Ingestion Service] → [Kafka] → [Processing Pipeline]
     ↓                ↓                    ↓              ↓
  MQTT          Validation          Message Queue    Database
  OPC-UA         Normalization       Streaming        Analytics
  REST API       Quality Check
```

## Usage

### 1. REST API Ingestion

#### Ingest Single Sensor Reading
```bash
curl -X POST "http://localhost:8000/api/v1/ingestion/sensor" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "well_id": "Well_01",
    "sensor_type": "motor_temperature",
    "sensor_value": 75.5,
    "measurement_unit": "C",
    "timestamp": "2024-01-01T12:00:00Z"
  }'
```

#### Ingest Batch Data
```bash
curl -X POST "http://localhost:8000/api/v1/ingestion/sensor/batch" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "readings": [
      {
        "well_id": "Well_01",
        "sensor_type": "motor_temperature",
        "sensor_value": 75.5
      },
      {
        "well_id": "Well_01",
        "sensor_type": "intake_pressure",
        "sensor_value": 500.0
      }
    ]
  }'
```

#### Ingest Raw Data
```bash
curl -X POST "http://localhost:8000/api/v1/ingestion/raw" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "device_id": "sensor_001",
    "metric": "temperature",
    "value": 75.5,
    "timestamp": "2024-01-01T12:00:00Z"
  }'
```

### 2. MQTT Ingestion

#### Setting Up the MQTT Broker (Mosquitto)
```bash
# Install Mosquitto
sudo apt-get install mosquitto mosquitto-clients

# Start Mosquitto
mosquitto -c /etc/mosquitto/mosquitto.conf
```

#### Setting Up MQTT Ingestion
```bash
cd backend
python scripts/start_mqtt_ingestion.py
```

#### Configuring MQTT Topics
The MQTT client listens to these topics by default:
- `sensors/+/+` - sensors/well_id/sensor_type
- `wells/+/data` - wells/well_id/data

#### Sending Test Data to MQTT
```bash
mosquitto_pub -h localhost -t "sensors/Well_01/motor_temperature" \
  -m '{"well_id": "Well_01", "sensor_type": "motor_temperature", "sensor_value": 75.5}'
```

### 3. OPC-UA Ingestion

#### Setting Up the OPC-UA Server (FreeOpcUa)
```bash
# Install FreeOpcUa server
pip install freeopcua

# Run server
python -m opcua.server
```

#### Setting Up OPC-UA Ingestion
```bash
cd backend
python scripts/start_opcua_ingestion.py
```

#### Configuring Node IDs
In the `start_opcua_ingestion.py` file you can set the Node IDs:
```python
node_ids = [
    "ns=2;s=Well_01.MotorTemperature",
    "ns=2;s=Well_01.IntakePressure",
]
```

## Data Validation

The system automatically validates received data:

### Validation Rules
- **Required Fields**: well_id, sensor_type, sensor_value
- **Value Ranges**: Checking value ranges based on sensor type
- **Format Validation**: Checking the format of well_id and timestamp
- **Quality Score**: Calculating the data quality score (0-100)

### Sensor Ranges
```python
{
    'motor_temperature': {'min': 65, 'max': 120, 'unit': 'C'},
    'intake_pressure': {'min': 450, 'max': 600, 'unit': 'psi'},
    'discharge_pressure': {'min': 800, 'max': 1200, 'unit': 'psi'},
    'vibration': {'min': 0.5, 'max': 5.0, 'unit': 'g'},
    'current': {'min': 30, 'max': 80, 'unit': 'A'},
    'flow_rate': {'min': 1500, 'max': 2500, 'unit': 'bpd'},
}
```

## Kafka Integration

All validated data is sent to Kafka:

- **Topic**: `sensor-data` (configurable)
- **Key**: well_id or node_id
- **Format**: JSON
- **Compression**: gzip

## Statistics

To view ingestion statistics:

```bash
curl -X GET "http://localhost:8000/api/v1/ingestion/stats" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

Response:
```json
{
  "total_received": 1000,
  "total_validated": 950,
  "total_sent_to_kafka": 950,
  "total_errors": 50,
  "sources": {
    "mqtt": 600,
    "opcua": 300,
    "rest": 100
  },
  "is_running": true,
  "mqtt_connected": true,
  "opcua_connected": true
}
```

## API Endpoints

### POST `/api/v1/ingestion/sensor`
Ingest single sensor reading

### POST `/api/v1/ingestion/sensor/batch`
Ingest batch of sensor readings (max 1000)

### POST `/api/v1/ingestion/raw`
Ingest raw data in any format

### GET `/api/v1/ingestion/stats`
Get ingestion statistics

## Configuration

In the `.env` file:
```env
# Kafka
KAFKA_BOOTSTRAP_SERVERS=localhost:9092
KAFKA_TOPIC_SENSOR_DATA=sensor-data

# MQTT (optional)
MQTT_BROKER_HOST=localhost
MQTT_BROKER_PORT=1883
MQTT_USERNAME=
MQTT_PASSWORD=

# OPC-UA (optional)
OPCUA_ENDPOINT_URL=opc.tcp://localhost:4840/freeopcua/server/
```

## Troubleshooting

### MQTT Connection Failed
- Check that the MQTT broker is running
- Check the firewall and port 1883
- Check the credentials

### OPC-UA Connection Failed
- Check that the OPC-UA server is running
- Check the endpoint URL
- Check the Node IDs

### Kafka Send Failed
- Check that the Kafka broker is running
- Check the connection string
- Check that the topic exists

## Best Practices

1. **Batch Processing**: Use the batch endpoint for large volumes of data
2. **Error Handling**: Always check the response
3. **Monitoring**: Check ingestion statistics regularly
4. **Validation**: Validate data before sending
5. **Retry Logic**: Retry on error

