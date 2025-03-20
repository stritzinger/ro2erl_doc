# Troubleshooting Guide

This document provides guidance for diagnosing and resolving common issues with the ROS2 Enhanced Reliability Layer (Target-X).

## Connection Issues

### Bridge Connection Issues

#### Symptoms
- Bridge not appearing in hub interface
- No message flow between sites
- Bridge metrics not updating

#### Common Causes
1. **Parent Application Configuration**
   - Incorrect grisp_connect configuration
   - Missing or incorrect cluster configuration
   - Network connectivity issues between parent application and grisp.io platform

2. **Bridge Configuration**
   - Missing or incorrect dispatch callback configuration
   - Incorrect application environment settings

#### Solutions
1. **Check Parent Application**
   - Verify grisp_connect is properly configured
   - Ensure cluster configuration is correct
   - Check network connectivity to grisp.io platform

2. **Verify Bridge Setup**
   - Review dispatch callback configuration
   - Validate application environment variables

3. **Network Verification**
   - Ensure proper network access is configured
   - Check firewall settings
   - Verify DNS resolution

## Hub Issues

### Hub Web Interface Not Accessible

**Symptoms:**
- Cannot access the hub's web interface
- Connection refused or timeout errors

**Possible Causes and Solutions:**

1. **Missing or Incorrect Cluster Setup**
   - Ensure a cluster has been created on grisp.io
   - Verify the cluster is properly configured with a braid node running ro2erl_hub
   - Check that the cluster is in a running state

2. **Wrong Port Configuration**
   - Verify the HTTP port configuration (default: 8080)
   - Check that the port is properly exposed if using Docker

3. **Authentication Issues**
   - Ensure you're using the correct access token in the URL
   - Verify the token is properly configured in the hub's environment

## Traffic Forwarding Issues

### Messages Not Being Forwarded

**Symptoms:**
- Bridge and hub are connected, but messages don't arrive at the destination
- No errors in logs

**Possible Causes and Solutions:**

1. **Topic Not Dispatched**
   - Verify that the topic is being dispatched by the sending bridge
   - Check dispatch calls in your application code
   - Ensure that the rosie_rclerl client is properly initialized

2. **Filter Rules Blocking Traffic**
   - Check if bandwidth limitation rules might be throttling the traffic
   - Temporarily disable rules to test if that's the issue

3. **Callback Not Registered**
   - Ensure that the receiving application has registered a dispatch callback
   - Verify the callback function is working as expected

### Bandwidth Limitations Not Working

**Symptoms:**
- Traffic exceeds configured bandwidth limits
- No throttling appears to happen

**Possible Causes and Solutions:**

1. **Rule Not Applied**
   - Check that the rule is enabled in the web interface
   - Verify the rule applies to the correct topic

2. **Topic QoS Incompatible**
   - Some topics with certain QoS settings cannot be filtered
   - Check the topic details in the web interface for compatibility

## Performance Issues

### High Latency

**Symptoms:**
- Message delivery is slow between sites
- End-to-end latency is higher than expected

**Possible Causes and Solutions:**

1. **Network Conditions**
   - Check network latency between sites
   - Consider if bandwidth limitations are too aggressive

2. **Resource Constraints**
   - Check CPU and memory usage on bridge and hub machines
   - Consider scaling resources if necessary

> **Note**: This section will be expanded with more specific performance tuning guidance as the project matures and real-world deployment data becomes available.

## Getting Help

If you encounter issues not covered in this guide:

1. Check the [GitHub repositories](https://github.com/stritzinger/ro2erl_doc) for updated documentation
2. Look for open or closed issues that might address your problem
3. Open a new issue with detailed information about your problem, including:
   - Component versions
   - Configuration details
   - Relevant log excerpts
   - Steps to reproduce

> **Note**: This troubleshooting guide will be expanded with additional issues and solutions as they are identified during deployment and usage. 