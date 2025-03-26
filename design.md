# ROS2 Enhanced Reliability Layer (Target-X)

## Overview
Target-X is a system designed to provide secure and reliable connectivity between ROS2/DDS/RTPS services across different sites through a cloud-based hub. It focuses on reliability, efficiency, and security, particularly for challenging network conditions such as low bandwidth, high latency, or intermittent connections.

## Problem Statement

### Limited WAN Connectivity for ROS2
ROS2 and DDS are primarily designed for robust local network communication. While they provide excellent performance and reliability within contained environments, they face significant challenges when deployed across Wide Area Networks (WANs), including:

- **Bandwidth Limitations**: ROS2/DDS traffic can be bandwidth-intensive, making it unsuitable for constrained WAN connections.
- **Latency Sensitivity**: Many DDS quality of service settings assume low-latency networks, leading to degraded performance over high-latency connections.
- **Intermittent Connectivity**: DDS discovery and communication mechanisms can fail or behave sub-optimally when network connections are intermittent.
- **Security Concerns**: Exposing DDS traffic directly to public networks introduces significant security vulnerabilities.

### Cross-Site Communication Challenges
Organizations deploying robotic systems across multiple sites face substantial obstacles:

- **Inconsistent Network Conditions**: Different deployment sites often have varying network capabilities and limitations.
- **Data Synchronization**: Maintaining consistent data across distributed robotic systems requires reliable message delivery mechanisms.
- **Centralized Management**: Monitoring and controlling distributed robotic fleets requires secure and reliable communication channels.
- **Infrastructure Complexity**: Building custom solutions for each deployment increases system complexity and maintenance burden.

### Traffic Management Requirements
Robotic systems operating in constrained network environments require sophisticated traffic handling:

- **Prioritization**: Critical control messages must take precedence over less time-sensitive data.
- **Bandwidth Allocation**: Available bandwidth needs to be intelligently allocated among competing data streams.
- **QoS Translation**: DDS Quality of Service parameters may need adaptation when traversing different network segments.
- **Adaptive Behavior**: Communication patterns should dynamically adjust to changing network conditions.

## Solution Approach

Target-X addresses these challenges through a layered architecture that intelligently bridges ROS2/DDS communication across diverse network environments:

### Bridge-Hub Architecture
The system employs a bridge-hub model where:

- **Local Bridges** operate at each site, interfacing with the local ROS2/DDS network.
- **Central Hub** provides secure interconnection between bridges and centralized traffic management.
- **Erlang Distribution** provides the secure, reliable communication channel between bridges and the hub.

### Adaptive Traffic Shaping
Target-X implements intelligent traffic management to optimize communication over constrained networks:

- **Topic-Based Policies**: Different ROS2 topics can be assigned specific bandwidth allocation and priority levels.
- **Bandwidth Buckets**: Token bucket algorithms ensure fair distribution of available bandwidth.
- **QoS Awareness**: Traffic shaping respects and adapts to the QoS requirements of different message types.
- **Metrics-Driven**: Traffic policies can adapt based on observed network performance and application needs.

### Security by Design
The system leverages established security infrastructure:

- **TLS Encryption**: All bridge-hub communication is encrypted using industry-standard TLS.
- **Certificate-Based Authentication**: The grisp.io framework provides robust certificate management.
- **Secure Elements**: Hardware-backed security enhances authentication on client devices.
- **Delegation Pattern**: By leveraging the grisp.io security framework, Target-X benefits from ongoing security improvements and best practices.

### Operational Simplicity
Despite addressing complex technical challenges, Target-X prioritizes operational simplicity:

- **Minimal Configuration**: Sensible defaults and automatic discovery reduce configuration complexity.
- **Web Interface**: A centralized interface provides visibility and control across the distributed system.
- **Metric Collection**: Comprehensive metrics enable operators to understand and optimize system performance.
- **Independent Components**: The modular design allows for flexible deployment and independent scaling.

### Communication Strategy
The Target-X system leverages Erlang distribution as the core mechanism for communication between Bridge components and the Hub. This architectural decision provides several key benefits:

#### Security
- Communication is secured through TLS provided by the grisp.io framework
- The grisp.io platform manages the PKI infrastructure for all cloud nodes
- Client certificates are securely distributed to clients via authenticated connections
- Authentication is strengthened by secure elements on client devices

#### Connection Management
- Security and connection management are delegated outside the Target-X project
- The grisp.io framework handles establishing and maintaining connections over the internet
- This separation of concerns allows Target-X to focus on its core functionality
- The system benefits from the mature, battle-tested Erlang distribution protocol

#### Bridge-Hub API
The communication between Bridge and Hub components follows a well-defined API:

##### Bridge to Hub Messages
1. **Attach**
   ```erlang
   gen_statem:cast(HubPid, {bridge_attach, BridgeId :: binary(), BridgePid :: pid()})
   ```
   - Sent by bridge to register with the hub
   - Hub responds by monitoring the bridge and storing its information
   - Bridge should send this message after discovering the hub

2. **Detach**
   ```erlang
   gen_statem:cast(HubPid, {bridge_detach, BridgePid :: pid()})
   ```
   - Sent by bridge to unregister from the hub
   - Hub responds by removing the bridge and cleaning up monitoring
   - Bridge should send this message before shutting down

3. **Dispatch**
   ```erlang
   gen_statem:cast(HubPid, {bridge_dispatch, BridgePid :: pid(), Timestamp :: integer(), Message :: term()})
   ```
   - Sent by bridge to forward a message to other bridges
   - Hub forwards the message to all connected bridges except the sender
   - Bridge should include current timestamp in milliseconds

##### Hub to Bridge Messages
1. **Dispatch**
   ```erlang
   gen_statem:cast(BridgePid, {hub_dispatch, Timestamp :: integer(), Message :: term()})
   ```
   - Sent by hub to forward messages from other bridges
   - Bridge should process the message and forward it to its network
   - Timestamp indicates when the original message was sent

#### Reliability
- Erlang distribution provides built-in reliability mechanisms
- Message delivery guarantees help maintain system integrity
- The framework handles reconnection scenarios automatically
- Network issues are abstracted away from application logic

### grisp.io Integration

The Target-X system is designed to leverage the grisp.io platform for its initial implementation, providing a robust foundation for secure and reliable distributed communication:

#### Platform Benefits
- **Secure Distribution**: Built-in TLS encryption and certificate management
- **Node Management**: Automated node discovery and connection handling
- **Authentication**: Integrated authentication mechanisms
- **Infrastructure**: Managed cloud infrastructure for the hub component

#### Integration Points
- **Bridge Component**:
  - Runs as a client node in the grisp.io cluster
  - Leverages grisp.io's secure distribution for hub communication
  - Benefits from automatic reconnection handling
  - Uses platform-provided certificate management

- **Hub Component**:
  - Deploys as a braid node in the grisp.io cloud
  - Integrates with platform's node management
  - Uses platform's authentication system
  - Leverages platform's monitoring capabilities

#### Deployment Workflow
1. **Cluster Setup**:
   - Create a grisp.io cluster
   - Configure client nodes for bridges
   - Deploy hub as a braid node
   - Set up network access policies

2. **Security Configuration**:
   - Platform handles certificate generation
   - Manages secure distribution setup
   - Configures authentication tokens
   - Establishes secure communication channels

3. **Operation**:
   - Platform monitors node health
   - Handles automatic reconnection
   - Manages certificate rotation
   - Provides operational metrics

#### Future Considerations
While the initial implementation relies on grisp.io, the architecture allows for:
- Alternative deployment options
- Custom security implementations
- Different authentication mechanisms
- Independent infrastructure deployment

## Value to the Ecosystem

The Target-X project addresses several technical gaps in the current ROS2/DDS ecosystem:

### Network Resilience
- Extends ROS2's communication capabilities beyond local networks
- Provides mechanisms to maintain service continuity despite unreliable connections
- Implements intelligent traffic management to optimize limited bandwidth resources

### Cross-Environment Connectivity
- Enables seamless integration between robot systems in diverse deployment environments
- Facilitates data exchange between edge devices and cloud services
- Supports heterogeneous system topologies while maintaining a consistent interface

### Operational Optimizations
- Offers fine-grained traffic prioritization based on application requirements
- Provides metrics and insights for network performance and usage patterns
- Enables efficient bandwidth utilization through configurable traffic shaping policies

### Technical Use Cases
- Multi-site robotic fleets requiring centralized coordination
- Edge-deployed robots operating in bandwidth-constrained environments
- Systems with mixed real-time and non-real-time communication needs
- Applications requiring secure tunneling of ROS2 traffic across public networks

## Architecture

The system consists of two independent Erlang applications:

### Bridge (ro2erl_bridge)
The Bridge component operates on the local network and serves as the entry/exit point for ROS2/DDS/RTPS traffic.

#### Key Features
- Detects and monitors Hub node availability within its Erlang cluster
- Provides message dispatch functionality
- Applies message filtering based on Hub-provided rules
- Supports attachment to multiple Hub nodes simultaneously
- Forwards messages to all connected Hubs
- Future capabilities:
  - Network traffic inspection for ROS2/DDS/RTPS packets
  - Local network traffic generation

#### Connection Management
- The Bridge does NOT establish or maintain the low-level Erlang distribution connection to the Hub
- The Bridge implements a higher-level protocol for hub attachment and detachment
- Low-level connection/reconnection is delegated to the parent application using ro2erl_bridge as a dependency
- For grisp.io framework users, this is handled by grisp_connect
- ro2erl_bridge does not depend on grisp_connect, making it usable by any Erlang application

#### Operation Modes
1. **Dispatch Mode** (Initial Implementation)
   - Receives parsed messages from rosie_rclerl client
   - Dispatches messages as if they originated from the local network
   - Configurable callback for receiving messages from the Hub
   - Forwards messages to all attached Hubs

2. **Network Mode** (Future Implementation)
   - Listens to local network traffic
   - Inspects and forwards ROS2/DDS/RTPS packets
   - Generates traffic on the local network for non-Erlang systems

### Hub (ro2erl_hub)
The Hub component runs as a Braid service in the grisp.io cloud platform, packaged as a Docker container.

#### Key Features
- Manages connected Bridge clients
- Monitors client node availability
- Handles traffic statistics and metrics
- Manages and distributes traffic shaping rules
- Provides web interface for monitoring and configuration

#### Connection Management
- The Hub does NOT manage Erlang distribution connections to Bridge clients
- Connection management is handled by the grisp.io framework through braid_node
- The Hub depends directly on braid_node as it's a self-contained application for the grisp.io cloud platform
- Packaged as a Docker container using rebar3_docker plugin

### Traffic Flow
1. Message originates from application or local network
2. Bridge processes message:
   - Updates metrics/statistics
   - Applies filter rules
   - Updates filtering outcome metrics
3. Bridge forwards message to all attached Hubs
4. Each Hub distributes message to its connected Bridges
5. Receiving Bridges process message:
   - Update statistics/metrics
   - Call configured dispatch callback
   - (Future) Encode and send to local network

### Metrics and Statistics
- Bridges maintain local metrics
- Periodic batch updates to Hub
- Metrics persistence during disconnection
- Traffic shaping based on collected metrics

## Supported Platforms

The Target-X system is designed to be flexible in its deployment options, with different components supporting various platforms based on their specific roles:

### Bridge Component (ro2erl_bridge)
The bridge component is designed to run on various platforms:

- **Primary Platforms**:
  - **Linux Distributions**: Fully supported out of the box thanks to Erlang's cross-platform capabilities
    - Suitable for standard deployments
    - Compatible with common Linux distributions
    - Production-ready environment

  - **RTEMS via grisp board**:
    - Bare-metal Erlang runtime
    - Optimized for embedded deployment
    - Leverages grisp.io framework for secure connectivity
    - Ideal for resource-constrained environments

- **Future Platform Support**:
  - Other RTOS options (subject to Erlang/OTP compatibility)

### Hub Component (ro2erl_hub)
The hub component is designed for cloud deployment with the following considerations:

- **Primary Platform**: Alpine Linux
  - Lightweight container base
  - Optimized for cloud deployment
  - Minimal attack surface
  - Efficient resource utilization

- **Containerization**:
  - Docker-based deployment
  - Container orchestration support
  - Scalable architecture

### Platform Selection Rationale
The choice of platforms is driven by several key factors:

- **Security**: Minimizing attack surfaces and leveraging secure frameworks
- **Resource Efficiency**: Optimizing for constrained environments
- **Deployment Flexibility**: Supporting various deployment scenarios
- **Maintenance**: Leveraging well-maintained and supported platforms

## First Implementation Milestone

### Features
- Basic Bridge-Hub connectivity
- Message dispatch functionality
- Topic-based bandwidth limitation rules
- Basic statistics collection
- Web interface for:
  - Active DDS topics listing
  - Basic statistics display
  - Bandwidth limitation rule management
  - Basic authentication via static token

### Web UI Authentication
- Simple authentication using a static token passed in the URL
- Token configured through application environment variables
- Token generation and URL creation handled by the grisp.io framework
- Access restricted to users with the correct token
- Sufficient security for initial prototype while keeping implementation simple

### Traffic Shaping
- Single rule per topic
- Bandwidth bucket implementation for quota management
- QoS-aware filtering capabilities

## Future Considerations
- Enhanced network traffic handling
- Advanced traffic shaping rules
- Extended web interface capabilities
- Additional security features
- Advanced metrics and analytics
