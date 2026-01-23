# Continuous Experimentation Documentation

## Overview
This document describes the continuous experimentation setup for the SMS Spam Detection System, focusing on comparing two versions of the app-service and model-service using Istio traffic management and Prometheus metrics.

## Changes Compared to Base Design
- **Base Design**: Single version of app-service and model-service deployed with standard Kubernetes resources.
- **Experimental Design**: Two versions deployed (v1 and v2) with Istio DestinationRules ensuring consistent routing (old-old or new-new combinations). Traffic split: 90% to v1, 10% to v2 using VirtualService with sticky sessions.

## Falsifiable Hypothesis
The new version (v2) of the app-service, which includes optimized caching and reduced response times, will improve user experience by reducing average page load time by at least 20% while maintaining or improving spam detection accuracy.

## Relevant Metrics
- **Counter**: `http_requests_total` (total requests per version)
- **Gauge**: `response_time_seconds` (average response time)
- **Histogram**: `request_duration_seconds` (distribution of request durations)

## Decision Process
Metrics will be collected via Prometheus and visualized in Grafana. The dashboard will show:
- Time series of response times for v1 vs v2
- Request counts and error rates
- Comparison charts with statistical significance tests

Decision criteria:
- If v2 shows >=20% improvement in average response time with <5% increase in errors, adopt v2.
- Data collected over 7 days with at least 1000 requests per version.

## Grafana Dashboard
[Screenshot to include image of dashboard showing v1/v2 comparison]

## Implementation Details
- Istio VirtualService routes 90% to v1, 10% to v2 with cookie-based sticky sessions.
- DestinationRules ensure version consistency.
- Prometheus scrapes metrics from both versions.