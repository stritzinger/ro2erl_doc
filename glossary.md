# Glossary

This document provides definitions for technical terms used throughout the Target-X project documentation.

## Core Concepts

### Target-X Components

| Term | Definition |
|------|------------|
| **ro2erl_bridge** | The component deployed at robot sites that interfaces with the local ROS2/DDS network and communicates with the hub. |
| **ro2erl_hub** | The cloud-based component that coordinates connected bridges and provides the web interface for management. |
| **Dispatch Mode** | The initial implementation approach where messages are received from and passed to the rosie_rclerl client rather than directly interfacing with network traffic. |
| **Network Mode** | Future implementation where the bridge directly inspects and generates network traffic for ROS2/DDS/RTPS. |
| **Bandwidth Bucket** | The mechanism used to implement bandwidth limitations through a token bucket algorithm. |

### ROS2 and DDS Terms

| Term | Definition |
|------|------------|
| **ROS2** | Robot Operating System 2, an open-source robotics middleware framework. |
| **DDS** | Data Distribution Service, the underlying communication middleware used by ROS2. |
| **RTPS** | Real-Time Publish-Subscribe Protocol, the wire protocol used by many DDS implementations. |
| **Node** | In ROS2, a process that performs computation. Nodes communicate with each other through topics, services, and actions. |
| **Topic** | A named bus over which nodes exchange messages in ROS2. |
| **QoS** | Quality of Service, parameters that control the timing and reliability aspects of message delivery. |
| **Publisher** | A ROS2 node that sends messages on a topic. |
| **Subscriber** | A ROS2 node that receives messages from a topic. |
| **rosie_rclerl** | ROS Client Library in Erlang, used by Target-X to interface with ROS2 systems. |

### Erlang Terms

| Term | Definition |
|------|------------|
| **Erlang/OTP** | A programming language and set of libraries designed for building scalable and fault-tolerant applications. |
| **Erlang Distribution** | The built-in mechanism in Erlang for communication between Erlang nodes, leveraged by Target-X for bridge-hub communication. |
| **Node** | In Erlang, a running Erlang runtime system that has been given a name. |
| **Application** | In Erlang/OTP, a component that can be started/stopped as a unit within an Erlang node. |
| **rebar3** | The standard build tool for Erlang applications. |
| **OTP** | Open Telecom Platform, a set of libraries and design principles for building robust applications in Erlang. |

### Network and Security Terms

| Term | Definition |
|------|------------|
| **TLS** | Transport Layer Security, a cryptographic protocol used to secure communications. |
| **PKI** | Public Key Infrastructure, a set of roles, policies, and procedures for creating, managing, and using digital certificates. |
| **Secure Element** | A tamper-resistant hardware component that can securely host applications and store cryptographic data. |
| **Traffic Shaping** | The process of controlling network traffic to optimize performance and ensure certain traffic types receive priority. |
| **Bandwidth Limitation** | Restricting the amount of data that can be transmitted within a given time period. |
| **Erlang Cookie** | A shared secret used by Erlang nodes to authenticate with each other for distribution connections. |

### grisp.io Framework Terms

| Term | Definition |
|------|------------|
| **grisp_connect** | Component of the grisp.io framework that manages secure connections to cloud services. |
| **braid_node** | Component of the grisp.io framework used for deploying services in the cloud platform. |
| **Braid Service** | A service deployed using the grisp.io cloud platform infrastructure. |

## Acronyms

| Acronym | Expansion |
|---------|-----------|
| **ROS** | Robot Operating System |
| **DDS** | Data Distribution Service |
| **RTPS** | Real-Time Publish-Subscribe Protocol |
| **QoS** | Quality of Service |
| **TLS** | Transport Layer Security |
| **PKI** | Public Key Infrastructure |
| **WAN** | Wide Area Network |
| **LAN** | Local Area Network |
| **API** | Application Programming Interface |
| **UI** | User Interface |
| **SPA** | Single Page Application |

> **Note**: This glossary will be expanded as the project evolves and new terms are introduced. 