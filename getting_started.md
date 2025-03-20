# Getting Started with Target-X

This guide will help you get started with the ROS2 Enhanced Reliability Layer (Target-X) for secure and reliable ROS2 communication across distributed systems.

## Overview

Target-X enables secure and reliable ROS2 communication across different sites through a cloud-based hub. It's particularly useful for:
- Multi-site robotic deployments
- Remote robot operations
- Systems with bandwidth constraints
- Secure ROS2 communication across public networks

## Quick Start

### 1. Prerequisites

Before you begin, ensure you have reviewed the [Prerequisites](prerequisites.md) document and have all required dependencies installed.

### 2. Setting Up Your Cluster

1. Log in to your grisp.io account
2. Create a new cluster or select an existing one
3. Configure your cluster:
   - Select the client nodes that will run the bridge component
   - Choose the braid node image that includes the ro2erl_hub
   - Configure network access and security settings

The grisp.io platform will automatically:
- Set up secure Erlang distribution between nodes
- Configure certificates and authentication
- Establish the necessary network connectivity

### 3. Deploying the Bridge

The ro2erl_bridge is designed to be used as a dependency within your own Erlang application. You have two options:

#### Option 1: Create Your Own Application

1. Create a new Erlang application:
   ```bash
   rebar3 new app my_ro2erl_app
   cd my_ro2erl_app
   ```

2. Add the required dependencies to `rebar.config`:
   ```erlang
   {deps, [
       {ro2erl_bridge, {git, "https://github.com/stritzinger/ro2erl_bridge.git"}},
       {grisp_connect, {git, "https://github.com/grisp/grisp_connect.git"}}
   ]}.
   ```

3. Configure ro2erl_bridge in your application's config:
   ```erlang
   % config/sys.config
   {ro2erl_bridge, [
       {dispatch_callback, my_app:handle_message/1}
   ]}.
   ```

4. Deploy your application:
   - For local development:
     ```bash
     rebar3 release
     ```
   - For grisp board deployment:
     ```bash
     rebar3 as dev grisp deploy
     # or for production
     rebar3 as prod grisp deploy
     ```

#### Option 2: Use the Reference Implementation

The [ro2erl_demo](https://github.com/stritzinger/ro2erl_demo) project provides a complete, self-contained example of an application using ro2erl_bridge on a grisp board:

1. Clone and deploy the demo project:
   ```bash
   git clone https://github.com/stritzinger/ro2erl_demo.git
   cd ro2erl_demo
   rebar3 as dev grisp deploy
   ```

The demo project serves as a complete reference implementation, showing:
- Proper dependency management
- Configuration setup
- Message handling
- Deployment process

### 4. Accessing the Hub Interface

1. Access your cluster configuration in the grisp.io interface:
   - Log in to your grisp.io account
   - Navigate to your cluster configuration page
   - Find the ro2erl_hub service details
   - The interface will provide the secure URL for accessing the hub

2. The hub web interface provides:
   - Real-time status of connected bridges
   - Active ROS2 topics monitoring
   - Traffic statistics
   - Bandwidth management controls

### 5. Managing Traffic

1. View active topics in the web interface
2. Configure bandwidth limits for specific topics:
   - Select the topic
   - Set the desired bandwidth limit
   - Apply the rule

3. Monitor traffic statistics:
   - View real-time bandwidth usage
   - Check message rates
   - Monitor connection status

## Troubleshooting

If you encounter any issues during setup or operation, please refer to the [Troubleshooting Guide](troubleshooting.md) for detailed solutions to common problems.

## Next Steps

- Review the [design documentation](design.md)
- Join the community discussions

> **Note**: This guide assumes a working implementation. Some features and configurations may be adjusted during development. 