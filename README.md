# FastAPI + Docker Learning Project

A small learning project that shows how to:
- build a simple FastAPI app
- containerize it with Docker
- deploy it to Google Cloud Run using GitHub Actions

## Project Structure

- `app.py` - FastAPI app and routes
- `main.py` - local Python entry point (runs Uvicorn)
- `Dockerfile` - container image definition
- `requirements.txt` - Python dependencies
- `.github/workflows/deploy.yml` - CI/CD pipeline for build and deploy

## API Endpoint

- `GET /api/test`
- Response:

```json
{
  "message": "Hello World!"
}
```

## Run Locally (without Docker)

### 1. Create and activate virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Start the app

Option A (from `main.py`):

```bash
python main.py
```

Option B (direct Uvicorn):

```bash
uvicorn app:app --host 0.0.0.0 --port 8000 --reload
```

### 4. Test

Open:
- `http://localhost:8000/api/test`
- `http://localhost:8000/docs` (Swagger UI)

## Run with Docker

### 1. Build image

```bash
docker build -t fastapi-docker-learning .
```

### 2. Run container

```bash
docker run --rm -p 8080:8080 fastapi-docker-learning
```

### 3. Test

Open:
- `http://localhost:8080/api/test`
- `http://localhost:8080/docs`

## CI/CD Deployment Flow

On every push to `main`, GitHub Actions does:
1. Build Docker image
2. Push image to Google Artifact Registry
3. Deploy to Google Cloud Run

Workflow file:
- `.github/workflows/deploy.yml`

### Required GitHub Secrets

Set these repository secrets before deployment:
- `GCP_SA_KEY`
- `GCP_REGION`
- `GCP_PROJECT_ID`
- `GCP_ARTIFACT_REPO`
- `GCP_IMAGE_NAME`
- `GCP_SERVICE_NAME`

## Learning Goals

This project is useful for practicing:
- FastAPI basics
- Dockerizing Python apps
- Cloud Run deployment
- GitHub Actions CI/CD pipelines
