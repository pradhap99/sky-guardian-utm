# Sky Guardian UTM - Full-Stack Setup Guide

## Overview

This guide walks you through setting up and running Sky Guardian UTM as a complete full-stack application with:
- **Node.js + Express backend** providing REST APIs
- **React + TypeScript frontend** with real-time risk scoring and conflict detection
- **Live map visualization** with Leaflet
- **Multi-role support** for Operators and Regulators

## Quick Start (Local Development)

### Prerequisites

- **Node.js 18+** and **npm**
- **Git**
- Two terminal windows (one for backend, one for frontend)

### Step 1: Clone or Setup Repository

```bash
git clone https://github.com/pradhap99/sky-guardian-utm.git
cd sky-guardian-utm
```

### Step 2: Start the Backend

The backend provides:
- Risk scoring API (`POST /api/score`)
- Flight submission & management (`POST /api/flights`, `GET /api/flights`)
- Flight approval/rejection (`POST /api/flights/:id/action`)
- Real-time conflict detection with spatial reasoning
- In-memory flight persistence

**Terminal 1:**

```bash
cd backend
npm install
npm run dev
```

**Expected output:**
```
Backend running on http://localhost:4000
```

### Step 3: Start the Frontend

The frontend provides:
- Interactive flight submission form
- Live risk scoring display
- Interactive map with flight corridors
- Operator and Regulator dashboards
- Real-time state synchronization with backend

**Terminal 2:**

```bash
cd frontend
npm install
npm start
```

**Expected output:**
```
Compiled successfully!
You can now view sky-guardian-frontend in the browser.
Local: http://localhost:3000
```

### Step 4: Open the Application

Open **http://localhost:3000** in your web browser.

## Using the Application

### Operator View (Default)

1. **Submit Flights**
   - Fill in origin/destination coordinates (defaults: Chennai area)
   - Set altitude range and time window
   - Enter aircraft type and pilot experience
   - Watch **live risk scores** update as you change inputs
   - Click "Submit Flight" to create a flight request

2. **View Map**
   - Pending flights appear in **orange** with polyline routes
   - Approved flights appear in **green**
   - Rejected flights appear in **red**
   - Markers show origin and destination points

3. **Conflict Detection**
   - Backend automatically detects conflicts when submitting flights
   - Conflicts shown as "Conflicts: N" on flight cards
   - Conflicts detected based on:
     - Spatial proximity (< 1km)
     - Time overlap
     - Altitude overlap

### Regulator View

1. Click the **Regulator** radio button in the top header

2. **Review Pending Flights**
   - View all pending flight requests
   - See operator details and risk scores
   - Review route, altitude, and time window

3. **Take Actions**
   - **Approve**: Accept flight request (status → APPROVED)
   - **Reject**: Deny flight request (status → REJECTED)
   - **Request Info**: Ask operator for more information (status → NEEDS_INFO)

4. **Audit Trail**
   - All actions are logged with timestamp
   - Regulator name recorded for each decision

## Architecture

### Backend (`/backend`)

**Files:**
- `server.js` - Express server with all APIs
- `package.json` - Node dependencies (express, cors, body-parser, uuid)

**APIs:**
```
POST   /api/score              # Calculate risk for flight parameters
GET    /api/flights            # List all flights (paginated)
POST   /api/flights            # Submit new flight
POST   /api/flights/:id/action # Approve/Reject/Request Info
```

**Risk Scoring Logic:**
- **Air Risk**: Proximity to other flights (simulated ADS-B data)
- **Ground Risk**: Distance to populated areas
- **Operational Risk**: Pilot experience, payload weight
- **Overall**: Average of three dimensions
- **Approval Tier**: Determined by risk thresholds

**Conflict Detection:**
- Spatial: Haversine distance calculation
- Temporal: Time window overlap check
- Altitude: Min/max altitude range overlap

### Frontend (`/frontend`)

**Key Files:**
- `package.json` - React dependencies
- `.env` - Backend API URL configuration
- `src/App.tsx` - Main app component with role toggle
- `src/FlightForm.tsx` - Flight submission form with live risk scoring
- `src/FlightList.tsx` - Flight cards with operator/regulator actions
- `src/MapView.tsx` - Leaflet map with flight corridors
- `src/styles.css` - Application styling

**Technologies:**
- React 18 with TypeScript
- Axios for HTTP requests
- Leaflet for mapping
- date-fns for date/time handling

## Environment Variables

### Frontend (`.env` in `/frontend`)

```env
REACT_APP_API_BASE=http://localhost:4000
```

For production, change to your deployed backend URL.

## Testing the Application

### Test Scenario 1: Basic Flight Submission

1. Enter coordinates (defaults are Chennai, India)
2. Watch risk scores update
3. Click Submit
4. Switch to Regulator view
5. Approve the flight
6. Watch status change on map (orange → green)

### Test Scenario 2: Conflict Detection

1. Submit first flight with origin/dest
2. Submit second flight with origin within 0.5km of first
3. Second flight shows "Conflicts: 1"
4. Regulator can see conflict details

### Test Scenario 3: Risk-Based Approval

1. Submit flight with low-risk parameters (experienced pilot, light payload, populated area)
2. Risk tier shows "standard" → auto-approved
3. Submit flight with high-risk parameters
4. Risk tier shows "high_risk_review" → marked pending

## Data Persistence

**Current:** In-memory store (flights lost on server restart)

**For Production:** 
- Add PostgreSQL + PostGIS for spatial queries
- Implement Redis for caching
- Add Kafka for event streaming
- Set up S3 for document storage

See main README.md for detailed architecture.

## Troubleshooting

### Backend not starting

```bash
# Check if port 4000 is in use
lsof -i :4000

# Kill process if needed (macOS/Linux)
kill -9 <PID>

# Try a different port
PORT=5000 npm run dev
```

### Frontend can't connect to backend

1. Check `.env` has correct `REACT_APP_API_BASE`
2. Ensure backend is running on the configured port
3. Check browser console for CORS errors
4. Try backend URL directly: `curl http://localhost:4000/api/flights`

### Map not displaying

1. Open browser DevTools → Console
2. Check for Leaflet CSS loading
3. Ensure Leaflet is in dependencies: `npm list leaflet`

## Deployment

### Backend Deployment (Render/Railway/Heroku)

```bash
# Push to hosting service
# Set environment variables (PORT, NODE_ENV)
# For production: add PostgreSQL and update connection string
```

### Frontend Deployment (Vercel/Netlify/GitHub Pages)

```bash
npm run build
# Deploy `build/` folder
```

## Next Steps

1. **Add Database Persistence**: PostgreSQL + PostGIS
2. **Implement Authentication**: OAuth2/OIDC SSO
3. **Add Telemetry**: Real remote ID feed integration
4. **3D Visualization**: Cesium instead of Leaflet
5. **Mobile App**: React Native pilot app
6. **Advanced Deconfliction**: AI-driven route optimization

## Support

For issues or questions:
1. Check the main [README.md](README.md)
2. Review backend API documentation in `backend/README.md`
3. Open a GitHub issue with details

---

**Last Updated:** 2024
**Version:** 1.0.0 (MVP)
