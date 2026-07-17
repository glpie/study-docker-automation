# Burger App - Microservices

A multi-service application built with Flask microservices, orchestrated using Docker containers.

## Architecture

The application consists of four microservices:

| Service | Port | Purpose |
|---------|------|---------|
| **auth-service** | 5000 | Authentication and authorization |
| **user-service** | 5001 | User management and profiles |
| **order-service** | 5002 | Order processing and management |
| **product-service** | 5003 | Product catalog and inventory |

## Prerequisites

- Docker and Docker Compose installed
- `.env` file configured in the `burguer-app` directory
- Each service must be built as a Docker image with tag `1.0`

## Quick Start

### Build Docker Images

First, build the Docker images for all services:

```bash
docker build -t auth-service:1.0 ./auth-service
docker build -t user-service:1.0 ./user-service
docker build -t order-service:1.0 ./order-service
docker build -t product-service:1.0 ./product-service
```

### Run All Services

Start all microservices at once (with network creation):

```bash
make run-all
```

This will:
1. Create a Docker network named `microservices-network` (if it doesn't exist)
2. Launch all four services in detached mode with their respective ports exposed
3. Connect all services to the network for inter-service communication

**Run without creating a network:**
```bash
make run-all CREATE_NETWORK=no
```

## Available Commands

### Run All Services
```bash
make run-all
```
Starts all microservices (auth, user, order, and product services) with network creation enabled by default.

**Without network creation:**
```bash
make run-all CREATE_NETWORK=no
```

### Create Docker Network
```bash
make create-network
```
Creates the `microservices-network` Docker network for inter-service communication. If the network already exists, this command will skip creation safely.

### Run Individual Services

Run auth-service on port 5000:
```bash
make run-auth
```

Run user-service on port 5001:
```bash
make run-user
```

Run order-service on port 5002:
```bash
make run-order
```

Run product-service on port 5003:
```bash
make run-product
```

**Note:** Individual service commands respect the `CREATE_NETWORK` variable. If you run individual services with `CREATE_NETWORK=yes`, they will connect to the network.

### Stop All Services
```bash
make stop-all
```
Stops all running microservice containers.

### View Help
```bash
make help
```
Displays all available Makefile commands and their descriptions.

## Network Configuration

### Docker Network
By default, `make run-all` creates a custom Docker network called `microservices-network`. This allows services to communicate with each other using container names as hostnames.

**Example inter-service communication:**
- From `order-service`, call `http://user-service:5000` to reach the user service
- From `product-service`, call `http://order-service:5000` to reach the order service

To disable network creation, use:
```bash
make run-all CREATE_NETWORK=no
```

## Environment Configuration

Each service uses its own `.env` file located in its service directory. The following variables are configured:

```env
MONGO_URI=mongodb+srv://[credentials]@[cluster]/burguer-app
PORT=5000
appName=burguer-app
SECRET_KEY=[your-secret-key]
JWT_SECRET=[your-jwt-secret]
```

Environment files are located at:
- `./auth-service/.env`
- `./user-service/.env`
- `./order-service/.env`
- `./product-service/.env`

## Service Details

Each microservice is a Flask application with:

- **Source Code**: `./[service-name]/`
- **Dockerfile**: Located in each service directory
- **Configuration**: `config/` directory
- **Controllers**: `controllers/` directory for route handlers
- **Models**: `models/` directory for database models
- **Services**: `services/` directory for business logic
- **Tests**: `test/` directory with pytest configuration

## Checking Running Containers

View all running services:
```bash
docker ps
```

View logs for a specific service:
```bash
docker logs <container-id>
```

Follow logs in real-time:
```bash
docker logs -f <container-id>
```

## Stopping Individual Services

Stop a specific running container:
```bash
docker stop <container-id>
```

## Troubleshooting

### Port Already in Use
If a port is already in use, stop the existing container:
```bash
docker ps
docker stop <container-id>
```

### .env Files Not Found
Ensure `.env` files exist in each service directory:
- `./auth-service/.env`
- `./user-service/.env`
- `./order-service/.env`
- `./product-service/.env`

### Image Not Found
Rebuild the Docker images:
```bash
docker build -t [service-name]:1.0 ./[service-name]
```

### Network Already Exists
If you get an error about the network already existing, this is normal. The `make create-network` command handles this gracefully and won't fail.

### Services Can't Communicate
If services can't reach each other by name, ensure:
1. All services are running on the same network: `docker network inspect microservices-network`
2. Run with network enabled: `make run-all` (not `make run-all CREATE_NETWORK=no`)
3. Check service names match exactly in your service-to-service calls

## Development

For local development without Docker, refer to individual service README files for setup instructions.
