# Project Setup Guide

## Prerequisites

- Node.js 20+
- Python 3.11+
- Docker & Docker Compose
- PostgreSQL (with TimescaleDB)

## Installation and Setup

### 1. Install Dependencies

```bash
# Install the main dependencies
npm run install:all

# Or separately:
npm install
cd frontend && npm install
cd ../backend && pip install -r requirements.txt
cd ../data-processing && pip install -r requirements.txt
cd ../ml-services && pip install -r requirements.txt
```

### 2. Setup with Docker

```bash
# Build and start all services
docker-compose up -d

# Or just build
docker-compose build
```

### 3. Manual Setup

#### Backend API
```bash
cd backend
cp .env.example .env
# Edit .env with appropriate settings
uvicorn app.main:app --reload
```

#### Frontend
```bash
cd frontend
npm run dev
```

### 4. Database Settings

```bash
# Run migrations
cd backend
alembic upgrade head

# Or with Docker
docker-compose exec backend alembic upgrade head
```

## Project Structure

```
i-lift---AI-Powered-Artificial-Lift-Management-Dashboard/
├── frontend/                 # Next.js Frontend
│   ├── src/
│   │   ├── app/            # Next.js App Router
│   │   ├── components/     # React Components
│   │   ├── lib/           # Utilities
│   │   └── types/         # TypeScript Types
│   └── package.json
├── backend/                 # FastAPI Backend
│   ├── app/
│   │   ├── api/           # API Routes
│   │   ├── core/          # Core Config
│   │   ├── models/        # Database Models
│   │   ├── schemas/       # Pydantic Schemas
│   │   └── services/      # Business Logic
│   └── requirements.txt
├── data-processing/         # Data Processing Services
│   └── synthetic_data_generator.py
├── ml-services/            # ML/AI Services
│   ├── anomaly_detection.py
│   └── predictive_maintenance.py
├── monitoring/             # Monitoring Configs
│   └── prometheus.yml
├── docker-compose.yml
└── package.json
```

## Useful Commands

```bash
# Run development
npm run dev

# Build the project
npm run build

# Test
npm run test

# Linting
npm run lint

# Docker
npm run docker:build
npm run docker:up
npm run docker:down
```

## Ports

- Frontend: http://localhost:3000
- Backend API: http://localhost:8000
- API Docs: http://localhost:8000/docs
- Grafana: http://localhost:3001
- Prometheus: http://localhost:9090
- MLflow: http://localhost:5000

## Important Notes

1. Set up the `.env` file in the backend
2. The database must have the TimescaleDB extension
3. To generate synthetic data use `data-processing/synthetic_data_generator.py`

