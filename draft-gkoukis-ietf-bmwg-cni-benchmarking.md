---
title: "TODO - Your title"
abbrev: "TODO - Abbreviation"
category: info

docname: draft-sofia-ietf-bmwg-cni-benchmarking-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: ""
workgroup: "IETF"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "IETF"
  type: ""
  mail: ""
  arch: ""
  github: "rute19104/ietf-bmwg-draft-2025-1"
  latest: "https://rute19104.github.io/ietf-bmwg-draft-2025-1/draft-samizadeh-ietf-bmwg-cni-benchmarking.html"

author:
  -
   ins: T. Samizadeh
   name: Tina Samizadeh
   org: fortiss GmbH
   street: Guerickestr. 25
   city: Munich
   code: 80805
   country: Germany
   email: samizadeh@fortiss.org
 -
   ins: R. C. Sofia
   name: Rute C. Sofia
   org: fortiss GmbH
   street: Guerickestr. 25
   city: Munich
   code: 80805
   country: Germany
   email: rs19104@gmail.com
  -
   ins: G. Koukis
   name: Georgios Koukis
   org: ATHENA RC
   street: University Campus South Entrance, Kimmeria
   city: Xanthi
   code: 67100
   country: Greece
   email: george.koukis@athenarc.gr
-

normative:
RFC2119:

informative:



--- abstract

This document provides an overview on approaches for Kubernetes CNI benchmarking within the context of Edge to Cloud deployments. The draft focuses on the performance of networking plugins used in container orchestration systems like Kubernetes, e.g., Multus, Calico, Cilium, Flannel, and explores an alignment towards software defined networking. The focus is on the proposal of formal methodologies that can support an adequate benchmarking of Kubernetes CNIs.


--- middle

# Introduction

TODO Introduction


# Conventions and Definitions

{::boilerplate bcp14-tagged}
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119 {{RFC2119}}.
In this document, these words will appear with that interpretation   only when in ALL CAPS. Lower case uses of these words are not to be    interpreted as carrying significance described in RFC 2119.

# Definitions

* Latency (aka bounded latency), concerns the end-to-end transmission delay between a transmitter and a receiver, when a traffic flow is triggered by an application. By definition, latency corresponds to the time interval between sending the first packet of a flow from a source to a destination, until the instant of reception of the last packet of that flow.

* Periodicity stands for whether or not the data transmission is executed in a periodic fashion and whenever possible, the specific periodicity per unit of time has been specified.

* "Transmit data size” corresponds to the data payload in bytes.

* “Time sync” refers to the need to ensure IEEE 1588 synchronization.

* "Node density" provides (wherever available) a glimpse into the number of end-nodes per 20mx20m.


# Fundamentals of CNI Benchmarking
The Container Network Interface (CNI) plays a key role in managing network connectivity between containers and enables the seamless integration of networking solutions within orchestration platforms such as Kubernetes.

## Overview and Relevance in the Context of Telco-Cloud Environments

## Container Network Interfaces in Kubernetes

## Relevancy of Kubernetes Networking in Telco-Cloud Environments

## Overview of Main CNIs
This section categorizes CNI models into four main categories based on their functional role and deployment scope:
**1) Primary and single interface–plugins:** This category includes plugins that create or move a single network interface into the pod’s network namespace and assign it an IP address via their built-in IPAM logic. Examples include bridge, macvlan, host-device and sr-iov. After the primary plugin completes, chained plugins (for example portmap, bandwidth, tuning, firewall, sbr and vrf) may execute sequentially to refine NAT rules, adjust interface parameters, or apply policies to the same interface.
**2) Standard Third-Party Plugins or Normal Non-Acceleration Networking Models [ietf-bmwg-04]:** This category includes the default container-networking setup, which relies on a single CNI plugin, typically installed by applying its YAML manifest. Examples include: 
• Antrea: builds on Open vSwitch (OVS) with optional eBPF layer, provides feature-rich OVS datapath; 
• Calico: uses iptables or nftables datapath by default, with an optional eBPF mode that replaces kube-proxy for higher performance, also offers rich policy and WireGuard/IP-in-IP/VXLAN encapsulation choices, focuses on scalability and rich features; 
• Cillium: uses pure eBPF/XDP datapath bypassing iptables and adding built-in L3/4/7 observability (with the Hubble ui), network policy and encryption; 
• Flannel: sets up default overlay VXLAN and is often paired with Calico (for policy enforcement) composing Canal, focuses on simplicity; 
• Kube-ovn: a cloud-native SDN, built on Open Virtual Network (OVN), supports logical switches, routers, and IPv6/dual-stack; 
• Kube-router: leverages the Linux kernel’s BGP and IPVS to provide pod routing, service proxy, and network policy, prioritizes lightness;
• WeaveNet: provides peer-to-peer mesh overlay with nodes auto-discovery and encrypts traffic, designed for ease of use in multi-cloud and edge scenarios (archived since June 2024).
**3) Multi-network Meta-plugins or Acceleration Networking Models [ietf-bmwg-04]:** This category includes models that combine multiple CNI plugins and use at least two network interfaces. A standard CNI sets up the primary interface, managing IP address allocation and traffic, while additional CNIs attach secondary interfaces. Examples include: 
• Multus: the de-facto meta-plugin, allows for the attachment of multiple network interfaces to a pod, interpreting one or more NetworkAttachmentDefinition CRDs, extending the default networking model to support multi-homed pods, enables advanced topology management and network flexibility; 
• Kaktus: forked from Multus, adds a deterministic device-naming scheme (“net-<hash>”) so each secondary NIC maps to its NetworkAttachment, aims at high-performance fabrics where interface order can change; 
• DANM: bundles its own CRDs, IPAM, and policy controllers to integrate with Nokia’s 5G/edge telco stack and to provision SR-IOV, IPvlan, or overlay networks with SLA awareness; 
• CNI-Genie: let pods choose one of several third-party CNIs at deploy time (archived since March 2025).
**4) Cloud-specific:** This category includes models that connect K8s networking directly into a Virtual Private Cloud (VPC) of the cloud provider, exposing the cloud capabilities inside the cluster. Examples include: Terway (Alibaba), Contrail/Tungsten Fabric, Contiv VPP (Cisco-led), ACI CNI (Cisco), AWS VPC CNI, Azure CNI, OCI VCN-Native CNI (Oracle) and OVN-Kubernetes overlays.

## How K8s Relies on CNIs
The CNI is a set of specifications and libraries that defines how container runtimes should configure network interfaces for containers and manage their connectivity. CNIs are essential components in K8s to implement the K8s network model [K8s-netw-model], providing a standardized and container runtime agnostic way to configure network interfaces within containers and networks. In practice, they function as the intermediary layer that connects the K8s control plane to the cluster’s underlying network infrastructure. From a networking perspective, since a pod or container lacks network connectivity when first created, K8s relies on a CNI plugin to:
- attach a virtual network interface (e.g., a veth) inside the container’s namespace,
- link that interface to the host’s networking stack,
- assign an IP address,
- set up network policies for isolation and
- install the appropriate routing information according to the cluster’s IPAM strategy.

CNIs enable seamless communication between pods in the cluster using pod IP addresses without NAT, with external networks and the outside world. Based on the way each implementation uses underlying technologies and routing (overlay/underlay), enforces policies (layer 3/4/7), and interacts with the kernel (netfilter, eBPF), CNIs can be grouped into distinct networking models as shown in Section 6.4. These design choices SHOULD be considered in CNI performance benchmarking across varied workloads and deployment scenarios, while they SHOULD be evaluated within the context of the full containerized infrastructure to reflect real-world behavior.

# CNI Benchmarking
While several performance-benchmarking suites are already available from CNI providers [cilium-bench], the open-source community [TNSM21-cni], [GLOBECOM21-bench-k8s], or IETF groups [ietf-bmwg-04], a comprehensive CNI evaluation SHOULD incorporate relevant performance metrics, scalability aspects and identify bottlenecks. Each experiment SHOULD be run at least five times with an adequate CPU cooling time to ensure statistical accuracy. Metrics SHOULD include the minimum, median, maximum, and 50th-, 90th- and 99th-percentile results. The testbed’s hardware, software versions, and topology MUST be documented to guarantee reproducibility.

## Performance Metrics and Aspects
Considering the architecture of microservice-based applications, microservices may interact with each other and external services. Having containerized applications and orchestration platforms like Kubernetes, there is a continuous need to address communication and networking as Kubernetes doesn't handle networking itself. Moreover, communication between containers is extremely important to meet QoS requirements of applications. To evaluate the performance of CNIs there are several metrics that should be taken into account including network throughput, end-to-end latency, Pod startup, CPU and Memory utilization, and packet delays and losses.
### QoS
Measuring the QoS that mostly focuses on traditional performance metrics like latency and handling various network configurations. However, a benchmark methodology SHOULD consider multiple transport protocols (TCP, UDP) since plugins often behave very differently under varied traffic flows in terms of performance or resource footprint. TCP_RR measures how fast requests and responses are exchanged over a single TCP connection. TCP_CRR tests how quickly new TCP connections can be opened, used, and closed. Similarly, UDP_RR measures RTT request/response to capture latency and jitter under message exchanges. For every protocol, the benchmark SHOULD consider a representative set of packet sizes (e.g., 64B, 512B, 1500B) and jumbo MTU (if it exists) so that fragmentation penalties such as jitter and throughput disparities become visible.
### QoE
Quality of Experience (QoE) benchmarking for evaluating CNI plugins is beyond measuring the performance metrics, it is more focused on evaluating how effectively a CNI can enhance the overall user experience. For instance, time to deploy or configure the CNI, ease of troubleshooting, and impact of the CNI on application performance. 
The integration of CNIs with K8s package managers like Helm makes installations, upgrades and rollbacks near zero-touch, minimizing the total installation time and manual intervention needed. In addition, CNI-specific CLIs wrap the traditional kubectl commands, accelerating troubleshooting and flattening the learning curve for newcomers. Examples like the “cilium” CLI and “calicoctl” provide one-command installs, live status summaries and diagnostics that would otherwise require multiple kubectl invocations and manual log scraping. For example, “cilium status –verbose” exposes IPAM state, health and statistics, while “calicoctl node diags” gathers a full support bundle for analysis. K8s distributions such as k3s, k0s, and MicroK8s adopt the same philosophy, including their own built CLI that streamlines cluster installation and cluster life cycle, with MicroK8s packaging popular add-ons as snaps, allowing them to be enabled with a single command.
### Deployment and Deletion Times
Deployment and deletion times are key metrics within the cluster lifecycle, capturing the control-plane overhead that a CNI introduces. At pod level they measure how long the kubelet needs to create a new network stack (CNI ADD) or tear it down (CNI DEL), while at cluster level they show how quickly a CNI DaemonSet can roll during upgrades. Therefore, a benchmarking methodology SHOULD quantify both operations with clearly defined metrics.
### CPU and Memory
The CPU and memory footprint of a CNI have significant practical consequences. It determines how many pods can co-locate on each node, while on resource-constrained edge or IoT devices (Raspberry Pi) can affect the operational functionality of the device, shortening battery life. The underlying architecture of the plugin is highly influential. Traditionally, the richer the feature set and the more complex the data-plane (encryption, eBPF, overlay tunnels), the higher the resource footprint of the CNI. For instance, when packet processing happens in user space, each packet must cross the kernel several times and be copied, so those daemons consume noticeably more CPU cycles than in-kernel eBPF programs. Similarly, CNIs that allocate external resources (e.g., ENIs) often carry a memory overhead for cloud-API caches. However, performance disparities are also observed under varying workloads. A CNI that excels in high-throughput TCP bulk transfers may underperform in high-rate UDP bursts, stress testing or policy-intensive operations. Therefore, a robust benchmark SHOULD profile every CNI in at least three traffic states: idle, low-traffic and high-load to reveal its actual resource requirements, its limitations when it scales up and its performance cliffs, both in node level (e.g., with Prometheus) or container/pod level (e.g., with cAdvisor).
## Interoperability and Scalability
To complement a comprehensive benchmarking, stress-test phases SHOULD be included to expose the point at which a CNI reaches its scalability ceiling and to verify that observability remains intact under extreme load. Such experiments SHOULD scale multiple processes, including concurrent pods, pod-creation bursts, service and network policies, deliberate CPU/RAM/Disk saturation and injected network latency or packet loss to observe how quickly the CNI degrades and how long it takes to recover. These tests can be orchestrated with chaos engineering tools, complemented by load generators like Kube-burner and network traffic tools such as iperf3 or k6.
## Observability and Bottleneck Detection

# CODECO Experimentation Framework Overview and Methodology
The CODECO Experimentation Framework (CODEF) is a novel open-source solution designed under the CODECO project to accelerate the experimentation of containerized edge-cloud deployments. CODEF follows a microservice-based architecture introducing advanced abstractions aimed at reducing experimentation complexity, increasing automation, and improving reliability.
CODEF’s architecture is composed of four abstraction layers that manage the entire experimental workflow, each implemented as an independent microservice/container. This design allows the framework to integrate additional technologies and features ensuring modularity and extensibility. The Infrastructure Managers allocate cluster nodes over heterogeneous edge-cloud systems, including bare-metal physical nodes, hypervisor-based virtual machines (VirtualBox, XCP-ng), and cloud testbeds (AWS, CloudLab, EdgeNet). The Resource Managers oversee the automated deployment of software and applications on allocated nodes based on custom Ansible playbook templates. Once cluster nodes are allocated, a dedicated Resource Manager is deployed for each node to ensure software deployment and management. The Experiment Controller automates experiments’ execution by managing experiment iterations, metrics collection and result generation, while the Results Processor offers statistical analysis, post-processing and report generation.
CODEF offers a holistic and declarative approach with complete experimentation automation, from cluster deployment to experiment execution and results processing. The instantiation of cluster nodes from scratch based on OS images guarantees that every cluster is built from a known baseline, therefore each experiment is conducted in a clean environment ensuring no remnants of previous runs remain. This also ensures applicability, with all clusters being identical. Low-level parameterization is also available for various networking and security options like tunneling and encapsulation mechanisms (VXLAN, Geneve IP-in-IP), encryption schemes (IPsec, WireGuard) or kernel-based options (eBPF/XDP data paths).
## CODEF Benchmarking and CNI Support
CODEF addresses the requirements for repeatable, infrastructure-agnostic benchmarking across the edge-cloud continuum. It supports a wide range of third-party CNI plugins like Antrea, Calico, Cilium, Flannel, Weavenet, Kube-router, Kube-ovn, Multus, and out-of-the-box network solutions like L2S-M [L2S-M]. These CNIs can be evaluated within multiple Kubernetes distributions (e.g., vanilla K8s, K3s, K0s, MicroK8s). Since each plugin employs distinct network layer options (underlay and/or overlay) and exposes different features such as enhanced security or encryption mechanisms, programmable data-paths, or network-policy enforcement, the resulting performance characteristics may vary. CODEF therefore provides a consistent end-to-end methodology for comparing these alternatives under diverse test conditions and across heterogeneous infrastructures and system architectures. Accordingly, any benchmarking methodology SHOULD account for the same dimensions to ensure comparable results.
## Environment Configuration Aspects
Besides the distinct characteristics of each CNI implementation, a benchmarking methodology SHOULD also account for architectural and physical variables: 
- VM/hypervisor or bare-metal deployments,
- intra- or inter-node scenarios, and
- deployments across vanilla or production-grade K8s distributions compared to those designed specifically for the edge.

Hardware diversity also, adds another layer of complexity - CPU architecture, core and thread counts, cache hierarchy, memory type and speed, NUMA layout, NIC models and firmware - and low-level settings such as tunnelling mode, MTU, or eBPF tweaks that depart from their defaults can all alter performance outcomes.  Experiments conducted with CODEF under a variety of conditions i.e., different intra- and inter-cluster scenarios with different hardware specs, across various K8s distributions, employing different low-level tunneling parameters or when applying high load/stress and workloads showed significant performance variability. Disparities observed not only among different CNI plugins on the same distribution, but also within a single plugin when ported to different K8s distributions. Some key observations showed that deploying lightweight plugins on edge-focused K8s distributions does not automatically translate into better resource efficiency or higher performance. In practice, some plugins downscale their footprint with cost of performance e.g., by employing a different tunneling protocol, while others incur additional overhead and better network performance on vanilla K8s, a trade-off that should be considered. In addition, the optimal combination of plugin and distribution is highly workload-specific. A particular workload may achieve its best results with a configuration that is, on paper, more resource-intensive because it matches the workload’s operational profile. These trade-offs SHOULD be considered, especially in IoT and edge environments, where resource limitations make it imperative to extract maximum performance.
## Measurement Tools
Through Ansible playbooks, CODEF automates the installation of a range of software and tools for both workload generation and measurement. It supports network-centric traffic generators such as iperf3, netperf, and sockperf, as well as comprehensive suites like the K8s-bench-suite, allowing to measure bandwidth, throughput, and packet fragmentation behavior across TCP and UDP protocols and message sizes. Resource footprint is captured at node or container scope for CPU, memory, and disk, with observability stacks built on Prometheus and Grafana to provide live monitoring and historical dashboards. Load and stress tests such as the CNCF Kube-Burner alongside chaos-engineering tools can subject the cluster to scaled pressure and orchestrated fault scenarios revealing performance limits and resilience gaps. Power consumption can also be estimated through empirical models or specialized tools such as CNCF Kepler and Scamander, however, their accuracy SHOULD be considered as it ultimately depends on the underlying hardware counters (e.g., bare-metal, RAPL) and their prediction models especially on virtualized or non-Intel systems.

# Best Practices
## Test Setup and Configuration for Telco-Cloud
### Pod-to-Pod
### Ingress and Egress Traffic
### ...
## Scalability, Fault Tolerance, Secure Connectivity Considerations
## Integration Consideration
## Result Generation
### Standardised Output Formats
### Reproducibility Guidelines
### Comparative Analysis Guidelines
# Challenges and Future Directions

 

# Security Considerations

This document describes formal methodologies to support a solid, replicable and scalable analysis of Kubernetes CNIs for heterogeneous networking environments.
TODO ...


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}
The research leading to this draft has been developed in the context of the Horizon Europe project Cognitive Decentralised Edge Cloud Orchestration (CODECO). the CODECO project has received funding from the European Commission programme Horizon Europe, under grant agreement number: 101092696.


# Reference

[K8s-netw-model] https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/ 

[L2S-M] L. F. Gonzalez, I. Vidal, F. Valera and D. R. Lopez, "Link Layer Connectivity as a Service for Ad-Hoc Microservice Platforms," in IEEE Network, vol. 36, no. 1, pp. 10-17, 2022, doi: 10.1109/MNET.001.2100363

[ietf-bmwg-04] draft-ietf-bmwg-containerized-infra-04

[cilium21-bench] https://cilium.io/blog/2021/05/11/cni-benchmark 

[GLOBECOM21-bench-k8s] S. K. N. Rao, F. Paganelli and A. Morton, "Benchmarking Kubernetes Container-Networking for Telco Usecases," 2021 IEEE Global Communications Conference (GLOBECOM), Madrid, Spain, 2021, pp. 1-7, doi: 10.1109/GLOBECOM46510.2021.9685803.

[TNSM21-cni] S. Qi, S. G. Kulkarni and K. K. Ramakrishnan, "Assessing Container Network Interface Plugins: Functionality, Performance, and Scalability," in IEEE Transactions on Network and Service Management, vol. 18, no. 1, pp. 656-671, March 2021, doi: 10.1109/TNSM.2020.3047545.
