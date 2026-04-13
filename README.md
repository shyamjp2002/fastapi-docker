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

### Deployment Sequence (Detailed)

The workflow has 2 jobs:

1. `build` job (`Build Container`)
- Checks out the repository
- Authenticates to Google Cloud using `GCP_SA_KEY`
- Sets up `gcloud`
- Configures Docker auth for Artifact Registry:
  - `${GCP_REGION}-docker.pkg.dev`
- Builds and pushes the image with Buildx
- Uses Git commit SHA as image tag:
  - `${GCP_REGION}-docker.pkg.dev/${GCP_PROJECT_ID}/${GCP_ARTIFACT_REPO}/${GCP_IMAGE_NAME}:${github.sha}`

2. `deploy` job (`Deploy Application`)
- Runs only after `build` succeeds (`needs: build`)
- Authenticates to Google Cloud again
- Deploys the exact same SHA-tagged image to Cloud Run:
  - `gcloud run deploy ${GCP_SERVICE_NAME} ... --region ${GCP_REGION} --platform managed --allow-unauthenticated`

This makes deployments traceable because each Cloud Run revision maps to a specific commit SHA.

### Required GitHub Secrets

Set these repository secrets before deployment:
- `GCP_SA_KEY`
- `GCP_REGION`
- `GCP_PROJECT_ID`
- `GCP_ARTIFACT_REPO`
- `GCP_IMAGE_NAME`
- `GCP_SERVICE_NAME`

Recommended values:
- `GCP_REGION`: e.g. `us-central1`
- `GCP_ARTIFACT_REPO`: existing Artifact Registry Docker repository name
- `GCP_IMAGE_NAME`: image name for this app (e.g. `fastapi-learning`)
- `GCP_SERVICE_NAME`: Cloud Run service name (e.g. `fastapi-learning-svc`)

## Learning Goals

This project is useful for practicing:
- FastAPI basics
- Dockerizing Python apps
- Cloud Run deployment
- GitHub Actions CI/CD pipelines
