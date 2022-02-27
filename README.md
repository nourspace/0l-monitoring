# 0l-monitoring

Monitoring tools for https://0l.network

This repository provides guides for both monitoring providers and node operators.

- Monitoring providers [`MPs`]: any party willing to provide monitoring services for 0L node operators by running
  monitoring tools such as Prometheus stack.
- 0L node operators [`OPs`]: any party running any type of 0L nodes (validator/VFN or fullnode) who want to minitor
  their nodes.

---

## Prometheus Stack

_[Prometheus](https://prometheus.io/) is an open source application which can scrap the real-time metrics to monitor
events and also do real-time alerting._

_[Grafana](https://grafana.com/) is an analytical and visualization tool which is helpful to create interactive charts &
graphs from the data and alerts scraped from the monitoring tools._

0L diem node exports set of Prometheus metrics that we would like to collect and use to build Grafana dashboards. These
are exported on ports `9101` and `9012`. In addition to diem metrics, node operators can choose to expose system metrics
like CPU, memory, storage, and others using [Prometheus Node Exporter](https://prometheus.io/docs/guides/node-exporter/)
.

Guides on how to set up Prometheus and Grafana instances can be found here:

- [Prometheus](./prometheus/README.md) [MPs]
- [Grafana](./grafana/README.md) [MPs]

As for node operators they can follow the steps below to allow monitoring providers to collect metrics from their hosts.

### Modifications to 0L hosts [OPs]

- Pick your [monitoring provider](#monitoring-providers) from the list below

- Open ports `9100-9102` to `$PROMETHEUS_STATIC_IP` (and probably to your own IP as well)
  > Depending on your host and firewall, you might need to enable that on different places; `ufw`, Digital Ocean Firewall, AWS Security Groups, etc.

- Install [Node Exporter](https://prometheus.io/docs/guides/node-exporter/)
  This assumes you are running Ubuntu
  ```shell
  sudo apt update
  sudo apt install prometheus-node-exporter
  ```
  or use [manual setup](./prometheus/README.md#Manual-Node-Exporter-setup)

- Confirm these endpoints are working
  - `curl http://YOUR-IP:9100/metrics`
  - `curl http://YOUR-IP:9101/metircs`
  - `curl http://YOUR-IP:9102/metrics`

- Share your host IP and validator ID with the monitoring provider

### Grafana Dashboards

> Example dashboards from N Space Grafana.

- #### 0L - Experiments
  ![0L - Experiments](https://i.imgur.com/5mTpz7l.jpg)
  http://grafana.openlibra.space:3000/d/MCc5xsTnz/0l-experiments?orgId=1&from=now-24h&to=now

- #### Validator monitoring
  ![Validator monitoring](https://i.imgur.com/QKi3XCd.jpg)
  http://grafana.openlibra.space:3000/d/llApF3o7k/validator-monitoring?orgId=1from=now-24h&to=now

- #### System Monitoring
  ![System Monitoring](https://i.imgur.com/sJrybQo.png)
  http://grafana.openlibra.space:3000/d/rYdddlPWk/system-monitoring?orgId=1&refresh=1m

---

## Monitoring Providers

- #### 1. N Space

  ```
  Prometheus
  Url      : http://prometheus.openlibra.space:9090
  Static IP: 206.189.251.112
  
  Grafana
  Url      : http://grafana.openlibra.space:3000
  Auth     : `viewer:viewer` (view only)
  ```
  Discord: `@nour_space#6652`

---

## Todo

- [ ] Add specific todos for Prometheus and Grafana setup guides
- [ ] Consider using K8s operators and/or Helm charts to run Prometheus stack
  - [ ] Use HTTPs and load balancers
- [ ] Link to and/or integrate other monitoring tools built by the 0L community
  - [ ] Enable alerting on Grafana dashboards

---

## Legacy

Some tasks and question from the Hackmd document that need to be integrated in the current todos.

https://hackmd.io/9dxv7ZwYS1yOmBVSjSV2wg

### Questions (old)

- [x] Privacy: Should I use single job for all nodes or keep it this way?
  ```
    - job_name: '0l-node-VAL_ID-XXXX'
      static_configs:
      - targets: [
        'a.b.y.x:9100',
        'a.b.y.x:9101',
        'a.b.y.x:9102'
      ]
  ```
  > The above makes it easier to know whose node is offline during development

  :star: Will use a random acronym that we agree on once you share your IP :star:

- [ ] Tech:
  Might want to log some more metrics like release version, network (fullnode, validator, vfn)
  For these we can either build our own http server that provides these by collecting from other services and only
  export its port, or directly adding metrics to the diem code which exposes metrics on 9101/9102

- [ ] Security:
  We want to create our own node-exporter config to only send meaningful and safe system metrics.

- [ ] Decentralization:
  We are running the two instances on our own for now, but thinking how to move this forward where there is no single
  point of failure neither a single entity hosting everyone's metrics.

## Todo (old)

- [x] Create Prometheus instance
- [x] Install node-exporter on own node
- [x] Test scraping own diem and node metrics
- [x] Collect more IPs
- [x] Install Grafana
  - [x] Provide viewer access
  - [ ] Provide editor access
  - [ ] Consider anon access?
    https://grafana.com/docs/grafana/latest/auth/grafana/#anonymous-authentication
- [ ] Create dashboards/panels
  - [x] Experimental
  - [x] Validator Monitoring
  - [x] System Monitoring: Node-Exporter dashboards
  - [ ] Define more dashboards Inspirations
    - Solana: https://chainflow.io/introducing-solana-mission-control/
    - Cosmos: https://chainflow.io/cosmos-validator-mission-control/
      https://github.com/Chainflow/cosmos-validator-mission-control
- [ ] Auto-discover node IPs
- [ ] Parse JSON metrics from port `6191`
  http://35.184.98.21:6191/metrics
  > Might help: https://stackoverflow.com/questions/55959877/convert-json-object-to-prometheus-metrics-format-using-jq/55959932#55959932
- [ ] Use port 80 for Grafana Both should be faced with some load balancer
- [ ] Move everything to a proper setup on K8s URLs, SSL, access, backups, ...
