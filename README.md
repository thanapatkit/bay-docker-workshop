# Java Spring Boot with Docker - Workshop

## Introduction

This repository demonstrates how to containerize a Java Spring Boot application using Docker. It covers pulling base images, writing a Dockerfile, building and running Docker containers, and optimizing the Dockerfile using multi-stage builds for better performance.

## Tech Stack

- **Java**: Version 23 (eclipse-temurin:23)
- **Spring Boot**: Version 3.3.4
- **Docker**: Used to containerize the application

## Prerequisites

Before you begin, ensure you have the following installed on your local machine:

- [Docker](https://docs.docker.com/get-docker/)
- Java Development Kit (JDK) 23
- Spring Boot 3.3.4
- Maven (for building the project)

## Clone the Repository

First, clone the GitHub repository to your local machine:

```bash
git clone https://github.com/up1/demo-docker-java.git
cd demo-docker-java
```

## Steps to Run the Application in Docker
# 1. Pull Base Image
We will use the eclipse-temurin:23 Docker image as our base Java runtime environment.

```bash
docker image pull eclipse-temurin:23
docker images
```

# 2. Write a Dockerfile
Below is an example of a simple Dockerfile for the project:

Version 1 (Basic Dockerfile)
This version uses a single stage to both build and run the application.

```bash
FROM eclipse-temurin:23-jdk
WORKDIR /xyz
COPY . .
RUN ./mvnw clean package
EXPOSE 8080
CMD ["java", "-jar", "target/api.jar"]
```
# 3. Build and Run the Docker Container
Follow these steps to build and run the Docker container for the application:

```bash
# Build the Docker image
docker image build -t myjava:1.0 .

# Run the container
docker run -d -p 8080:8080 myjava:1.0

# Check running containers
docker container ls
```

Once the container is running, the application should be accessible at:

http://localhost:8080/hello

# 4. Improve the Dockerfile with Multi-Stage Build
Multi-stage builds help reduce the final image size by separating the build and runtime environments. Here is an improved version of the Dockerfile that utilizes multi-stage builds:

Version 2 (Optimized Dockerfile)
```bash
FROM eclipse-temurin:23-jdk AS step01
WORKDIR /xyz
COPY . .
RUN ./mvnw clean package

FROM eclipse-temurin:23-jre-alpine
WORKDIR /build
COPY --from=step01 /xyz/target/api.jar .
EXPOSE 8080
CMD ["java", "-jar", "api.jar"]
```

In this version:

Stage 1 (Build): Uses the full JDK to build the Java application.
Stage 2 (Runtime): Uses a lightweight JRE to run the application, resulting in a smaller image.

#5. Build and Run with the Optimized Dockerfile
Follow the same steps to build and run the Docker container but with the optimized Dockerfile:

```bash
# Build the Docker image
docker image build -t myjava:2.0 .

# Run the container
docker run -d -p 8080:8080 myjava:2.0

# Check running containers
docker container ls
```
The application should still be accessible at http://localhost:8080/hello, but now with a smaller and more efficient Docker image.
