# Sky Guardian Backend

## Overview

Node.js + Express backend for the Sky Guardian UTM platform. Provides REST APIs for flight management, risk scoring, conflict detection, and approval workflows.

## Setup

```bash
npm install
npm run dev   # Starts with nodemon on port 4000
# or
npm start     # Production mode
```

## API Endpoints

### Flight Scoring
- **POST /api/score** - Calculate risk scores for flight parameters
  - Input: origin, dest, altitude_min, altitude_max, pilot_hours, aircraft, payload_kg
  - Output: air, ground, operational, overall risk scores + approval tier and confidence

### Flight Management
- **POST /api/flights** - Submit a new flight
- **GET /api/flights** - List all flights (sorted by creation date descending)
- **POST /api/flights/:id/action** - Approve, reject, or request info on a flight

## Key Features

### Risk Scoring Engine
Evaluates three dimensions of risk:
- **Air Risk**: Proximity to seeded flights, altitude conflicts
- **Ground Risk**: Distance to city center, population density
- **Operational Risk**: Pilot experience, payload weight

Returns approval tier:
- `standard`: Low risk flights (fast track)
- `detailed_review`: Medium risk flights
- `high_risk_review`: High-risk flights requiring regulator decision

### Conflict Detection
- Checks time overlap, distance, and altitude conflicts
- Uses haversine formula for geographic distance
- Flags conflicting flights in response

### In-Memory Store
Demo-ready with pre-seeded sample flights from Chennai airspace.
Data persists during session but resets on server restart.

## Architecture (Production)

For production deployment, consider:
- **Database**: PostgreSQL + PostGIS for spatial queries
- **Caching**: Redis for flight state and conflict indices
- **Message Queue**: Kafka for telemetry streaming
- **Monitoring**: Prometheus + Grafana, ELK for logs
- **Deployment**: Kubernetes with Helm charts

## Next Steps

See main [README.md](../README.md) for full architecture and roadmap.
