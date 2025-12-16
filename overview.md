# ROS2 Enhanced Reliability Layer (Target-X)

## Project Purpose
Target-X enables secure and reliable ROS2/DDS communication across distributed robotic systems, optimizing performance for challenging network conditions like low bandwidth, high latency, and intermittent connectivity.

## At a Glance

- **Problem**: ROS2/DDS systems struggle with wide-area networks, bandwidth constraints, and security challenges 
- **Solution**: Bridge-hub architecture with intelligent traffic management
- **Benefits**: Extended operational range, network resilience, and simplified multi-site management
- **Implementation**: Two independent Erlang applications handling local and cloud-side operations

## Architecture Highlights

```
[Robot Site A]                       [Cloud]                         [Robot Site B]
  ROS2/DDS   <--------Internet-----> Hub Node <------Internet-------->  ROS2/DDS
   ^   ^                               ^  ^                               ^   ^
   |   |                               |  |                               |   |
   v   v                               |  |                               v   v
Bridge Node <--Erlang Distribution---- +  + ----Erlang Distribution--> Bridge Node
[Traffic Shaping]                       \/                       [Traffic Shaping]
     ^                         Hub-managed Peer List                        ^
     |                                  ||                                  |
     \--------- Direct Bridge-to-Bridge Erlang Distribution ----------------/
                            (Optional Direct Data Path)
```

## Key Components

- **ro2erl_bridge**: Monitors topics, applies filtering rules, and manages data flow at each robot site
- **ro2erl_hub**: Coordinates connected bridges, distributes messages, and provides web UI for monitoring and configuration
- **Direct-connect option**: Bridges can be configured to exchange data directly bridge↔bridge while still reporting metrics to, and receiving control from, the hub
- **Traffic Shaping**: Intelligently manages bandwidth through configurable rules and topic prioritization
- **Security**: Leverages TLS encryption and certificate-based authentication via the grisp.io framework
- **Web Interface**: Provides monitoring, configuration, and rule management capabilities

## Key Features

- **Secure Communication**: End-to-end encryption with TLS and certificate-based authentication
- **Bandwidth Management**: Configurable traffic shaping policies for efficient utilization of limited connections
- **QoS-Aware Filtering**: Intelligent message handling respecting ROS2 Quality of Service requirements
- **Metrics Collection**: Comprehensive statistics gathering for performance monitoring and optimization
- **Centralized Management**: Web interface for configuration, monitoring, and rule management
- **Resilient Operation**: Store-and-forward capabilities during intermittent connectivity
- **Erlang Foundation**: Built on Erlang's robust distribution and concurrency model

## Primary Use Cases

- Fleet management across multiple facilities
- Remote robot operations in bandwidth-constrained environments
- Secure data collection from distributed robotic systems
- Multi-site ROS2 application deployment
- Cloud-connected field robotics
- Industrial automation with distributed components

## Technical Documentation

For more detailed information, see:

- [Design Document](design.md) - Comprehensive technical specification and architecture
- [Frontend Specification](frontend.md) - ro2erl_hub web interface requirements and integration
