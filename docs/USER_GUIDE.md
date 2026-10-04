# User Guide

IntelliLift AI Dashboard User Guide

## Getting Started

### Login

1. Open the browser and go to `http://localhost:3000`
2. Enter username and password
3. Click "Login"

### Dashboard Overview

After login, you are directed to the Dashboard page, which includes:

- **KPI Widgets**: Key performance indicators
- **Real-time Metrics**: Real-time metrics
- **Well Status**: Status of wells
- **Active Alerts**: Active alerts
- **Activity Feed**: Activity feed

## Navigation

### Main Menu

- **Dashboard**: Main page
- **Wells**: Well management
- **Sensors**: Sensor management
- **Alerts**: Alert management
- **Analytics**: Analytics
- **ML Predictions**: ML predictions
- **Monitoring**: System monitoring
- **Settings**: Settings

## Features

### Wells Management

#### Viewing Wells

1. Go to "Wells" from the main menu
2. View the list of all wells
3. Filter by status, type, location

#### Creating a Well

1. Click "Add Well"
2. Fill out the form:
   - Name
   - Location
   - Coordinates (Latitude, Longitude)
   - Well Type (ESP, PCP, Gas Lift, Rod Pump)
3. Click "Create"

#### Viewing Well Details

1. Click on a well in the list
2. View:
   - General information
   - Sensors
   - Real-time data
   - History
   - Alerts

### Sensors Management

#### Viewing Sensors

1. Go to "Sensors"
2. View the list of sensors
3. Filter by well, type, status

#### Sensor Readings

1. Click on a sensor
2. View:
   - Current value
   - Historical chart
   - Statistics
   - Quality metrics

### Alerts

#### Viewing Alerts

1. Go to "Alerts"
2. View the list of alerts
3. Filter by:
   - Status (Active, Resolved)
   - Severity (Critical, Warning, Info)
   - Well
   - Date range

#### Resolving Alerts

1. Click on an alert
2. View details
3. Click "Resolve"
4. Enter resolution notes
5. Click "Confirm"

### Analytics

#### KPIs

1. Go to "Analytics" > "KPIs"
2. Select the well and time range
3. View:
   - Total readings
   - Average values
   - Min/Max values
   - Trends

#### Trends

1. Go to "Analytics" > "Trends"
2. Select:
   - Well(s)
   - Metric
   - Time range
3. View the trend chart

#### Comparison

1. Go to "Analytics" > "Comparison"
2. Select several wells
3. Select the metric
4. View the comparison

### ML Predictions

#### Viewing Predictions

1. Go to "ML Predictions"
2. View:
   - Anomaly detection results
   - Predictive maintenance forecasts
   - Failure predictions

#### Running Anomaly Detection

1. Go to "ML Predictions" > "Anomaly Detection"
2. Select:
   - Well
   - Sensor type
   - Time range
3. Click "Detect Anomalies"
4. View results

### Monitoring

#### System Health

1. Go to "Monitoring"
2. View:
   - Overall system status
   - Database health
   - Redis health
   - System resources (CPU, Memory)

#### Metrics

1. Go to "Monitoring" > "Metrics"
2. View:
   - Business metrics
   - System metrics
   - Performance metrics

## Tips & Tricks

### Keyboard Shortcuts

- `Ctrl + K`: Quick search
- `Esc`: Close modal
- `Ctrl + /`: Show shortcuts

### Filters

- Use filters to limit the results
- Save filters for later use
- Export filtered data

### Charts

- Zoom in/out with scroll
- Hover to see exact values
- Download the chart as an image

## Troubleshooting

### Common Issues

#### Cannot Login

- Check username and password
- Check the internet connection
- Contact the administrator

#### Data Not Loading

- Refresh the page
- Check the connection to the backend
- Clear browser cache

#### Charts Not Displaying

- Check the available data
- Check the time range
- Refresh the page

## Support

For help and support:
- **Documentation**: Refer to the documentation
- **Issues**: Report a problem on GitHub
- **Email**: support@example.com

