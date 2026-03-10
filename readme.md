# Jenkins Fundamentals – CI Pipeline with Docker Agents

March 3, 2026

The goal of this project was to learn how Jenkins pipelines work in a containerized CI environment using Docker.

In this project I built a Jenkins controller running in Docker, configured ephemeral Docker agents, and created a pipeline triggered by SCM polling. The pipeline runs a small Python application and demonstrates how Jenkins executes builds across different stages and environments.

This project also explores how Jenkins can securely interact with the Docker daemon on the host using a Docker proxy container.

Link to Jenkins course from DevOps Journey: https://www.youtube.com/watch?v=6YZvp2GwT0A

<img width="696" height="238" alt="image" src="https://github.com/user-attachments/assets/3b314b25-e2be-49cf-aa2b-c459a121cd19" />

## Architecture

```
Developer → Git Repository
        │
        ▼
Jenkins Controller (Docker container)
        │
        ▼
Docker Proxy (Socat container)
        │
        ▼
Docker Host
        │
        ▼
Ephemeral Docker Agents
   • Alpine Agent
   • Python Agent
```

## Overview

This setup includes:

- Jenkins running in Docker
- The Blue Ocean plugin for pipeline visualization
- Ephemeral Docker agents for isolated builds
- A Docker socket proxy for secure communication with the Docker host
- A Jenkins pipeline triggered with SCM polling

NOTES: Blue Ocean provides a modern interface to create and visualize CI/CD pipelines and makes it easier to understand pipeline execution stages. Using Docker agents allows Jenkins to dynamically provision build environments so that each job runs in an isolated container instead of relying on static machines.

## Key Componnents

### Jenkins Controller

The Jenkins controller manages pipelines and assigns jobs to agents.

It stores:

- Pipeline configurations
- Build logs
- Job history

### Docker Agents

Two ephemeral agents were configured:

*Alpine Agent*
```
jenkins/agent:alpine-jdk21
```

*Docker Agent*
```
python-agent (local image)
```

### Docker Proxy

Because Jenkins runs inside a container, it cannot directly access the host Docker daemon. A Socat Docker proxy container was created to bridge communication:

```
Jenkins Container
     │
 TCP Connection
     │
Docker Proxy
     │
Unix Socket
     │
Docker Daemon
```

### Pipeline

The pipeline runs a small Python application using two stages.

*Build* --> Creates a Python virtual environment and installs dependencies.
```
python3 -m venv venv
. venv/bin/activate
pip install -r requirements.txt
```

*Test* -->  Runs the Python application with different parameters.
```
python3 hello.py
python3 hello.py --name=Brad
```

### Pipeline Trigger

The pipeline uses SCM polling to detect repository changes. This tells Jenkins to check the repository every 5 minutes.
```
*/5 * * * *
```

### Lessons Learned

This project helped reinforce several DevOps concepts:

- Jenkins controller vs agent architecture
- Dynamic Docker build agents
- Jenkins pipelines using Jenkinsfile
- Containerized CI environments
- Docker socket proxy patterns
- Handling Python environments inside CI pipelines
