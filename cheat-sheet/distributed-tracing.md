# Distributed Tracing Cheat Sheet

## 1. What is Distributed Tracing?
- **Definition**: Monitors requests across distributed systems (e.g., microservices) to track performance, latency, and errors.
- **Key Components**:
  - **Trace**: End-to-end request journey across services.
  - **Span**: Single unit of work (e.g., API call, DB query).
  - **Trace ID**: Unique identifier for a request.
  - **Span ID**: Unique identifier for each span.
  - **Context Propagation**: Passes trace/span IDs between services.

## 2. Why Use Agents in Distributed Tracing?
- **Purpose**:
  - Collect telemetry (traces, spans, metrics) from apps/services.
  - Automate instrumentation to reduce manual coding.
  - Handle context propagation for trace continuity.
  - Buffer and forward trace data to collectors/backends.
- **Benefits**:
  - Simplifies instrumentation (auto-instrumentation).
  - Reduces developer effort.
  - Ensures framework/language compatibility.
  - Minimizes performance overhead.
  - Supports vendor-agnostic tracing (e.g., OpenTelemetry).

## 3. Distributed Tracing Agents
| Agent               | Description                                                  | Supported Languages/Frameworks                     | Key Features                                                            | Use Case                              |
|---------------------|--------------------------------------------------------------|---------------------------------------------------|-------------------------------------------------------------------------|---------------------------------------|
| **OpenTelemetry**   | Vendor-agnostic, CNCF-standard for traces, metrics, logs.    | Java, Python, Node.js, Go, .NET, etc.             | Auto-instrumentation, OTLP export, multi-backend support (Jaeger, Zipkin). | Cloud-native, multi-vendor apps.      |
| **Jaeger Agent**    | Lightweight daemon, collects spans via UDP for Jaeger.       | Go, Java, Python, Node.js, C++.                   | Low-latency UDP collection, Jaeger UI integration.                      | High-performance microservices.       |
| **Zipkin Agent**    | Collects traces for Zipkin, supports OpenZipkin.             | Java, Scala, Go, Ruby, Python.                    | Simple integration, Spring Cloud Sleuth, RabbitMQ support.               | Legacy or Zipkin-based systems.       |
| **Datadog Agent**   | Proprietary agent for Datadog APM.                          | Python, Java, Ruby, Go, .NET, PHP.                | Auto-instrumentation, tail-based sampling, log/metric correlation.       | Enterprises using Datadog platform.   |
| **New Relic Agent** | Proprietary agent for New Relic APM.                        | Java, .NET, Python, Ruby, Go, Node.js, PHP.       | Auto-instrumentation, flame graphs, New Relic platform integration.      | Teams using New Relic observability.  |
| **AWS X-Ray Agent** | AWS-native tracing for serverless/containerized apps.        | Node.js, Java, .NET, Python, Go.                  | Auto-captures Lambda traces, integrates with AWS services.               | AWS-based microservices.              |

## 4. How Agents Work
- **Instrumentation**:
  - **Manual**: Add tracing libraries (e.g., OpenTelemetry SDK) to code.
  - **Automatic**: Agents inject tracing code at runtime (e.g., Java bytecode).
- **Data Collection**:
  - Agents collect spans, buffer locally, and forward to collectors.
- **Context Propagation**:
  - Passes trace/span IDs (e.g., via `traceparent` HTTP header).
- **Export**:
  - Agents send spans to collectors/backends via OTLP, gRPC, HTTP, or UDP.

## 5. Exporting Traces from Different Servers
- **Process**:
  1. **Instrumentation**: Each server runs an agent or tracing library.
  2. **Local Collection**: Agents buffer spans locally.
  3. **Forwarding**:
     - Agents send spans to a collector (e.g., OpenTelemetry Collector, Jaeger).
     - Protocols: OTLP, gRPC, HTTP, UDP.
     - Optional: Use Kafka/RabbitMQ for buffering.
  4. **Storage**: Collectors store spans in Elasticsearch, Cassandra, or cloud storage.
  5. **Visualization**: View traces in Jaeger UI, Zipkin UI, or commercial dashboards.
- **Example Configuration (OpenTelemetry)**:
  ```yaml
  receivers:
    otlp:
      protocols:
        grpc:
          endpoint: "0.0.0.0:4317"
  exporters:
    jaeger:
      endpoint: "jaeger-collector:14250"
  service:
    pipelines:
      traces:
        receivers: [otlp]
        exporters: [jaeger]
  ```
- **Challenges**:
  - **Clock Skew**: Use NTP for synchronized clocks.
  - **Network Latency**: Batch data to reduce overhead.
  - **Scalability**: Use distributed storage (e.g., Cassandra, AWS S3).
  - **Sampling**: Head-based or tail-based sampling to manage data volume.

## 6. Why Choose Specific Agents?
- **OpenTelemetry**: Vendor-agnostic, future-proof, broad language support.
- **Jaeger**: High-performance, open-source, lightweight.
- **Zipkin**: Simple, good for legacy or Spring-based systems.
- **Datadog/New Relic**: Unified observability for enterprises.
- **AWS X-Ray**: AWS-native, ideal for serverless.

## 7. Best Practices
- **Instrumentation**: Prefer auto-instrumentation; add custom spans for key operations.
- **Sampling**: Use tail-based sampling for critical traces; adjust rates.
- **Context Propagation**: Ensure `traceparent` headers are passed across services.
- **Storage & Visualization**: Use scalable storage; visualize with flame graphs/Gantt charts.
- **Monitoring**: Set alerts for latency/errors; correlate traces with logs/metrics.

## 8. Tools for Export and Visualization
- **Open-Source**:
  - **Jaeger**: Elasticsearch/Cassandra storage, Jaeger UI.
  - **Zipkin**: Cassandra/in-memory storage, Zipkin UI.
  - **OpenObserve**: Unified traces, logs, metrics.
- **Commercial**:
  - **Datadog**: Unified APM, flame graphs.
  - **New Relic**: Detailed traces, Infinite Tracing.
  - **AWS X-Ray**: AWS-integrated, service maps.
- **Collectors**:
  - **OpenTelemetry Collector**: Vendor-agnostic, supports OTLP/Jaeger/Zipkin.
  - **Jaeger Collector**: High-throughput, Elasticsearch-backed.
  - **Zipkin Server**: HTTP-based collection.

## 9. Example: Multi-Server Trace Export
- **Scenario**: Microservices app (Node.js, Java, Python).
- **Setup**:
  1. Install OpenTelemetry SDKs for each language.
  2. Deploy OpenTelemetry Collector centrally.
  3. Configure agents to send traces via OTLP.
  4. Export to Jaeger for storage/visualization.
- **Node.js Example**:
  ```javascript
  const { NodeTracerProvider } = require('@opentelemetry/sdk-trace-node');
  const { OTLPTraceExporter } = require('@opentelemetry/exporter-trace-otlp-grpc');
  const { Resource } = require('@opentelemetry/resources');
  const provider = new NodeTracerProvider({
    resource: new Resource({ 'service.name': 'node-service' }),
  });
  const exporter = new OTLPTraceExporter({ url: 'grpc://collector:4317' });
  provider.register();
  ```

## 10. Sources
- Datadog: Distributed tracing basics
- Dynatrace: Tracing and observability
- Lumigo: Microservices tracing tools
- Splunk: Microservices tracing
- Groundcover: eBPF-based tracing
- New Relic: Distributed tracing setup
- AWS: X-Ray tracing overview
- ServiceNow: OpenTelemetry exporters
- Honeycomb: Tracing benefits
- Middleware: Trace components
