# Highly Resilient Geographic Map Application - Project Overview

The system can be recreated using the commands below. Wait for the system to stabilize after every command.

```bash
kubectl apply --server-side --wait -R -f ./prerequisites/
kubectl apply --server-side --wait -R -f ./apps/
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