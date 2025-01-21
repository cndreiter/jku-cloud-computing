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

View the frontend at [http://map.projectselene.org](http://map.projectselene.org) (replace the domain with your own domain for accessing the frontend on your own cluster).

View Kiali dashboard at [http://localhost:20001](http://localhost:20001) with:

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

### Database file upload

1. Obtain a source file in the `.osm.pbf` format, for example from https://download.geofabrik.de/europe.html
1. Make sure the database is running
1. Obtain the database password using 
    
    ```bash
    kubectl get secret postgresql-app -n jku-cloud-computing --template={{.data.password}} | base64 -d
    ```
1. Execute the command:
   
    ```bash
    kubectl port-forward svc/postgresql-rw -n jku-cloud-computing 5432
    ```
2. While that command is running open a seperate terminal to run:
    
    ```bash
    docker run -it --rm -v /path/to/download/folder:/downloads:ro iboates/osm2pgsql -U app -d app -H host.docker.internal --number-processes 4 /downloads/your-file-name-here.osm.pbf -W
    ```
3. Enter the database password when asked.

Please note that even if the upload fails at the end, a part of the map might still be available after the failed upload. If you are using linux you might also need to add `--add-host=host.docker.internal:host-gateway` to the run args.

## Technical Background

### Database

Configured using helm with GIS plugin.

### Backend

Uses [martin-server](https://martin.maplibre.org/), a vector tiles server written in Rust. We use the Docker image they provide.

### Frontend

Uses [Leaflet](https://leafletjs.com/) to interactively display maps.

### Istio

In order to simplify replication of our setup, we decided to install istio by converting it to a kubernetes yaml first. We did this using the following command:
```bash
helm template istiod istio/istiod -n istio-system --kube-version v1.30.5 > istio.yaml
```

# Lessons Learned

- Minikube eats considerable amount of memory when working locally, more lightweigt alternatives could be evaluated.
- Google Cloud visibility did cause problems multiple times. We more or less learned some of the quirks of Google Cloud.
- Simulating Node failure led to failure of the application. We tried reducing resource limits of our pods to no avail. Turns out even though e2-medium on GKE advertises 2CPU's per Node, only 940m is actually allocatable. We switched to e2-standard-4 with 4CPU's per Node.
- Simulating Node failure still sometimes lead to long downtime, this was due to the pods for a certain component being all located on the same Node. We included Anti Affinity for Frontend and Backend Pods. Database seems difficult and would require additional effort in terms of configuration.
- Istio is not easy to configure, when applying istio manifest for the second time on a fresh Cluster we get errors about conflicts between fields.
