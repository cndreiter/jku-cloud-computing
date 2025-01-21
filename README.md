# Highly Resilient Geographic Map Application - Project Overview

## Setup

The system can be recreated using the commands below. Wait for the system to stabilize after every command.

The host names for the frontend API and for the frontend are located at the file _ingress/app-ingress.yaml_:
* Frontend API: _map-api.projectselene.org_
* Frontend: _map.projectselene.org_

They are just placeholders. Replace them with your own domains according to your needs and connect your domains to the IP address returned by the following command:

```
$ kubectl get ingresses -n jku-cloud-computing
NAME          CLASS    HOSTS                                             ADDRESS         PORTS   AGE
app-ingress   <none>   map-api.projectselene.org,map.projectselene.org   #.#.#.#         80      18h
```

```bash
$ kubectl apply --server-side --wait -R -f ./prerequisites/
$ kubectl apply --server-side --wait -R -f ./apps/
$ kubectl apply --server-side --wait -R -f ./ingress/
$ kubectl apply --server-side --wait -R -f ./monitoring/
```

View kiali dashborad at [http://localhost:20001](http://localhost:20001) with:

```bash
$ kubectl port-forward svc/kiali -n istio-system 20001
```

View Grafana dashboard at [http://localhost:8081](http://localhost:8081) (user: `admin`, password: `prom-operator`) with:

```bash
$ kubectl port-forward svc/prometheus-community-grafana -n monitoring 8081:80
```

### Grafana Database Dashboard

To view a preconfigured Dashboard of the Database using Grafana, head to the "Dashboard" section, click create & import Dashboard and use the following link for your import:

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

# Lessons Learned

- Minikube eats considerable amount of memory when working locally, more lightweigt alternatives could be evaluated.
- Google Cloud visibility did cause problems multiple times. We more or less learned some of the quirks of Google Cloud.
- Simulating Node failure led to failure of the application. We tried reducing resource limits of our pods to no avail. Turns out even though e2-medium on GKE advertises 2CPU's per Node, only 940m is actually allocatable. We switched to e2-standard-4 with 4CPU's per Node.
- Simulating Node failure still sometimes lead to long downtime, this was due to the pods for a certain component being all located on the same Node. We included Anti Affinity for Frontend and Backend Pods. Database seems difficult and would require additional effort in terms of configuration.
- Istio is not easy to configure, when applying istio manifest for the second time on a fresh Cluster we get errors about conflicts between fields.
