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
   ins: xxx
   name: xxx
   org: xxx
   street: xxx
   city: xxx
   code: xxx
   country: xxx
   email: email
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
- Multus
- calico
- Cillium
- Flannel
- WeaveNet
- Kube-router
- Antrea
- ...

## How K8s Relies in CNIs


# CNI Benchmarking
## Performance Metrics and Aspects
To evaluate the performance, there are several metrics that should be taken into account including network throughput, end-to-end latency, CPU and Memory utilization.  
### QoS
### QoE
### Deployment and Deletion Times
### CPU and Memory
...

## Interoperability and Scability
## Observability and Bottleneck Detection

# CODECO Experimentation Framework Methodology
## CODEF Overview and CNI Support
## Environment Configuration Aspects
## Measurement Tools


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
