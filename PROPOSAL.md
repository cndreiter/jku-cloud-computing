# Project Architecture Overview

For this project we decided to deploy an application which displays an interactive map to the user. For this we will use multiple components listed below for functionality, stability and security.

We intend to deploy each component in a way that allows it to scale vertically and can survive single node outages.

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
- I guess we definetly want to integrate this, only to what extent?

### Authentication and Authorization
- **Purpose**: Secures interactions between the frontend and backend.
- **Technology**: JWT-based authentication or integration with OAuth/OpenID.

## Data Flow
1. **User Interaction**:
   - A user interacts with the frontend (e.g., pans or zooms the map).
   - The frontend sends requests to the web server for map data.

2. **Request Handling**:
   - The web server receives the request and routes it to the fileserver for the requested tiles or data.

3. **Service Mesh**:
   - Istio manages the traffic flow, ensuring reliability with features like retries, circuit breaking, and fallbacks.

4. **Map Data Delivery**:
   - The fileserver responds with the required map data, which the web server forwards to the frontend for rendering.

5. **Resilience**:
   - If the primary fileserver fails, Istio reroutes traffic to a backup server (if configured).

## Kubernetes Deployment

### Cluster
- Our Kubernetes cluster hosts all services and the Istio service mesh.

### Pods
- Each service (frontend, fileserver, web server, optional components) runs in its own pod.

### Services
- Kubernetes `Services` expose each component internally within the cluster.
- The frontend may be exposed externally using a `LoadBalancer` or `Ingress` resource.

### Istio Configuration
- Istio manages communication between services using VirtualServices, DestinationRules, and Gateways.

## Key Features

### Resilience
- Managed via Istio’s traffic rules and fault-tolerance mechanisms (e.g., retries, failovers).

### Observability
- Monitoring and logging are integrated to understand the system’s health and performance.

### Scalability
- Kubernetes allows scaling up/down services as required.

### Optional Chaos Engineering
- Intentional failures are introduced to test the resilience of the system under stress.
