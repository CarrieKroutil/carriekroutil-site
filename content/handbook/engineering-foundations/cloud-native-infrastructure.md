---
title: "Cloud-Native & Infrastructure"
weight: 30
description: "What cloud-native actually means, the CNCF projects worth knowing, and the trio you'll meet everywhere — Docker, Kubernetes, and Terraform."
lastUpdated: 2026-06-25
goDeeper:
  - group: Tools
    title: "CNCF Cloud Native Landscape"
    url: "https://landscape.cncf.io/"
    why: "The map of the whole ecosystem — every project sorted by what it does, so you can place a tool before you adopt it."
  - group: Tools
    title: "Kubernetes documentation"
    url: "https://kubernetes.io/docs/home/"
    why: "The canonical reference — concepts, tutorials, and the API. Start with the Concepts section, not the install guide."
  - group: Tools
    title: "Docker documentation"
    url: "https://docs.docker.com/"
    why: "Where the Dockerfile reference and the get-started guide live — the fastest way from zero to a running container."
  - group: Courses
    title: "Terraform on HashiCorp Developer"
    url: "https://developer.hashicorp.com/terraform"
    why: "Hands-on tutorials and the full HCL reference, straight from the people who build it."
---

You don't need to be able to operate a Kubernetes cluster to manage a team that runs on one. But you do need a mental model of the pieces — enough to read an architecture diagram, follow an incident, and ask the question that matters. This page is that model: what "cloud-native" means, the projects worth recognizing, and the three tools you'll run into on almost every modern team.

## What "cloud-native" actually means

"Cloud-native" gets used as a buzzword, but it points at something specific. Cloud-native applications are:

- **Containerized** — packaged with their dependencies (e.g., Docker), so they run the same everywhere.
- **Dynamically orchestrated** — scheduled and scaled automatically (e.g., Kubernetes).
- **Microservice-based** — modular and loosely coupled, so pieces can change independently (one of the [architecture styles]({{< relref "/handbook/engineering-foundations/software-architecture-design.md" >}}) worth knowing).
- **Resilient, scalable, and observable** — built to survive failure, grow under load, and tell you what they're doing. That last property is what good [monitoring and alerting]({{< relref "/handbook/operational-excellence/monitoring-alerting.md" >}}) turns into something you can act on.

The point isn't to check every box. It's that these properties tend to reinforce each other, and the tooling below exists to make them achievable without heroics.

## CNCF: the ecosystem

The **Cloud Native Computing Foundation (CNCF)** is an open-source foundation under the Linux Foundation, created to support and promote cloud-native technologies. Its real value to you is as a map: when someone says "we're adding a service mesh," the CNCF landscape tells you what category that is and which projects are mature.

"Graduated" projects are the mature tier — widely adopted, with strong community governance and rigorous criteria for security, maintainability, and adoption. These are the names worth recognizing:

| Project | Category | What it does |
|--------|----------|--------------|
| [**Kubernetes**](https://kubernetes.io/) | Container orchestration | Automates deployment, scaling, and management of containerized applications. |
| [**Prometheus**](https://prometheus.io/) | Monitoring | Metrics-based monitoring with a time-series database and alerting. |
| [**Envoy**](https://www.envoyproxy.io/) | Service proxy / mesh | High-performance edge and service proxy (used in Istio). |
| [**etcd**](https://etcd.io/) | Key-value store | Consistent, distributed KV store — Kubernetes' backing store. |
| [**Helm**](https://helm.sh/) | Package manager | The apt/yum/homebrew of Kubernetes — manages K8s apps using charts. |
| [**containerd**](https://containerd.io/) | Container runtime | Industry-standard container runtime used by Docker and Kubernetes. |
| [**CoreDNS**](https://coredns.io/) | DNS | DNS server used in Kubernetes for service discovery. |
| [**Fluentd**](https://www.fluentd.org/) | Logging | Unified logging layer to collect, transform, and route logs. |
| [**OpenTelemetry**](https://opentelemetry.io/) | Observability | Standardized framework for traces, metrics, and logs. |
| [**gRPC**](https://grpc.io/) | Communication | High-performance, language-neutral RPC framework. |
| [**Linkerd**](https://linkerd.io/) | Service mesh | Lightweight, performant service mesh — an alternative to Istio. |
| [**Rook**](https://rook.io/) | Storage orchestration | Cloud-native storage orchestrator for Ceph and others. |
| [**Argo**](https://argoproj.github.io/) | CI/CD & workflows | Kubernetes-native tools for workflows, GitOps, and progressive delivery. |
| [**Vitess**](https://vitess.io/) | Database | Scalable MySQL clustering for cloud-native environments. |
| [**Jaeger**](https://www.jaegertracing.io/) | Tracing | Distributed tracing for monitoring microservices (from Uber). |
| [**Thanos**](https://thanos.io/) | Monitoring | Highly available Prometheus with long-term storage. |

**Why teams reach for CNCF projects:** they're battle-tested at scale (Google, Netflix, Uber), they follow open standards so they interoperate well — Kubernetes + Prometheus + Fluentd gives you full observability — and they're vendor-neutral, which avoids lock-in. Graduated projects also meet a real production-readiness bar, so adopting one is rarely a bet on something unproven.

{{< protip >}}
The CNCF landscape is overwhelming on purpose — it's a catalog, not a shopping list. When a team wants to add a tool, I ask which *category* it fills and what we'd remove or simplify by adding it. Most cloud-native pain I've seen comes from adopting five graduated projects to solve a problem that needed one.
{{< /protip >}}

Learn more: the [CNCF projects page](https://www.cncf.io/projects/), the [Cloud Native Landscape](https://landscape.cncf.io/), and the [graduation criteria](https://github.com/cncf/toc/blob/main/process/graduation_criteria.adoc).

## Docker: containers

Docker packages an application and everything it needs into an image that runs identically on a laptop, in CI, and in production. The core concepts:

- **Docker Client** — the CLI or GUI you run commands with (`docker build`, `docker run`).
- **Docker Daemon** — listens for client requests and manages Docker objects (images, containers, volumes, networks).
- **Dockerfile** — a declarative script defining how to build an image, layer by layer.
- **Images** — immutable, versioned templates built from Dockerfiles.
- **Containers** — running instances of images, with an isolated filesystem and processes.
- **Registry** — a place to push and pull images (Docker Hub, or self-hosted).
- **Volumes & Networks** — volumes give containers persistent storage; networks let them communicate securely.

## Kubernetes: orchestration

If Docker runs one container, Kubernetes (K8s) runs thousands across many machines, keeps them healthy, and scales them up and down. The pieces:

- **Control Plane**
  - **API Server** — the front door for all cluster commands.
  - **Scheduler** — assigns Pods to Nodes based on resource needs.
  - **Controller Manager** — ensures the desired state (Deployments, ReplicaSets) matches reality.
- **Worker Nodes**
  - **Kubelet** — the agent on each node that watches for Pod specs.
  - **Container Runtime** — (e.g., containerd) launches the containers.
  - **Pods** — the smallest deployable unit, grouping one or more containers.
- **Key Resources**
  - **Deployment** — declaratively manages Pods and ReplicaSets for rolling updates.
  - **Service** — a stable network endpoint that exposes Pods.
  - **Ingress** — rules for routing external HTTP(S) traffic in.
  - **ConfigMaps & Secrets** — inject configuration and sensitive data.
  - **Volumes** — persistent storage attached to Pods.

The mental model that sticks: you declare the state you want, and Kubernetes works continuously to make reality match it. Most of what feels like magic is just controllers reconciling desired versus actual.

## Terraform: infrastructure as code

Terraform lets you declare infrastructure — servers, databases, networks, cloud resources — in version-controlled files instead of clicking through a console. Its core concepts:

- **Terraform CLI** — the commands (`terraform init`, `plan`, `apply`) that read your configuration.
- **`.tf` files** — human-written HCL declaring resources and modules.
- **Providers** — plugins that let Terraform manage different platforms (AWS, Azure, Kubernetes, etc.).
- **Modules** — reusable, composable packages of `.tf` files to structure and share infrastructure.
- **State** — the `terraform.tfstate` file that tracks real-world resources and their attributes.
- **Backend** — where state lives (local, S3, Terraform Cloud) and how locking and remote operations are managed.
- **Resource Graph** — the internal dependency graph Terraform builds to order operations correctly.
- **Plan & Apply** — a two-step flow: `plan` previews changes, `apply` makes them live.

{{< protip >}}
The single most important Terraform discipline is treating the state file as production data. It's the source of truth for what actually exists, and a corrupted or locally-edited state is the fastest way to a very bad afternoon. Store it in a remote backend with locking from day one — never on a laptop, never in the repo.
{{< /protip >}}
