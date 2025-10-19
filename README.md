# Containerized Web Server (Flask) with CI/CD and Kubernetes

This example demonstrates a simple Flask web app, treating it like real software. It can run locally, in a Docker container, and on a Kubernetes cluster. A GitHub Actions workflow builds and pushes the image to Docker Hub whenever you push code.

## Why this project exists

If you're learning DevOps or looking for a clear reference, this repo shows the entire process:
- Build a small Flask app
- Package it using a minimal Dockerfile
- Automate image builds with CI/CD
- Run it reliably on Kubernetes with multiple replicas

## What’s included

- Flask app serving a coffee shop landing page
- Dockerfile for packaging the app
- GitHub Actions workflow to build and push images
- Kubernetes manifests (Deployment and Service)

## Repository structure

```
application/
	app.py
	Dockerfile
	requirements.txt
	templates/
		index.html
	k8s/
		Deployment.yml
		Service.yml
.github/
	workflows/
		deployment.yml
Iac/
README.md
```

## Prerequisites

- Python 3.10+ (only needed for local runs without Docker)
- Docker Desktop
- Docker Hub account (to push images)
- kubectl and a Kubernetes cluster (minikube, kind, or a cloud cluster)

## Run locally (no Docker)

```powershell
cd application
pip install -r requirements.txt
python app.py
```

Open http://localhost:5000

## Build and run with Docker

```powershell
cd application
docker build -t tuheen27/devops-projects:latest .
docker run --rm -p 5000:5000 tuheen27/devops-projects:latest
```

Open http://localhost:5000

## CI/CD (GitHub Actions)

The workflow located at `.github/workflows/deployment.yml` builds and pushes the Docker image on pushes and pull requests to the `main` branch.

Required repository secrets:
- `DOCKER_USERNAME`
- `DOCKER_PASSWORD`

The image tag used is `tuheen27/devops-projects:latest`.

Tip: If your Dockerfile is in the `application/` folder, set the workflow to `with.context: application` so it builds from the correct directory.

## Deploy to Kubernetes

Apply the manifests from the `application/k8s` folder.

```powershell
kubectl apply -f application/k8s/Deployment.yml
kubectl apply -f application/k8s/Service.yml
```

Details:
- Image: `tuheen27/devops-projects:latest`
- Replicas: 3
- Container port: 5000
- Service: NodePort on port 80 forwarding to targetPort 5000

Get the Service information and access URL:

```powershell
kubectl get svc flask-service -o wide
```

## Make it yours

- Change the Docker image name/tag in all relevant places (`Docker build`, GitHub Actions, and `Deployment.yml`).
- Customize `templates/index.html` for your own landing page.
- Add environment variables or ConfigMaps/Secrets if needed for configuration.

## Troubleshooting

- ImagePullBackOff: Ensure the image exists and is public, or set up a pull secret and reference it in the Deployment.
- Port already in use: Stop other apps using port 5000 locally, or map the container to a different host port (`-p 8080:5000`).
- CI build context: If the workflow cannot find the Dockerfile, set its build context to `application`.

---

Built for clarity and reproducibility. If you want this README adjusted for a different image name or registry, let me know.
