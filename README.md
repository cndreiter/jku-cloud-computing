# Highly Resilient Geographic Map Application - Project Overview

## Setup

The system can be recreated using the commands below. Wait for the system to stabilize after every command.

```bash
kubectl apply --server-side --wait -R -f ./prerequisites/
kubectl apply --server-side --wait -R -f ./apps/
kubectl apply --server-side --wait -R -f ./ingress/
kubectl apply --server-side --wait -R -f ./monitoring/
```

View kiali dashborad at [http://localhost:20001](http://localhost:20001) with:

```bash
kubectl port-forward svc/kiali -n istio-system 20001
```

View Grafana dashboard at [http://localhost:8081](http://localhost:8081) (user: `admin`, password: `prom-operator`) with:

```bash
kubectl port-forward svc/prometheus-community-grafana -n monitoring 8081:80
```

### Grafana Database Dashboard

To view a preconfigured Dashboard using Prometheus, head to the "Dashboard" section, click create & import Dashboard and use the following link for your import:

https://grafana.com/grafana/dashboards/20417-cloudnativepg/

## Database

Configured using helm with GIS plugin.

### Database file upload

Describe how to upload map data.

## Backend

Uses [martin-server](https://martin.maplibre.org/), a vector tiles server written in Rust. We use the Docker image they provide.

## Frontend

Uses [Leaflet](https://leafletjs.com/) to interactively display maps.

## Istio

Describe how we configured istio, using Helm.
