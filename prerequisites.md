# Prerequisites

This document outlines the necessary prerequisites for working with and deploying the ROS2 Enhanced Reliability Layer (Target-X).

## System Requirements

### ro2erl_bridge Requirements

- **Erlang/OTP**: Version 27 or later
- **ROS2**: Galactic or newer
- **Network**: Ability to establish outbound connections (no inbound connections required)
- **rosie_rclerl**: ROS2 Client Library in Erlang

### ro2erl_hub Requirements

- **Erlang/OTP**: Version 27 or later
- **Container Support**: Docker for containerized deployment
- **Network**: Public endpoint with TLS support
- **grisp.io Platform**: Integration with the grisp.io cloud platform for the initial milestone
  - Provides secure distribution
  - Handles certificate management
  - Manages authentication
  - Provides connectivity infrastructure

## Knowledge Prerequisites

To work effectively with the Target-X project, familiarity with the following technologies is recommended:

- **Erlang**: Basic understanding of Erlang syntax and OTP principles
- **ROS2**: Familiarity with ROS2 concepts, especially topics, QoS, and DDS
- **Docker**: For deploying and managing the hub component
- **Network Concepts**: Understanding of topics like bandwidth management, latency, and connectivity challenges

## Development Environment Setup

For development work on the Target-X project components, the following tools are recommended:

- **rebar3**: Erlang build tool for managing dependencies and building releases
- **Docker**: For testing hub deployment
- **ROS2 Environment**: A working ROS2 installation for testing the bridge component
- **Git**: For version control and contribution management

## Third-Party Dependencies

The project relies on the following key external dependencies:

- **rosie_rclerl**: ROS Client Library in Erlang ([GitHub Repository](https://github.com/rosie-project/rosie_rclerl))
- **grisp_connect**: For bridge clients connecting to the hub *(part of the grisp.io framework)*
- **braid_node**: For hub deployment in the grisp.io cloud platform

Additional dependencies are specified in each component's `rebar.config` file.

## Compatibility Notes

- The initial implementation uses the rosie_rclerl client for ROS2 integration, with the dispatch mode handling message passing
- For optimal security, the recommended deployment uses the grisp.io platform infrastructure for secure node communication, but alternative deployment models are possible

> **Note**: Specific version compatibility information will be expanded as the project matures. Check the repository for updates to compatibility requirements. 