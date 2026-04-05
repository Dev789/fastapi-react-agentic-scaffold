---
description: Bootstrap the local development environment from a fresh clone
---

# Setup Local Development Environment

## Prerequisites
- **Git** installed
- **Docker** and **Docker Compose** installed and running
- **Node.js** 20.x LTS and a compatible `npm`
- **Python** 3.11 or 3.12
- A code editor (VS Code recommended)

## Steps

### 1. Clone the Repository
```bash
git clone <repository-url>
cd <project-name>
```

### 2. Setup Environment Variables
```bash
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
```
Edit both `.env` files with your local values (database credentials, API URL, etc.).

### 3. Start Infrastructure with Docker Compose
```bash
docker-compose up -d db redis
```
This starts PostgreSQL and Redis containers. Wait ~10 seconds for them to become healthy.

### 4. Setup Backend
```bash
cd backend
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
# source .venv/bin/activate
pip install -r requirements.txt
```

### 5. Apply Database Migrations
```bash
cd backend
alembic upgrade head
```

### 6. Seed the Database (Optional)
```bash
cd backend
python -m app.db.init_db
```

### 7. Start the Backend Dev Server
```bash
cd backend
uvicorn app.main:app --reload --port 8000
```
Verify: open `http://localhost:8000/docs` for the Swagger UI.

### 8. Setup Frontend
```bash
cd frontend
npm install
```

### 9. Start the Frontend Dev Server
```bash
cd frontend
npm run dev
```
Verify: open `http://localhost:5173` in your browser.

## Verification Checklist
- [ ] PostgreSQL container is running (`docker ps`)
- [ ] Backend responds at `http://localhost:8000/health`
- [ ] Swagger UI loads at `http://localhost:8000/docs`
- [ ] Frontend loads at `http://localhost:5173`
- [ ] Frontend can call the backend API without CORS errors
