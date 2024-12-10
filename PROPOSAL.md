# Project Architecture Overview

For this project we decided to deploy an application which displays an interactive map to the user. For this we will use multiple components listed below for functionality, stability and security.

We intend to deploy each component in a way that allows it to scale horizontally and can survive single node outages.

We also intend to look into "Chaos Engineering" which would further enhance the resilience of our system. For that purpose we may deploy additional components.

## High-Level Components

### Frontend
- **Technology**: Leaflet (JavaScript library for map rendering).
- **Purpose**: Displays maps and provides user interaction (zoom, pan, overlays).
- **Deployment**: Hosted in a Docker container, served via Kubernetes.
- **Responsible**: Christian

### Tileserver
- **Technology**: Martin Server.
- **Purpose**: Serves map data (e.g., tiles or vector data) to the frontend or web server.
- **Deployment**: Containerized and deployed on Kubernetes.
- **Responsible**: Marcus

### Database
- **Technology**: PostgresSQL
- **Purpose**: Stores raw data
- **Deployment**: Containerized and deployed on Kubernetes.
- **Responsible**: Julian

### Service Mesh
- **Technology**: Istio.
- **Purpose**: Manages network traffic between services.
  - Provides load balancing, traffic routing, retries, and fallbacks.
  - Handles observability (telemetry, logging, tracing).
- **Deployment**: Installed on the Kubernetes cluster as an overlay.
- **Reponsible**:
  - Setup: Together 
  - Data flow: Christian
  - Encryption: Julian
  - Network policies: Marcus

## Optional Components

### Chaos Engineering
We intend to look into tools that test for outage resilients including tools that automatically kill pods, generate large amount of traffics and randomly fails requests.

### Authentication and Authorization
- **Purpose**: Secures interactions between the frontend and backend.
- **Technology**: JWT-based authentication or integration with OAuth/OpenID.

## Milestones

- **Basic setup**: Frontend, Tileserver and PostgresSQL are deployed and working but not yet scalable and no istio mesh is deployed
- **Scalable setup**: Make sure that the components can scale horizontally.
- **Istio setup**: Setup istio mesh
- **Istio configuration**: Istio Data flow, Encryption, Network policies
- **Chaos Engineering**: Look into Chaos Engineering tools.
