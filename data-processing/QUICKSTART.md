# Quick Start Guide - Synthetic Data Generator

Quick guide to using the Synthetic Data Generator

## Installing Dependencies

```bash
pip install pandas numpy
```

## Quick Usage

### 1. Simple Data Generation

```python
from synthetic_data_generator import SyntheticALSDataGenerator
from datetime import datetime, timedelta

# Create the generator
generator = SyntheticALSDataGenerator(seed=42)

# Generate data for one well
df = generator.generate_well_data(
    well_id="Well_01",
    start_date=datetime.now() - timedelta(days=7),
    days=7,
    interval_seconds=60,  # every 1 minute
)

print(f"Generated: {len(df):,} records")
```

### 2. Generating Data for Multiple Wells

```python
well_ids = ["Well_01", "Well_02", "Well_03"]
df = generator.generate_multiple_wells(
    well_ids=well_ids,
    start_date=datetime.now() - timedelta(days=30),
    days=30,
)
```

### 3. Export to a File

```python
# Export to Parquet (recommended)
generator.export_to_parquet(df, "data/my_data.parquet")

# Export to CSV
generator.export_to_csv(df, "data/my_data.csv")

# Export to JSON (for small samples)
generator.export_to_json(df.head(1000), "data/sample.json")
```

### 4. Viewing Statistics

```python
stats = generator.get_statistics(df)
print(stats)
```

## Practical Examples

### Example 1: Data for Testing (1 day)

```python
generator = SyntheticALSDataGenerator(seed=42)
df = generator.generate_well_data(
    well_id="Test_Well",
    start_date=datetime.now() - timedelta(days=1),
    days=1,
    interval_seconds=60,  # every minute
    include_failures=False,  # without failure
)
generator.export_to_parquet(df, "data/test_data.parquet")
```

### Example 2: Data with a Failure Scenario

```python
generator = SyntheticALSDataGenerator(seed=42)
df = generator.generate_well_data(
    well_id="Failing_Well",
    start_date=datetime.now() - timedelta(days=30),
    days=30,
    failure_probability=1.0,  # 100% probability of failure
)
generator.export_to_parquet(df, "data/failure_data.parquet")
```

### Example 3: Data for ML Training (6 months)

```python
generator = SyntheticALSDataGenerator(seed=42)
well_ids = [f"Well_{i:02d}" for i in range(1, 21)]  # 20 wells

df = generator.generate_multiple_wells(
    well_ids=well_ids,
    start_date=datetime.now() - timedelta(days=180),
    days=180,
    interval_seconds=3600,  # every hour (to reduce volume)
    include_anomalies=True,
    include_failures=True,
)

generator.export_to_parquet(df, "data/ml_training_data.parquet")
print(f"Generated: {len(df):,} records")
```

## Testing the Generator

```bash
cd data-processing
python test_generator.py
```

This script tests all the generator's capabilities.

## Important Notes

1. **Memory**: For large datasets, increase `interval_seconds`
2. **Reproducibility**: Use `seed` for reproducibility
3. **Format**: Use Parquet for large datasets
4. **Performance**: 
   - 1 second interval: ~15.5M records/day
   - 60 second interval: ~1,440 records/day
   - 3600 second interval: ~24 records/day

## Common Problems

### Problem: Memory Error
**Solution**: Increase `interval_seconds` or decrease `days`

### Problem: File too large
**Solution**: Use Parquet with compression or increase the interval

### Problem: Data are identical
**Solution**: Change or remove `seed`

