# Run-Docker-inside-Docker

# 🔹 1. What is DinD?

Docker-in-Docker (DinD) allows you to run Docker inside a Docker container.
It’s useful for:
- CI/CD pipelines that build Docker images
- Testing containerized environments
- Isolated Docker experimentation

# 🔹 2. Using the Official docker:dind Image
Run DinD Container
```
docker run --privileged --name dind-test -d docker:dind
```
✅ ```--privileged``` is required to give the container access to host kernel features.

# 🔹 3. Access the DinD Container
```
docker exec -it dind-test sh
```
Inside this container, you can now run:
```
docker run hello-world
```
Yes! You’re running Docker inside Docker 🚀

# 🔹 4. Mount Docker Socket (Alternative Approach)

Another way (safer & more common in CI/CD) is Docker-outside-of-Docker (DooD) — instead of running DinD fully, you mount the host Docker socket:
```
docker run -v /var/run/docker.sock:/var/run/docker.sock -it docker
```
This way, the container uses the host’s Docker daemon.
👉 Faster, but less isolated.

🔹 5. Use in CI/CD (Example GitLab CI)
```
image: docker:20.10.16

services:
  - docker:dind

variables:
  DOCKER_TLS_CERTDIR: "/certs"

build-job:
  stage: build
  script:
    - docker build -t myapp .
    - docker run myapp
```
# ✅ Pros & Cons
## ✔️ Pros:
- Fully isolated Docker environment
- Great for CI/CD pipelines
- Easy to experiment

## ⚠️ Cons:

- Slower (nested containers)
- ```--privileged``` mode is a security risk
- DooD (mounting Docker socket) is usually preferred
