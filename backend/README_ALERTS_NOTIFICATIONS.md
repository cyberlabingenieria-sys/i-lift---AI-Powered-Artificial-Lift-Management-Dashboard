# Alert System and Notifications

Complete guide to the Alert and Notifications system

## Overview

The Alert system includes:
- **Alert Rules Engine**: Defining and evaluating alert rules
- **Alert Detection Service**: Automatic detection of alerts from sensor data
- **Notification Service**: Sending notifications through various channels
- **Alert Management**: Managing alerts (create, resolve, delete)

## Alert Rules

### Default Rules

The system starts with the following default rules:

1. **Motor Temperature Critical** (>95°C)
2. **Motor Temperature Warning** (>85°C)
3. **Motor Temperature Low** (<65°C)
4. **Intake Pressure Critical** (<400 PSI)
5. **Intake Pressure Warning** (<450 PSI)
6. **Vibration High** (>4.0 m/s²)
7. **Current High** (>75 A)
8. **Flow Rate Low** (<1500 bbl/day)

### Rule Conditions

- `gt`: Greater than threshold
- `lt`: Less than threshold
- `eq`: Equals threshold
- `between`: Between two thresholds

### Severity Levels

- `low`: needs attention
- `medium`: needs review
- `high`: needs immediate action
- `critical`: needs immediate action

## API Endpoints

### Alert Rules

#### GET `/api/v1/alert-rules/`
List all alert rules

#### POST `/api/v1/alert-rules/`
Create a new alert rule (admin only)

#### GET `/api/v1/alert-rules/{rule_id}`
Get a specific alert rule

#### PUT `/api/v1/alert-rules/{rule_id}`
Update an alert rule (admin only)

#### DELETE `/api/v1/alert-rules/{rule_id}`
Delete an alert rule (admin only)

### Notifications

#### POST `/api/v1/notifications/send`
Send a notification for an alert

#### GET `/api/v1/notifications/preferences`
Get the user's notification settings

#### PUT `/api/v1/notifications/preferences`
Update notification settings

## Notification Channels

### 1. Email
- For alerts with severity medium and above
- Configurable per user

### 2. SMS
- For alerts with critical severity
- Requires an SMS provider to be configured

### 3. Push Notifications
- For all alerts
- Real-time browser notifications

### 4. Webhook
- For integration with external systems
- POST request to a specified URL

## Alert Detection

### Automatic Detection

`AlertDetectionService` automatically:
1. Monitors sensor readings
2. Compares them with alert rules
3. Creates alerts
4. Sends notifications

### Manual Detection

You can manually check a well:

```python
from app.services.alert_detection_service import AlertDetectionService

service = AlertDetectionService()
alerts = await service.check_well_status("Well_01")
```

## Setting Up the Alert Monitor

```bash
python backend/scripts/start_alert_monitor.py
```

This script continuously monitors sensor data and creates alerts.

## Notification Configuration

### Email Configuration

```python
# TODO: Configure SMTP settings in config.py
SMTP_HOST = "smtp.gmail.com"
SMTP_PORT = 587
SMTP_USER = "your-email@gmail.com"
SMTP_PASSWORD = "your-password"
```

### SMS Configuration

```python
# TODO: Configure SMS provider (Twilio, etc.)
SMS_PROVIDER = "twilio"
SMS_ACCOUNT_SID = "your-account-sid"
SMS_AUTH_TOKEN = "your-auth-token"
SMS_FROM_NUMBER = "+1234567890"
```

### Push Notifications

```python
# TODO: Configure push notification service
PUSH_SERVICE_URL = "https://your-push-service.com"
PUSH_API_KEY = "your-api-key"
```

## Example Usage

### Creating a New Alert Rule

```bash
curl -X POST "http://localhost:8000/api/v1/alert-rules/" \
  -H "Authorization: Bearer ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "rule_id": "custom_rule_1",
    "name": "Custom Temperature Rule",
    "sensor_type": "motor_temperature",
    "condition": "gt",
    "threshold": 90.0,
    "severity": "high",
    "message_template": "Custom alert: {value}°C"
  }'
```

### Sending a Notification

```bash
curl -X POST "http://localhost:8000/api/v1/notifications/send" \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "alert_id": "uuid",
    "channels": ["email", "push"],
    "recipients": ["user@example.com"]
  }'
```

## Alert Lifecycle

1. **Detection**: Alert rule triggered
2. **Creation**: Alert created in database
3. **Notification**: Notifications sent via channels
4. **Resolution**: Alert resolved by user
5. **Archival**: Alert archived (optional)

## Best Practices

1. **Rule Design**: Design rules so they produce fewer false positives
2. **Severity Levels**: Use severity levels correctly
3. **Notification Channels**: Choose an appropriate channel for each severity level
4. **Monitoring**: Run the Alert monitor continuously
5. **Resolution**: Resolve alerts in a timely manner

## Integration

### With Sensor Service:
- Sensor readings are checked automatically
- Alerts are created in real time

### With ML Service:
- ML predictions can be triggers for alerts
- Anomaly detection alerts are created automatically

### With Dashboard:
- Alerts are displayed on the dashboard
- Real-time updates for alerts

