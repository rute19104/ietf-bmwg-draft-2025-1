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
   ins: George Koukis
   name: George Koukis
   org: Athena
   street: xxx
   city: xxx
   code: xxx
   country: xxx
   email: George.Koukis@athenarc.gr
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

## Container Network Interfaces in Kubernets

## Relevancy of Kubernetes Networking in Telco-Cloud Environments

## Overview of Main CNIs
This section categorizes CNI models based on four main categories, the: i) Main and single interface–plugins; ii) Standard 3rd party plugins or normal non-acceleration networking models; iii) Multi-network meta-plugins or acceleration networking models; and iv) Cloud-specific.
i) Main and single interface–plugins: Create or move an interface into the pod and run IPAM, including the bridge, macvlan, host-device, sr-iov, as well as the portmap, bandwidth, tuning, firewall, sbr, vrf which run after the main plugin and tweak that one interface.
ii) Standard 3rd party plugins or Normal Non-Acceleration Networking Models: the default container-networking setup that relies on a single CNI plugin, typically installed by applying its YAML manifest.
    • Antrea: 
    • Calico: Can enable eBPF to swap iptables for high-performance eBPF datapath.
    • Cillium (eBPF)1: In the case of the eBPF/XDP offloading container network, Cilium CNI with its eBPF offloading feature.
    • Flannel: Focuses on simplicity, creating VXLAN and often paired with Calico (for policy enforcement) composing Canal.
    • Kube-ovn: OVN-based SDN 
    • Kube-router: BGP + IPVS; lightweight
    • WeaveNet:
iii) Multi-network meta-plugins or Acceleration Networking Models: combine multiple CNI plugins, utilizing at least two network interfaces. A standard CNI sets up the primary interface, managing IP address allocation and traffic, while additional CNIs attach secondary interfaces.
    • Multus: allows for the attachment of multiple network interfaces to a pod extending the default cluster networking model to support multi-homed pods. It achieves this by interpreting one or more NetworkAttachmentDefinition CRD referenced by a pod, enabling advanced topology management and network flexibility.
    • Kaktus2: uses the Multus code-base but adds deterministic NIC device naming.
    • DANM: integration with Nokia’s Telco stack 
    • CNI-Genie: archived since March 2025.
iv) Cloud-specific
    • Terway: 
    • VPC: 
    • Contrail:
    • Contiv: 
    • ACI: 

## How K8s Relies in CNIs
The CNI is a set of specifications and libraries that defines how container runtimes should configure network interfaces for containers and manage their connectivity. In these lines, CNIs are essential components in K8s to implement the K8s network model1, providing a standardized and container runtime agnostic way to configure network interfaces within containers and networks. In practice, they function as the intermediary layer that connects the K8s control plane to the cluster’s underlying network infrastructure. From a networking perspective, since a pod or container lacks network connectivity when first created, K8s relies on a CNI plugin to attach a virtual network interface inside the container’s namespace, link that interface to the host’s networking stack, assign an IP address, set up network policies for isolation and install the appropriate routing information according to the cluster’s IPAM strategy. Therefore, it enables seamless communication between pods in the cluster using pod IP addresses without NAT, with external networks and the outside world. Based on the way each implementation uses underlying technologies and routing (overlay/underlay), enforces policies (layer 3/4/7), and interacts with the kernel (eBPF), CNIs can be grouped into distinct networking models as shown in Section 4.4. These design choices affect the benchmarking performance of the CNIs in different scenarios and workloads, as well as the complete containerized infrastructure.

# CNI Benchmarking
## Performance Metrics and Aspects
Considering the architecture of microservice-based applications, microservices may interact with each outer and external services. Having containerized applications and orhestration platforms like Kubernets, there is a need to address communiation and networking as Kubernetes doesn't handle networking itself. Moreover, communication between containers is extremly important to meet QoS requirements of application.
To evaluate the performance of CNIs there are several metrics that should be taken into account including network throughput, end-to-end latency, Pod startup, CPU and Memory utilization, and packet delays and losses. 
### QoS
Measuring the QoS that mostly focuses on traditional performance metrics like latency and handling various network configurations.
TCP_RR measures how fast requests and responses are exchanged over a single TCP connection.
TCP_CRR tests how quickly new TCP connections can be opened, used, and closed.
### QoE
Quality of Experience (QoE) benchmarking for evaluating CNI plugins is beyond measing the performance metrics, It is more focused on evaluating that how effectively a CNI can enhance the overall user experience. For instance, time to deploy or configure the CNI, ease of troubleshooting, and impact of the CNI on application performance.
### Deployment and Deletion Times
### CPU and Memory
...

## Interoperability and Scability
## Observability and Bottleneck Detection

# CODECO Experimentation Framework Methodology
The CODECO Experimentation Framework (CODEF) is a novel open-source solution designed under the CODECO project to accelerate the experimentation of containerized edge-cloud deployments. CODEF follows a micro-service philosophy and architecture introducing advanced abstractions aimed at reducing experimentation complexity, increasing automation, and improving reliability.
CODEF’s architecture is composed of four abstraction layers that collectively span the entire experimental workflow, each implemented as an independent microservice/container. This design allows the framework to integrate additional technologies and features ensuring modularity and extensibility. The Infrastructure Managers allocate cluster nodes over heterogeneous edge-cloud systems, including bare-metal physical nodes, hypervisor-based virtual machines (VirtualBox, XCP-ng), and cloud testbeds (AWS, CloudLab, EdgeNet). The Resource Managers oversee the automated deployment of software and applications on allocated nodes based on custom Ansible playbook templates. Once cluster nodes are allocated, a dedicated Resource Manager is deployed for each node to ensure efficient software deployment and management. The Experiment Controller automates experiments’ execution by managing experiment iterations, metrics collection and result generation, while the Results Processor offers statistical analysis, post-processing and report generation.
CODEF offers a holistic and declarative approach with complete experimentation automation, from cluster deployment to experiment execution and results processing. The instantiation of cluster nodes from scratch based on OS images guarantees that every cluster is built from a known baseline, therefore each experiment is conducted in a clean environment ensuring no remnants of previous runs remain. This also ensures replicability of experiments with all clusters being identical. Low level parameterization is also available for various networking and security options like tunneling and encapsulation mechanisms such as VXLAN, Geneve or IP-in-IP, encryption schemes including IPsec and WireGuard or kernel-based options like eBPF/XDP data paths.
## CODEF Overview and CNI Support
CODEF addresses the requirements for repeatable, infrastructure-agnostic benchmarking across the edge-cloud continuum. It supports a wide range of 3rd party CNI plugins like Antrea, Calico, Cilium, Flannel, Weavenet, Kube-router, Kube-ovn and Multus as well as out-of-the-box network solutions like L2S-M [1]. These CNIs can be evaluated within multiple Kubernetes distributions (e.g., vanilla K8s, K3s, K0s, MicroK8s). Since each plugin employs distinct network layer options (underlay and/or overlay) and exposes different feature sets such as enhanced security or encryption mechanisms, programmable data-paths, and granular network-policy enforcement, the resulting performance characteristics may vary. CODEF therefore provides a consistent end-to-end methodology for comparing these alternatives under equivalent test conditions. 
## Environment Configuration Aspects
Besides the distinct characteristics of each CNI implementation, a benchmarking methodology must also account for architectural and physical variables such as VM/hypervisor or bare-metal deployments, intra- or inter-node scenarios, and the differences between vanilla or production-grade K8s distributions compared to those designed specifically for the edge. Hardware diversity also, adds another layer of complexity - CPU architecture, core and thread counts, cache hierarchy, memory type and speed, NUMA layout, NIC models and firmware - and low-level settings such as tunnelling mode, MTU, or eBPF tweaks that depart from their defaults can all alter performance outcomes. Experiments conducted with CODEF under a variety of conditions i.e., different intra- and inter-cluster scenarios with different hardware specs [], across various K8s distributions, employing different low-level tunneling parameters [] or when applying high load/stress and workloads [] showed significant performance variability. Disparities observed not only among across different CNI plugins on the same distribution, but also within a single plugin when ported to different K8s distributions. Some key observations showed that deploying lightweight plugins on edge-focused K8s distributions does not automatically translate into better resource efficiency or higher performance. In practice, some plugins downscale their footprint with cost of performance e.g., by employing a different tunneling protocol, while others incur additional overhead and better network performance on vanilla K8s, a trade-off that should be considered. In addition, the optimal combination of plugin and distribution is highly workload-specific. A particular workload may achieve its best results with a configuration that is, on paper, more resource-intensive because it matches the workload’s operational profile. These trade-offs become even more critical in IoT and edge environments, where resource limitations make it imperative to extract maximum performance.
## Measurement Tools
Through Ansible playbooks, CODEF automates the installation of a range of software and tools for both workload generation and measurement. It supports network-centric traffic generators such as iperf3, netperf, and sockperf, as well as comprehensive suites like the K8s-bench-suite1, allowing to measure bandwidth, throughput, and packet fragmentation behavior across TCP and UDP protocols and message sizes. Resource footprint is captured at node or container scope for CPU, memory, and disk, with observability stacks built on Prometheus and Grafana to provide live monitoring and historical dashboards. Load and stress tests such as the CNCF Kube-Burner alongside chaos-engineering tools can subject the cluster to scaled pressure and orchestrated fault scenarios revealing performance limits and resilience gaps. Power consumption can also be estimated through empirical models or specialized tools such as CNCF Kepler and Scamander, however, their accuracy ultimately depends on the underlying hardware counters (e.g., bare-metal, RAPL) and their prediction models especially on virtualized or non-Intel systems.

...

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
[1] L. F. Gonzalez, I. Vidal, F. Valera and D. R. Lopez, "Link Layer Connectivity as a Service for Ad-Hoc Microservice Platforms," in IEEE Network, vol. 36, no. 1, pp. 10-17, 2022, doi: 10.1109/MNET.001.2100363
