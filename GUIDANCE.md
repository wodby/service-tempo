# Grafana Tempo on Wodby

What Wodby sets up for Tempo on this service. It runs the official `grafana/tempo` image as a single instance in
monolithic mode, with the configuration file from the `config` entry mounted at `/etc/tempo/tempo.yaml`.

## How other services reach it

All ports are private; nothing is published outside the environment.

- Query API: `http://<Tempo service name>:3200`. Grafana uses this address as the Tempo data source URL.
- OTLP ingest: gRPC on port `4317` and HTTP on port `4318`. Tempo accepts traces from anything inside the environment
  without authentication. Applications in other environments should send traces to an OpenTelemetry Collector instead,
  which can be published with a token.

## What is configured

- Traces are stored on the local filesystem of the `data` volume, mounted at `/var/tempo`: the write-ahead log in
  `/var/tempo/wal` and blocks in `/var/tempo/blocks`.
- Traces older than 7 days are deleted (`overrides.defaults.compaction.block_retention: 168h`).
- Tempo sets its Go memory limit from the container memory limit.
- TraceQL search and TraceQL metrics queries work without extra configuration. The metrics generator (span metrics and
  service graphs written to Prometheus) is off.
- Usage reporting to Grafana Labs is off.
- The container runs as user `10001`, not root. The image has no shell.
- `/ready` on port 3200 answers when Tempo can accept requests.

## Changing configuration

Edit the `config` entry of the app service, not the file in the container. Tempo reads the file only when it starts:
after saving a change, deploy the service. Wodby restarts the pod when the file changed. Keep the listen ports and storage
paths unchanged; the service's endpoints and volume depend on them.

Common changes:

- Retention: `overrides.defaults.compaction.block_retention`, for example `336h` for 14 days. Increase the volume
  size together with retention.
- Span metrics and service graphs: add a `metrics_generator` section with `storage.path` on the volume and a
  `remote_write` URL of a Prometheus-compatible service, and list the processors under
  `overrides.defaults.metrics_generator.processors`.

## Data

The data volume holds all traces. The manifest declares no Wodby backups, imports or actions. Tempo is not scalable on
this service: it runs one instance on one volume.

## Check the result

Tempo's image has no shell, so check it from another service in the environment, for example Grafana:

- `curl -s http://<Tempo service name>:3200/ready`
- `curl -s 'http://<Tempo service name>:3200/api/search/tag/service.name/values'` lists the services that sent traces.
