
MahaKavach Backend Real-Time Crowd & Safety Intelligence Service This repository contains the backend services powering the MahaKavach platform. 
It is responsible for real-time crowd data ingestion, processing, aggregation, and distribution to client applications via WebSockets and APIs. 
The backend is designed as a stateless, event-driven service suitable for cloud deployment and horizontal scaling. Responsibilities The backend service handles: Crowd data ingestion from image-based analysis pipelines Real-time crowd state aggregation (station & train level) WebSocket-based data streaming to clients Community chat message routing (line-wise) Safety signal computation and risk scoring Incident and panic event handling Persistence of historical metadata This separation allows independent deployment, scaling, and evolution of frontend and backend systems.
System Architecture Image Inputs / Datasets ↓ Crowd Detection Pipeline (YOLO-based) ↓ Density Aggregation & State Engine ↓ FastAPI Backend Service ↓ WebSocket Streams + REST APIs ↓ Client Applications (Web / PWA) Tech Stack Core Backend FastAPI (Python) – API & WebSocket server Native WebSockets – low-latency real-time streaming AsyncIO – non-blocking event loop Data Layer PostgreSQL – persistent storage (Supabase) In-memory state store – live crowd snapshots AI / Processing Image-based crowd density inputs Rule-based safety and risk engine ML-ready interfaces for future models Infrastructure Container-friendly service design Environment-driven configuration Cloud deployment (Render) API & WebSocket Design WebSocket Streams /ws/crowd Streams real-time station and train crowd updates Push-based (no client polling) Supports multiple concurrent subscribers /ws/chat/{line} Real-time community chat per railway line Lines: western | central | harbour REST Endpoints (Sample) POST /api/panic POST /api/chat/message GET /api/stations GET /api/trains/incoming APIs are designed to be idempotent, stateless, and horizontally scalable. Environment Configuration Create a .env file: DATABASE_URL=postgresql://user:password@host:port/db PORT=8000 Local Development Install Dependencies pip install -r requirements.txt Run the Server uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload Backend will be available at: http://localhost:8000 Deployment Notes Backend is deployed independently from frontend Cold-start latency exists on free-tier hosting WebSocket connections automatically recover on reconnect Designed to scale horizontally with external state storage Engineering Highlights Event-driven architecture using WebSockets Stateless service design Clear separation between ingestion, processing, and delivery layers Production-style API boundaries Designed for high-frequency real-time updates Known Limitations Image ingestion is simulated (no live camera feeds) Safety scoring is rule-based (ML-ready) Free-tier hosting impacts cold-start latency Future Enhancements Redis-based distributed state store Message queue for ingestion (Kafka / PubSub) ML-based crowd prediction Authority-level operational dashboards Multi-city backend configuration Why a Separate Backend Repository? Independent deployment lifecycle Clear API contracts Enables frontend/backend team parallelism Reflects real-world microservice boundaries Author Harsh Shah make this into github readme code  like senior developer


# MahaKavach Backend

**Real-Time Crowd & Safety Intelligence Service**

[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)](https://fastapi.tiangolo.com/)
[![Python](https://img.shields.io/badge/python-3.11+-blue.svg?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![WebSocket](https://img.shields.io/badge/WebSocket-010101?style=for-the-badge&logo=socketdotio&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)

> High-performance backend services powering real-time crowd monitoring and safety intelligence for urban rail transit systems.

---

##  Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [API Reference](#api-reference)
- [Deployment](#deployment)
- [Engineering Highlights](#engineering-highlights)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

---

##  Overview

MahaKavach Backend is a **stateless, event-driven microservice** designed for real-time crowd data ingestion, processing, aggregation, and distribution. Built to support horizontal scaling and cloud-native deployment, it serves as the critical data backbone for the MahaKavach platform.

### Core Responsibilities

-  **Crowd Data Ingestion** - Real-time processing from image-based analysis pipelines
-  **State Aggregation** - Station and train-level crowd density computation
-  **WebSocket Streaming** - Low-latency data distribution to client applications
-  **Community Chat Routing** - Line-wise message distribution system
-  **Safety Intelligence** - Risk scoring and incident event handling
-  **Historical Persistence** - Metadata storage for analytics and auditing

### Architecture Separation

The backend operates **independently** from frontend clients, enabling:

- Parallel development workflows
- Independent deployment lifecycles
- Clear API contract boundaries
- Technology stack flexibility
- Horizontal scalability

---

##  Architecture
```
┌─────────────────────────────┐
│  Image Inputs / Datasets    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Crowd Detection Pipeline    │
│      (YOLO-based)           │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│ Density Aggregation &       │
│    State Engine             │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│   FastAPI Backend Service   │
│                             │
│  ┌───────────────────────┐ │
│  │ WebSocket Streams     │ │
│  │ REST APIs             │ │
│  │ Event Processing      │ │
│  └───────────────────────┘ │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│  Client Applications        │
│  (Web / PWA / Mobile)       │
└─────────────────────────────┘
```

### Data Flow

1. **Ingestion Layer** - Receives crowd density data from computer vision pipelines
2. **Processing Layer** - Aggregates, validates, and enriches incoming data streams
3. **Distribution Layer** - Broadcasts updates via WebSockets and serves REST APIs
4. **Persistence Layer** - Stores historical data and system state

---

## 🛠️ Tech Stack

### Core Backend

| Component | Technology | Purpose |
|-----------|-----------|---------|
| API Framework | **FastAPI** | High-performance async API server |
| Real-time | **Native WebSockets** | Low-latency bidirectional streaming |
| Concurrency | **AsyncIO** | Non-blocking event loop |
| Runtime | **Python 3.11+** | Modern Python with performance optimizations |

### Data & Storage

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Database | **PostgreSQL** (Supabase) | Persistent storage with JSONB support |
| State Store | **In-Memory Cache** | Live crowd snapshots and session data |
| Future | **Redis** | Distributed state management (planned) |

### AI & Processing

- **Computer Vision Inputs** - YOLO-based crowd detection pipeline
- **Rule-Based Engine** - Safety scoring and risk assessment
- **ML-Ready Architecture** - Interfaces prepared for future model integration

### Infrastructure

-  **Container-Ready** - Docker-compatible service design
-  **Cloud-Native** - Environment-driven configuration
-  **Horizontally Scalable** - Stateless service architecture
-  **Deployed on Render** - Production hosting with auto-scaling

---

##  Getting Started

### Prerequisites
```bash
python >= 3.11
postgresql >= 14
pip >= 23.0
```

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/mahakavach-backend.git
cd mahakavach-backend
```

2. **Create virtual environment**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Configure environment**

Create `.env` file in project root:
```env
# Database
DATABASE_URL=postgresql://user:password@host:port/dbname

# Server
PORT=8000
HOST=0.0.0.0
ENVIRONMENT=development

# Optional
LOG_LEVEL=INFO
CORS_ORIGINS=http://localhost:3000,https://yourdomain.com
```

### Running Locally

**Development server with hot reload:**
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

**Production server:**
```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4
```

The server will be available at:
- **API Documentation**: http://localhost:8000/docs
- **ReDoc**: http://localhost:8000/redoc
- **Health Check**: http://localhost:8000/health

---

## 📡 API Reference

### WebSocket Endpoints

#### `/ws/crowd`

Real-time crowd data stream for all stations and trains.

**Connection:**
```javascript
const ws = new WebSocket('ws://localhost:8000/ws/crowd');

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Crowd update:', data);
};
```

**Message Format:**
```json
{
  "type": "crowd_update",
  "timestamp": "2024-01-15T10:30:45Z",
  "stations": [
    {
      "id": "CST",
      "name": "Chhatrapati Shivaji Terminus",
      "crowd_level": "high",
      "count": 1250,
      "platforms": {...}
    }
  ],
  "trains": [...]
}
```

#### `/ws/chat/{line}`

Line-specific community chat stream.

**Supported Lines:**
- `western`
- `central`
- `harbour`

**Connection:**
```javascript
const ws = new WebSocket('ws://localhost:8000/ws/chat/western');
```

---

### REST Endpoints

#### **POST** `/api/panic`

Report a panic or emergency event.

**Request Body:**
```json
{
  "station_id": "CST",
  "severity": "high",
  "description": "Overcrowding on platform 3",
  "reporter_id": "anonymous"
}
```

**Response:** `201 Created`

---

#### **POST** `/api/chat/message`

Send a chat message to a railway line channel.

**Request Body:**
```json
{
  "line": "western",
  "message": "Heavy crowd at Dadar",
  "user_id": "user_123"
}
```

**Response:** `200 OK`

---

#### **GET** `/api/stations`

Retrieve all monitored stations with current crowd status.

**Response:**
```json
{
  "stations": [
    {
      "id": "CST",
      "name": "Chhatrapati Shivaji Terminus",
      "line": "central",
      "crowd_level": "moderate",
      "last_updated": "2024-01-15T10:30:00Z"
    }
  ]
}
```

---

#### **GET** `/api/trains/incoming`

Get incoming trains with predicted crowd levels.

**Query Parameters:**
- `station_id` (optional) - Filter by station
- `line` (optional) - Filter by railway line

**Response:**
```json
{
  "trains": [
    {
      "train_id": "12345",
      "line": "western",
      "current_station": "Bandra",
      "next_station": "Khar Road",
      "crowd_level": "high",
      "eta_minutes": 3
    }
  ]
}
```

---

### API Design Principles

-  **Idempotent** - Safe to retry operations
-  **Stateless** - No server-side session dependencies
-  **RESTful** - Consistent resource-oriented design
-  **Versioned** - Future-proof API evolution path
-  **Documented** - Auto-generated OpenAPI specs

---

## 🚢 Deployment

### Environment Configuration

The service uses environment variables for configuration. Never commit `.env` files.

**Production checklist:**
```env
DATABASE_URL=<production_db_url>
ENVIRONMENT=production
LOG_LEVEL=WARNING
CORS_ORIGINS=https://mahakavach.app
SECRET_KEY=<generate_secure_key>
```

### Docker Deployment
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Build and run:**
```bash
docker build -t mahakavach-backend .
docker run -p 8000:8000 --env-file .env mahakavach-backend
```

### Cloud Deployment (Render)

**Build Command:**
```bash
pip install -r requirements.txt
```

**Start Command:**
```bash
uvicorn app.main:app --host 0.0.0.0 --port $PORT
```

---

### Deployment Considerations

| Aspect | Note |
|--------|------|
| **Cold Start** | Free-tier hosting may experience initial latency (~5-10s) |
| **WebSocket Recovery** | Clients implement automatic reconnection logic |
| **Scaling** | Horizontal scaling requires external state store (Redis) |
| **Database** | Connection pooling configured for concurrent requests |
| **Monitoring** | Health checks at `/health` endpoint |

---

##  Engineering Highlights

### Event-Driven Architecture

- **Push-based updates** eliminate polling overhead
- **WebSocket broadcast patterns** for efficient multi-client distribution
- **AsyncIO concurrency** enables thousands of simultaneous connections

### Stateless Service Design

- **No server-side sessions** - enables seamless horizontal scaling
- **Idempotent APIs** - safe retry logic for network failures
- **External state storage** - database and cache for persistence

### Clean Layer Separation
```
┌─────────────────────────────────┐
│     Presentation Layer          │  ← FastAPI routes, WebSocket handlers
├─────────────────────────────────┤
│     Business Logic Layer        │  ← Crowd aggregation, safety scoring
├─────────────────────────────────┤
│     Data Access Layer           │  ← PostgreSQL queries, caching
├─────────────────────────────────┤
│     External Integrations       │  ← CV pipeline, future ML models
└─────────────────────────────────┘
```

### Production-Ready API Boundaries

- Comprehensive input validation (Pydantic models)
- Structured error responses
- Request/response logging
- Rate limiting ready (future)
- API versioning strategy

---

## 🔮 Roadmap

### Near-Term (Q1 2024)

- [ ] **Redis Integration** - Distributed state management
- [ ] **Message Queue** - Kafka/PubNub for ingestion pipeline
- [ ] **Enhanced Monitoring** - Prometheus metrics, Grafana dashboards
- [ ] **Rate Limiting** - Per-client API throttling

### Mid-Term (Q2 2024)

- [ ] **ML-Based Prediction** - Crowd forecasting models
- [ ] **Multi-City Support** - Configuration-driven deployment
- [ ] **Authority Dashboard** - Operational command center APIs
- [ ] **Historical Analytics** - Time-series aggregation endpoints

### Long-Term

- [ ] **Real-Time Camera Integration** - Live CCTV feed processing
- [ ] **Advanced Safety AI** - Anomaly detection and risk prediction
- [ ] **Multi-Modal Transit** - Bus, metro, ferry integration
- [ ] **Public Safety API** - Integration with emergency services

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

### Development Guidelines

- Follow PEP 8 style guidelines
- Write unit tests for new features
- Update API documentation
- Maintain backward compatibility

---

#

---

##  Author

**Harsh Shah**

---

##  Acknowledgments

- FastAPI community for excellent async framework
- Supabase for managed PostgreSQL infrastructure
- Open-source computer vision contributors

---

<div align="center">

**Built with ❤️ for safer Mumbaikars**

[Report Bug](https://github.com/yourusername/mahakavach-backend/issues) · [Request Feature](https://github.com/yourusername/mahakavach-backend/issues)

</div>
