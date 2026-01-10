# Sky Guardian UTM - Enterprise-Grade Unmanned Traffic Management

**Sky Guardian** is an enterprise-grade SaaS platform for managing multi-operator Beyond Visual Line of Sight (BVLOS) drone operations. It provides real-time airspace deconfliction, automated jurisdictional compliance, and streamlined regulator approval workflows.

## Overview

As unmanned aerial systems (UAS) traffic increases globally, regulatory bodies require sophisticated coordination mechanisms. Sky Guardian solves this by providing:

- **Multi-Operator Coordination**: Enable multiple drone operators to safely share airspace in real-time
- **Automated Risk Scoring**: Evaluate flight risk across air, ground, and operational dimensions
- **4D Conflict Detection**: Detect and prevent spatial/temporal collisions before they happen
- **Compliance Automation**: Enforce jurisdiction-specific regulations automatically
- **Regulator Portal**: Enable civil aviation authorities to review and approve flights efficiently

## Key Features

### Operator Portal
- **Flight Submission Form**: Submit BVLOS flights with aircraft, pilot, and route information
- **Live Risk Scoring**: Real-time scoring across three dimensions:
  - **Air Risk**: Proximity to other flights, airspace, and obstacles
  - **Ground Risk**: Population density, critical infrastructure nearby
  - **Operational Risk**: Pilot experience, payload weight, weather conditions
- **4D Reservation System**: Request airspace corridors with lat/lon/altitude/time boundaries
- **Document Management**: Upload certifications, airworthiness documents, and compliance proofs

### Regulator Portal
- **Review Queue**: Risk-sorted list of pending flight requests
- **Approval Workflow**: Approve, reject, or request additional information
- **Live Operations Dashboard**: Monitor ongoing flights and detect conflicts in real-time
- **Audit Trail**: Complete tracking of all decisions and actions for compliance

### Core Engine
- **Configurable Risk Rules**: Customize risk thresholds by jurisdiction
- **Spatial Conflict Detection**: Sub-100ms latency using PostGIS spatial indexing
- **Deconfliction Suggestions**: AI-driven recommendations for route/altitude/time adjustments
- **Multi-Jurisdiction Support**: Compliance templates for EASA, DGCA, CASA, and others

### Integrations
- **Remote ID Feeds**: ADS-B and cellular remote ID tracking
- **Weather APIs**: Wind, precipitation, and visibility data
- **SSO/OIDC**: Enterprise authentication
- **Telemetry Streaming**: Real-time flight tracking via Kafka

## Architecture

### Technology Stack

**Frontend**
- React 18 + TypeScript
- Leaflet/Cesium for 3D airspace visualization
- Mapbox GL for basemaps
- Material-UI for components

**Backend**
- Node.js + Express (microservices)
- PostgreSQL + PostGIS for spatial data
- Redis for caching and conflict detection state
- Kafka for event streaming
- Kubernetes for orchestration

**Deployment**
- Docker containers
- Kubernetes (EKS/GKE/AKS)
- Terraform for infrastructure-as-code
- GitHub Actions for CI/CD

### Microservices

1. **Flight Management Service**: CRUD operations for flights, pilots, operators
2. **Airspace Orchestration Service**: Manages 4D reservation system using spatial queries
3. **Risk Engine Service**: Evaluates flight risk using configurable rules
4. **Conflict Detection Service**: Real-time deconfliction using spatial indices
5. **Compliance Engine**: Jurisdiction-specific rule templates and validation
6. **Notification Service**: WebSocket, push, and email alerts
7. **Telemetry Pipeline**: Ingests remote ID feeds and normalizes data

## Quick Start (Local Development)

### Prerequisites
- Node 18+
- npm or yarn
- Docker (optional, for production-like environment)

### Setup

```bash
# Backend
cd backend
npm install
npm run dev  # Starts on http://localhost:4000

# Frontend (new terminal)
cd frontend
npm install
npm start    # Starts on http://localhost:3000
```

The demo includes:
- **Operator view**: Submit flights with live risk scoring
- **Interactive map**: Visualize flight corridors
- **Regulator view**: Approve/reject pending flights
- **In-memory data store**: Pre-populated with sample flights

### Browser Preview (No Backend)

For a standalone HTML preview without running Node.js:

1. Open `preview.html` in your browser
2. Interact with the form to see live risk scoring
3. Submit flights to the map
4. Toggle to Regulator view to approve/reject

## Project Structure

```
sky-guardian-utm/
├── backend/
│   ├── server.js           # Express app and routes
│   ├── package.json        # Dependencies
│   └── README.md           # Backend docs
├── frontend/
│   ├── src/
│   │   ├── App.tsx         # Main component
│   │   ├── FlightForm.tsx  # Operator submission form
│   │   ├── FlightList.tsx  # Flight queue
│   │   ├── MapView.tsx     # Leaflet map
│   │   └── styles.css      # Styling
│   ├── package.json        # Dependencies
│   └── README.md           # Frontend docs
├── preview.html            # Standalone demo (no backend)
└── README.md               # This file
```

## MVP Scope (6-Month Roadmap)

### Phase 1: Core Platform (Weeks 1-6)
- Basic flight submission and risk scoring
- Interactive 4D reservation system
- Regulator approval workflow
- Single-jurisdiction compliance

### Phase 2: Integrations (Weeks 7-9)
- Remote ID feed ingestion
- Weather API integration
- SSO/OIDC authentication

### Phase 3: Production Hardening (Weeks 10-24)
- Multi-tenant isolation
- Persistent database (PostgreSQL + PostGIS)
- Advanced deconfliction (AI/ML)
- SOC2 compliance controls
- Load testing (100+ concurrent flights)
- Deployment automation (Terraform + Helm)

## API Endpoints

### Flight Management
- `POST /api/flights` - Submit a new flight
- `GET /api/flights` - List all flights
- `POST /api/flights/:id/action` - Approve/reject flight

### Risk Scoring
- `POST /api/score` - Calculate risk for parameters

### Data Models

**Flight**
```json
{
  "id": "flight-uuid",
  "operator": "Operator Name",
  "origin": { "lat": 13.0827, "lon": 80.2707 },
  "dest": { "lat": 13.0358, "lon": 80.2440 },
  "altitude_min": 300,
  "altitude_max": 400,
  "start": "2024-01-15T10:00:00Z",
  "end": "2024-01-15T11:00:00Z",
  "aircraft": "DJI Air 3S",
  "pilot_hours": 120,
  "payload_kg": 2.5,
  "risk": { "air": 35, "ground": 42, "operational": 48, "overall": 41.67 },
  "status": "APPROVED|PENDING|REJECTED|NEEDS_INFO",
  "createdAt": "2024-01-15T09:00:00Z"
}
```

## Compliance & Security

- **Multi-tenancy**: Complete data isolation per operator/jurisdiction
- **RBAC**: Role-based access for Operator, Regulator, Pilot roles
- **Audit Logging**: All approvals, rejections, and modifications tracked
- **Encryption**: TLS in transit, AES-256 at rest (KMS)
- **SOC2 Ready**: Controls mapped to compliance frameworks
- **Performance**: Sub-100ms conflict detection latency

## Next Steps

### For Development
1. Clone this repository
2. Follow Quick Start above
3. Check `backend/README.md` and `frontend/README.md` for detailed docs
4. Create pull requests for new features

### For Deployment
1. See `terraform/` directory for infrastructure-as-code
2. Customize `helm/` charts for your Kubernetes cluster
3. Set up CI/CD pipelines (GitHub Actions provided)

### Roadmap (Post-MVP)
- [ ] Multi-jurisdiction compliance engine (EASA, CASA, DGCA templates)
- [ ] Advanced AI-driven deconfliction
- [ ] ATM/ATC integration and LAANC compatibility
- [ ] Mobile pilot app with real-time alerts
- [ ] Billing & usage metering
- [ ] Horizontal scaling to 1000+ concurrent flights

## Contributing

We welcome contributions! Please:
1. Fork the repository
2. Create a feature branch
3. Submit pull requests with test coverage
4. Follow the coding standards in CONTRIBUTING.md

## License

MIT License - See LICENSE file for details

## Support

For issues, questions, or feature requests, please open a GitHub issue or contact support@skyguardian.io

## Team

Built by a team of aviation, drone, and software experts focused on advancing safe autonomous flight.
