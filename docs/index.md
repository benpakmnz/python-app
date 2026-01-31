# Python Flask Application

A lightweight Flask-based microservice that provides system information and health check endpoints. This service is containerized for easy deployment on Kubernetes.

## Overview

This is a simple REST API service built with Flask that exposes endpoints for retrieving system information and health status. The service is designed to run in containerized environments and includes Kubernetes deployment configurations.

## Features

- **System Information Endpoint** - Returns current time, hostname, and deployment status
- **Health Check Endpoint** - Returns service health status
- **Docker Support** - Fully containerized with Dockerfile
- **Kubernetes Ready** - Includes deployment manifests and Helm charts
- **REST API** - JSON responses for all endpoints

## Prerequisites

- Python 3.x
- Flask 3.0.3
- Docker (for containerization)
- Kubernetes cluster (for deployment)

## Installation

### Local Development

1. **Clone or navigate to the project:**
   ```bash
   cd python-app
   ```

2. **Create a virtual environment (optional but recommended):**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application:**
   ```bash
   python src/app.py
   ```

The application will start on `http://localhost:5000`

## API Endpoints

### 1. System Information

**Endpoint:** `GET /api/v1/info`

**Description:** Returns current system information including time, hostname, and deployment location.

**Response:**
```json
{
  "time": "12:34:56PM on January 31, 2026",
  "hostname": "your-machine-name",
  "message": "You are doing great today!!!!!!!!!! :))",
  "deployed_on": "Kubernetes"
}
```

**Example:**
```bash
curl http://localhost:5000/api/v1/info
```

### 2. Health Check

**Endpoint:** `GET /api/v1/healthz`

**Description:** Returns the health status of the service.

**Response:**
```json
{
  "status": "up"
}
```

**HTTP Status Code:** `200 OK`

**Example:**
```bash
curl http://localhost:5000/api/v1/healthz
```

## Docker Deployment

### Build Docker Image

```bash
docker build -t python-app:latest .
```

### Run Docker Container

```bash
docker run -p 5000:5000 python-app:latest
```

The application will be accessible at `http://localhost:5000`

### Docker Image Details

- **Base Image:** Defined in Dockerfile
- **Port:** 5000 (exposed)
- **Entry Point:** Runs `python src/app.py`

## Kubernetes Deployment

### Prerequisites

- Kubernetes cluster running
- kubectl configured to access your cluster
- Docker image pushed to a container registry

### Deployment Methods

#### Option 1: Using kubectl (Raw YAML)

```bash
kubectl apply -f k8s/deploy.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

#### Option 2: Using Helm

```bash
helm install python-app ./charts/python-app/
```

### Access the Service

1. **Port Forwarding:**
   ```bash
   kubectl port-forward svc/python-app 5000:5000
   ```
   Access at: `http://localhost:5000`

2. **Ingress:**
   See `k8s/ingress.yaml` for ingress configuration

3. **Service Discovery:**
   From within the cluster: `http://python-app:5000`

### Kubernetes Resources

- **Deployment** (`k8s/deploy.yaml`) - Manages pod replicas
- **Service** (`k8s/service.yaml`) - Exposes the application
- **Ingress** (`k8s/ingress.yaml`) - External HTTP routing

## Helm Chart

The Helm chart is located in `charts/python-app/` and includes:

- **Chart.yaml** - Chart metadata
- **values.yaml** - Default configuration values
- **templates/** - Kubernetes resource templates
  - `deployment.yaml` - Pod deployment configuration
  - `service.yaml` - Service configuration
  - `ingress.yaml` - Ingress configuration
  - `httproute.yaml` - HTTP routing (if using Flagger/Gateway API)

### Helm Installation

```bash
# Install
helm install python-app ./charts/python-app/

# Upgrade
helm upgrade python-app ./charts/python-app/

# Uninstall
helm uninstall python-app
```

## Project Structure

```
python-app/
├── src/
│   └── app.py              # Main Flask application
├── k8s/
│   ├── deploy.yaml         # Kubernetes deployment manifest
│   ├── service.yaml        # Kubernetes service manifest
│   └── ingress.yaml        # Kubernetes ingress manifest
├── charts/
│   └── python-app/         # Helm chart
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
├── Dockerfile              # Docker image definition
├── requirements.txt        # Python dependencies
└── README.md              # This file
```

## Configuration

### Environment Variables

Currently, the application uses default configuration. To customize:

1. **Port:** Modify `app.run(host='0.0.0.0', port=5000)` in `src/app.py`
2. **Host:** Default is `0.0.0.0` (accessible from all network interfaces)

### Kubernetes Configuration

Modify `charts/python-app/values.yaml` to customize:
- Replica count
- Resource limits/requests
- Environment variables
- Image registry/tag
- Service type
- Ingress settings

## Troubleshooting

### Service not responding

1. **Check if container is running:**
   ```bash
   docker ps  # For Docker
   kubectl get pods  # For Kubernetes
   ```

2. **Check health endpoint:**
   ```bash
   curl http://localhost:5000/api/v1/healthz
   ```

3. **View logs:**
   ```bash
   docker logs <container-id>  # Docker
   kubectl logs <pod-name>     # Kubernetes
   ```

### Port already in use

Change the port in `app.py` or use a different port when running:
```bash
python src/app.py --port 8000
```

## Development

### Adding New Endpoints

Edit `src/app.py`:

```python
@app.route('/api/v1/new-endpoint')
def new_endpoint():
    return jsonify({'data': 'value'}), 200
```

### Installing Additional Dependencies

1. Add package to `requirements.txt`
2. Reinstall dependencies: `pip install -r requirements.txt`
3. Rebuild Docker image if using containers

## Performance Considerations

- Application is lightweight and suitable for microservices
- No database dependencies - stateless design
- Can be scaled horizontally in Kubernetes
- Suitable for containerized deployments with auto-scaling

## Security

- Application runs on all network interfaces (0.0.0.0)
- No authentication/authorization implemented (add as needed)
- Consider network policies in Kubernetes for security
- Use secrets for sensitive configuration in production

## Deployment in Production

1. **Build and push image:**
   ```bash
   docker build -t registry.example.com/python-app:v1.0.0 .
   docker push registry.example.com/python-app:v1.0.0
   ```

2. **Update Helm values:**
   ```yaml
   image:
     repository: registry.example.com/python-app
     tag: v1.0.0
   ```

3. **Deploy with Helm:**
   ```bash
   helm install python-app ./charts/python-app/ -n production
   ```

## Monitoring

Monitor the health endpoint in production:
```bash
# Health check every 10 seconds
while true; do
  curl http://localhost:5000/api/v1/healthz
  sleep 10
done
```

## Support & Maintenance

For issues, improvements, or questions about this service, please refer to the project repository.

## License

[Add your license information here]

---

**Last Updated:** January 31, 2026
