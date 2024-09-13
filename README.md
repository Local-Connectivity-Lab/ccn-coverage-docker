# ccn-coverage-docker

## tl;dr
```
# clone
git clone https://github.com/Local-Connectivity-Lab/ccn-coverage-docker
cd ccn-coverage-docker
git checkout allconnect

# create .env file, with dev defaults
cat > .env << EOF
PUBLIC_URL=.
PORT=3002
COVERAGE_API_PORT=3000
SCRAPER_URL=http://localhost/plans
EOF

# generate keys
mkdir keys; cd keys
openssl ecparam -name secp256k1 -genkey -out api-secret
openssl ec -in api-secret -pubout -out api-pub

# start the system (it will take a couple of minutes)
docker compose up -d --build
```

## Overview

This repository contains the Docker setup for deploying the coverage API and visualization services locally for [coverage.seattlecommunitynetwork.org](https://coverage.seattlecommunitynetwork.org/)
. It includes the following components:
- **ccn-coverage-api**: The backend API service.
- **ccn-coverage-scraper**: The AllConnect API service.
- **ccn-coverage-vis**: The frontend visualization service.
- **MongoDB**: The database service.
- **nginx**: To provide reverse proxy.

## Prerequisites

Ensure you have the following installed on your system:
- [Docker](https://docs.docker.com/get-docker/)

## Deployment

Follow these steps to deploy the services using Docker Compose.

### Clone the Repository

```sh
git clone https://github.com/Local-Connectivity-Lab/ccn-coverage-docker.git
cd ccn-coverage-docker
```

### Update Environment Variables

Ensure the `MONGODB_URI` and `API_URL` environment variables are correctly set in the `docker-compose.yml` file.

```yaml
services:
  ccn-coverage-api:
    ...
    environment:
      - MONGODB_URI=mongodb://mongodb:27017/api-data

  ccn-coverage-vis:
    ...
    environment:
      - API_URL=http://ccn-coverage-api:3000
```

### Build and Start the Services

To build and start the services, run the following command:

```sh
docker compose up -d
```

### Verify the Deployment

1. **Check the running containers:**

   ```sh
   docker ps
   ```

   You should see the containers for `ccn-coverage-api`, `ccn-coverage-vis`, and `mongodb` running.

2. **Access the services in your browser:**
   - **API service:** `http://localhost:3000`
   - **Visualization service:** `http://localhost:3002`

3. **Check the logs for any errors:**

   ```sh
   docker compose logs -f
   ```

## Maintenance

### Stopping the Services

To stop the running services, use:

```sh
docker compose down
```

### Rebuilding the Services

If you make changes to the Dockerfiles or the source code, rebuild the services:

```sh
docker compose up --build -d
```

### Removing Unused Docker Images

To clean up unused Docker images and free up space, run:

```sh
docker system prune -a
```

## Troubleshooting

- **Check container logs for errors:**
  ```sh
  docker logs <container_name>
  ```

- **Verify MongoDB connection:**
  ```sh
  docker exec -it ccn-coverage-docker-mongodb-1 mongo
  use api-data
  show collections
  ```

## Contributing

Feel free to open issues or submit pull requests for any improvements or bug fixes.
