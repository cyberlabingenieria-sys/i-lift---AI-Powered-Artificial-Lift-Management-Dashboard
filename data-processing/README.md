# Synthetic Data Generator

A tool for generating realistic synthetic data for Artificial Lift systems.

## Features

- ✅ Data generation for multiple wells
- ✅ Realistic patterns (daily/weekly cycles)
- ✅ Failure scenarios
- ✅ Random anomalies
- ✅ Export to various formats (Parquet, CSV, JSON)
- ✅ Reproducibility with seed
- ✅ Statistics and reporting

## Usage

### Via Command Line

```bash
cd data-processing
python synthetic_data_generator.py
```

This command generates 6 months of data for 10 wells.

### Via Python API

```python
from synthetic_data_generator import SyntheticALSDataGenerator
from datetime import datetime, timedelta

# Initialize generator
generator = SyntheticALSDataGenerator(seed=42)

# Generate data for one well
start_date = datetime.now() - timedelta(days=180)
df = generator.generate_well_data(
    well_id="Well_01",
    start_date=start_date,
    days=180,
    interval_seconds=1,
    include_anomalies=True,
    include_failures=True,
    failure_probability=0.1,
)

# Generate for multiple wells
well_ids = ["Well_01", "Well_02", "Well_03"]
df = generator.generate_multiple_wells(
    well_ids=well_ids,
    start_date=start_date,
    days=180,
)

# Export to different formats
generator.export_to_parquet(df, "data/synthetic_data.parquet")
generator.export_to_csv(df, "data/synthetic_data.csv")
generator.export_to_json(df.head(1000), "data/synthetic_data_sample.json")

# Get statistics
stats = generator.get_statistics(df)
print(stats)
```

### Via REST API

```bash
# Generate synthetic data
curl -X POST "http://localhost:8000/api/v1/synthetic-data/generate" \
  -H "Authorization: Bearer ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "well_ids": ["Well_01", "Well_02"],
    "days": 180,
    "interval_seconds": 1,
    "include_anomalies": true,
    "include_failures": true,
    "failure_probability": 0.1,
    "export_format": "parquet",
    "seed": 42
  }'

# Get statistics
curl -X GET "http://localhost:8000/api/v1/synthetic-data/stats?file_path=data/generated/synthetic_data_20240101_120000.parquet" \
  -H "Authorization: Bearer TOKEN"
```

## Parameters

### generate_well_data()

- `well_id`: well identifier
- `start_date`: start date
- `days`: number of days (default: 180)
- `interval_seconds`: time interval in seconds (default: 1)
- `include_anomalies`: include anomalies (default: True)
- `include_failures`: include failure scenarios (default: True)
- `failure_probability`: failure probability (0-1, default: 0.1)

## Sensors

The generated data includes these sensors:

1. **motor_temperature**: motor temperature (65-120°C)
2. **intake_pressure**: intake pressure (450-600 psi)
3. **discharge_pressure**: discharge pressure (800-1200 psi)
4. **vibration**: vibration (0.5-5.0 g)
5. **current**: current (30-80 A)
6. **flow_rate**: flow rate (1500-2500 bpd)

## Data Patterns

### Daily Cycle
Daily variations (day/night) using a sine wave

### Weekly Trend
Weekly trend to simulate the production schedule

### Gradual Drift
Gradual drift to simulate equipment aging

### Failure Scenarios
- **Temperature/Vibration**: Gradual increase (overheating, bearing wear)
- **Pressure**: Gradual decrease (pump degradation)
- **Current**: Increasing fluctuations (motor issues)
- **Flow Rate**: Gradual decrease (production decline)

### Anomalies
- 1% of the data includes random anomalies
- Sudden spike or drop
- Magnitude: 2-5x standard deviation

## Export Formats

### Parquet (recommended)
- Compressed and fast
- Suitable for large datasets
- Preserves data types

### CSV
- Compatible with most tools
- Can be split into chunks for large files

### JSON
- Suitable for API/testing
- Limited to small samples (up to 10K records)

## Statistics and Reporting

```python
stats = generator.get_statistics(df)
# Returns:
# {
#   'total_records': 15552000,
#   'date_range': {...},
#   'wells': ['Well_01', ...],
#   'sensors': [...],
#   'sensor_statistics': {...},
#   'status_distribution': {...}
# }
```

## Examples

### Generating Data for Testing
```python
# 1 day of data with a 1-second interval
df = generator.generate_well_data(
    well_id="Test_Well",
    start_date=datetime.now() - timedelta(days=1),
    days=1,
    interval_seconds=1,
    include_failures=False,  # without failure for testing
)
```

### Generating Data with Failure
```python
# 30 days with a high probability of failure
df = generator.generate_well_data(
    well_id="Well_08",
    start_date=datetime.now() - timedelta(days=30),
    days=30,
    failure_probability=0.5,  # 50% probability of failure
)
```

### Generating Data for ML Training
```python
# 6 months of data for training
well_ids = [f"Well_{i:02d}" for i in range(1, 21)]  # 20 wells
df = generator.generate_multiple_wells(
    well_ids=well_ids,
    start_date=datetime.now() - timedelta(days=180),
    days=180,
    include_anomalies=True,
    include_failures=True,
)

# Export for ML
generator.export_to_parquet(df, "data/ml_training_data.parquet")
```

## Notes

1. **Memory**: Use Parquet for large datasets
2. **Reproducibility**: Use seed for reproducibility
3. **Performance**: Increase interval_seconds to reduce data volume
4. **Realism**: Set failure_probability as needed

