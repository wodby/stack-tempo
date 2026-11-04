# Grafana Tempo application stack for Kubernetes on Wodby

Deploy distributed tracing on Kubernetes with Wodby: Grafana Tempo stores traces,
the OpenTelemetry Collector receives them, and Grafana shows them.

This repository defines the Wodby stack manifests and default service
composition for Grafana Tempo.

<!-- wodby:generated:start -->

## Stack contract

- [Grafana Tempo stack on Wodby](https://wodby.com/stacks/tempo)
- [Browse Wodby application stacks](https://wodby.com/stacks)
- [Wodby stack documentation](https://wodby.com/docs/2.0/stacks/)
- [Stack manifest reference](https://wodby.com/docs/2.0/stacks/template/)

## Service definitions

- [Grafana Tempo service](https://github.com/wodby/service-tempo)
- [OpenTelemetry Collector service](https://github.com/wodby/service-opentelemetry-collector)
- [Grafana service](https://github.com/wodby/service-grafana)

## What's included

| Component / service | Default configuration |
| --- | --- |
| Grafana Tempo<br>`tempo` | required; enabled by default; volumes: `data` 20 GB |
| OpenTelemetry Collector<br>`otel-collector` | required; enabled by default; links: `tempo` → `tempo` |
| Grafana<br>`grafana` | optional; enabled by default; volumes: `data` 10 GB; links: `tempo` → `tempo` |

Enabled optional services are selected by default but can be excluded when an
app is created. Disabled optional services are available but not selected by
default. Required services cannot be excluded.

## Validate the stack manifest

```bash
wodby stack validate-manifest stack.yml --org <org-id>
```

<!-- wodby:generated:end -->

## Send traces

Applications send traces to the OpenTelemetry Collector over OTLP. Its HTTP port
has a public HTTPS domain, so applications in other environments, other
clusters or outside Wodby can use it. Every request needs the Collector's
generated ingest token.

Set the standard OpenTelemetry SDK variables in the application:

```
OTEL_EXPORTER_OTLP_ENDPOINT=https://<Collector domain>
OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf
OTEL_EXPORTER_OTLP_HEADERS=Authorization=Bearer%20<ingest token>
OTEL_SERVICE_NAME=<application name>
```

The Collector accepts traces only. Set `OTEL_METRICS_EXPORTER=none` and
`OTEL_LOGS_EXPORTER=none` for SDKs and agents that also export metrics or logs.

Open Grafana and use the Tempo data source, or **Drilldown → Traces**, to search
traces. Traces are kept for 7 days by default; change the retention in the Tempo
service's config.
