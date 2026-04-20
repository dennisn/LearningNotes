# Grafana Concepts and Basic Configuration

- Course URL: <https://app.pluralsight.com/ilx/video-courses/grafana-concepts-basic-config/course-overview>

## What is Grafana

- Grafana is an open‑source project for real‑time observability and data visualization
  - "Grafana Labs" is the company name
- Deployment options: clouds, self-host, by container, as managed-service or as Enterprise service
- Service-Reliability Engineering (SRE) principals
  - Concepts
    - Service-Level Indicators (SLIs): measure performance/reliability from the user point-of-view (POV)
    - Service-Level Objectives (SLOs): define targets for each SLI
    - Service-Level Agreements (SLAs): formalize (i.e. contract) expected performance & consequence if the system falls below those levels
  - Techniques: monitor SLIs & alert when SLO is not met
  - Dashboard: high-level view of system health, while provide tools for quick root causes analysis when things go wrong
- Grafana: provide observability, not just monitoring
  - Monitoring: notice when things go wrong
  - Observability: context when things go wrong --> why something go wrong: from metrics (Prometheus), logs (Loki), and traces (Tempo)
  - Overall system view: status of system components, status over time --> identify trends/patterns

## Install & Configure Grafana

- Cloud by Grafana labs: free option that is usable
- Linux demo: <https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian>
  - default username/password: `admin`
  - Also other installation options: Windows, Macs, Container

## Adding a Data Source to Grafana

- Data source: where Grafana pulls data from to create dashboards and visualizations
  - can combined data from different sources into one dashboard: Prometheus metrices (i.e. CPU & memory usage) and SQL with business related data (e.g. no. of sales/active users)
- **Prometheus**: open source monitor & alert toolkit --> store metrics as time series data
  - Collect data at interval, which can be retrieved with PromQL (Prometheus Query Language)
  - 2 components: node exporter & Prometheus itself
- Grafana pre-built Dashboards: useful as starting point, before modify/customise for your need
