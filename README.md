# observability-stack-with-docker-compose

Scenario: An existing web server is experiencing intermittent performance issues, but the team lacks visibility into its operations. This repo shows how to deploy an observability stack using Docker Compose to monitor the web server.

## Run the application

```bash
docker compose up -d
```

## Access the services via

- [Grafana](http://localhost:3000) (default login details)
- [Prometheus](http://localhost:9090)
- [Nginx](http://localhost:8080)
- [Loki](http://localhost:3100)

## Generate traffic/errors

```bash
curl -s http://localhost:8080/health
ab -n 1000 -c 50 http://localhost:8080/   # or hey -z 30s -c 10
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8080/500
```

## Dashboard & alerts setup

### Panels

Panels were built for each monitoring tool. There's Prometheus for metrics and Loki for logs.

- **Prometheus** shows active connections, requests/sec, connections accepted/handled.

- **Loki**: HTTP status rates by code, recent logs, top endpoints, 5xx errors/sec.

### Alerts

An alert was setup to notify engineers whenever there is a high rate of 5xx error from the website. This was setup using a Loki query.

`sum(rate({job="nginx-logs", status=~"5.."}[5m])) > 1 for 5 minutes`

## Visuals

![Nginx](./images/nginx.png)
![Nginx Exporter](./images/nginx-exporter.png)
![Prometheus](./images/prometheus.png)
![Grafana](./images/grafana.png)
![Running Containers](./images/running-containers.png)
![Dashboard-1](./images/dashboard-1.png)
![Dashboard-2](./images/dashboard-2.png)
