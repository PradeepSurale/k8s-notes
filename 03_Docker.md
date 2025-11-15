## What is Docker?

Docker is a containerization platform that lets you **package an application and all its dependencies into a lightweight, portable unit called a container**. These containers run consistently across different environments—developer laptop, test servers, production, or cloud—without compatibility issues.

In simple words:

  - **Docker = A tool to build, run, and manage containers.**
  - Containers = Small, isolated environments for running applications.


## What Docker Does?

**1. Package (Build) the Application**

You write `a Dockerfile` that defines:
- Base OS/runtime
- App code
- Dependencies
- Commands to run

Then Docker creates **an image** from it.

**2. Run the Application**

Docker runs containers from the image:
```
docker run my-app
```

**3. Ship the Application**
Docker images can be uploaded to:
- Docker Hub
- AWS ECR
- GCP Artifact Registry

This allows easy download and deployment anywhere.

## Docker Architecture (Simple View)
![Alt text](./resources/docker-architecture.jpg "Docker Architecture")


## Key Components
1. **Dockerfile** - Script that describes how to build the image.
2. **Image** - A blueprint/template for a container.
3. **Container** - A running instance of an image.
4. **Docker Engine** - Background service that runs containers.
5. **Docker Hub** - Public registry to store/share images.


## Docker Most Common Commands
1. Build image
```
docker build . -t myapp:1.0
```

2. Tag Image
```
docker tag myapp:1.0 myrepo/myapp:1.0
```

3. Push image
```
docker push myrepo/myapp:1.0
```

4. Pull image
```
docker pull nginx
```

5. List images
```
docker images
```

6. Run a container
  ```
  docker run IMAGE
  ```
  Examples:
  ```
  docker run nginx
  docker run -d -p 8080:80 nginx
  ```
7. List running containers
```
docker ps
```

8. List all containers (including stopped)
```
docker ps -a
```

9. Start/Stop/Restart/Remove container.
```
docker stop CONTAINER_ID
docker start CONTAINER_ID
docker restart CONTAINER_ID
docker rm CONTAINER_ID
```

10. List logs
```
docker logs CONTAINER_ID      -- once
docker logs -f CONTAINER_ID   -- to see continues
```

11. Exec in containe
```
docker exec -it CONTAINER_ID bash
```
