# Grafana Tempo service for Kubernetes on Wodby

Run Grafana Tempo as a reusable Kubernetes application service with Wodby.

This repository defines the Wodby service manifests and operational
configuration for Grafana Tempo.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Wodby stacks using this service

- [Grafana Tempo application stack](https://github.com/wodby/stack-tempo)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `tempo` |
| Type | Application service |
| Versions | `3.1` by default |
| Workloads | `main` (Statefulset, primary) |
| Containers | `tempo` using `grafana/tempo` |
| Endpoints | `tempo`: HTTP 3200 (main), TCP 4317, HTTP 4318 |
| Service links | None |
| Application build | Not buildable from application source |
| Helm | chart `oci://registry-1.docker.io/wodby/stateful`; version `0.2.0` |
| Configuration and operations | 1 configuration files, 1 volumes |

## Use this service

Use this service through [Grafana Tempo application stack](https://github.com/wodby/stack-tempo), or reference `tempo` from a custom
Wodby stack.

A service is a reusable component and does not deploy by itself. The stack
defines its links, settings, versions, resources, and relationship to the rest
of the application.

## Maintain a custom version

1. Fork this repository.
2. Edit the service manifest and referenced files.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, volume, config, and
derivative names stable unless dependent stacks and app-level overrides are
updated at the same time.

Validate the manifests with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/) and the [managed services index](https://github.com/wodby/services).
