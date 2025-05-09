# ro2erl_hub: Frontend Specification

## Overview
The ro2erl_hub application includes a web frontend that provides monitoring and configuration capabilities for the ROS2 Enhanced Reliability Layer. This document outlines the frontend requirements, layout, and integration with the hub component.

## First Milestone Frontend Implementation

### Core Functionality
- Simple authenticated access via URL token
- Bridge status display
- Topic listing with basic metrics and inline rule management
- System-wide statistics overview

### User Interface Layout

#### Bridge Status Panel
- Straightforward list of connected bridges
- No filtering or search functionality in initial implementation

#### Topic Management Panel
- List view of all active topics across connected bridge nodes
- Each topic displayed as a single row containing:
  - Topic name/identifier
  - Basic metrics: sample rate and bandwidth usage for dispatched messages
    (sent to the bridge) and forwarded messages (forwarded to the hub)
  - Visual indicator showing if filtering is possible
  - For filterable topics:
    - If no bandwidth limit is set: display an edit box and button to set a limit
    - If bandwidth limit is active: display the current limit and a button to disable it

#### Status Bar
- Aggregated bandwidth usage statistics,
in particular the aggregation for dispatched vs forwarded to see the impact of
the bandwidth limitation.

### Authentication
- URL-based token authentication (initial implementation)
- Token configured through ro2erl_hub environment variables
- No user management or settings interface

## Hub/Frontend Integration

The hub will provide a WebSocket interface with JSON-RPC for the frontend to connect and receive topic data, bridge status, and system metrics. This approach enables real-time updates and bidirectional communication between the frontend and the hub.

### WebSocket API

#### Protocol Details
*   The WebSocket connection implements standard ping/pong mechanism with a configurable ping interval (default: 30000ms) and timeout (default: 60000ms)
*   Clients MUST respond to ping frames with pong frames within the configured timeout period
*   The server will close the connection if no pong response is received within the timeout
*   Each message must be a complete, self-contained JSON-RPC message
*   Partial or malformed JSON messages will be rejected with error -32700
*   Upon reconnection, the client must request fresh data using topic.list and bridge.list
*   The client is responsible for maintaining its own state based on received notifications

#### Authentication
The WebSocket endpoint will be accessible at:
```
ws://<server>/ro2erl_hub/ws?token=<AUTH_TOKEN>
```

Where:
- `<server>` is the host and port of the ro2erl_hub server
- `<AUTH_TOKEN>` is the secret token configured in the hub's application environment

The token authentication is simple to implement and use, while still providing basic security:
*   Connections without a valid token will be rejected with HTTP 401 Unauthorized
*   Once authenticated, the connection is considered authorized for all subsequent operations

#### JSON-RPC Protocol
The WebSocket API follows the JSON-RPC 2.0 specification for both requests and responses.

##### Request Format
```json
{
  "jsonrpc": "2.0",
  "id": <id>,
  "method": "<method_name>",
  "params": <params_object>
}
```

##### Response Format
```json
{
  "jsonrpc": "2.0",
  "id": <id>,
  "result": <result_object>
}
```

##### Error Response Format
```json
{
  "jsonrpc": "2.0",
  "id": <id>,
  "error": {
    "code": <error_code>,
    "message": "<error_message>",
    "data": <additional_data>
  }
}
```

##### Notification Format (server to client)
```json
{
  "jsonrpc": "2.0",
  "method": "<notification_name>",
  "params": <params_object>
}
```

#### API Methods

##### topic.get
Retrieves information about a specific topic.

###### Parameters
```json
{
  "topic_name": "<topic_name>"
}
```

###### Response (Success)
```json
{
  "topic_name": "<topic_name>",
  "filterable": true,
  "bandwidth_limit": 1024,
  "metrics": {
    "dispatched": {
      "bandwidth": 512,
      "rate": 2.5
    },
    "forwarded": {
      "bandwidth": 768,
      "rate": 3.2
    }
  }
}
```

###### Response (Error)
```json
{
  "code": -32001,
  "message": "Topic not found"
}
```

##### topic.list
Retrieves a list of all known topics.

###### Parameters
None or empty object `{}`

###### Response (Success)
```json
[
  {
    "topic_name": "<topic_name_1>",
    "filterable": true,
    "bandwidth_limit": 1024,
    "metrics": {
      "dispatched": {
        "bandwidth": 512,
        "rate": 2.5
      },
      "forwarded": {
        "bandwidth": 768,
        "rate": 3.2
      }
    }
  },
  {
    "topic_name": "<topic_name_2>",
    "filterable": false,
    "bandwidth_limit": null,
    "metrics": {
      "dispatched": {
        "bandwidth": 128,
        "rate": 0.5
      },
      "forwarded": {
        "bandwidth": 128,
        "rate": 0.5
      }
    }
  }
]
```

##### topic.setBandwidth
Sets the bandwidth limit for a specific topic.

###### Parameters
```json
{
  "topic_name": "<topic_name>",
  "bandwidth": 2048
}
```

Note: Use null to remove bandwidth limits:
```json
{
  "topic_name": "<topic_name>",
  "bandwidth": null
}
```

###### Response (Success)
```json
"ok"
```

###### Response (Error)
```json
{
  "code": -32001,
  "message": "Topic not found"
}
```

Or

```json
{
  "code": -32002,
  "message": "Topic not filterable"
}
```

##### bridge.list
Retrieves a list of all connected bridges.

###### Parameters
None or empty object `{}`

###### Response (Success)
```json
[
  {"bridge_id": "bridge1"},
  {"bridge_id": "bridge2"}
]
```

#### Notifications

##### topic.updated
Sent by the server whenever topic information changes. Has the same format as the response from `topic.get`.

```json
{
  "jsonrpc": "2.0",
  "method": "topic.updated",
  "params": {
    "topic_name": "<topic_name>",
    "filterable": true,
    "bandwidth_limit": 2048,
    "metrics": {
      "dispatched": {
        "bandwidth": 512,
        "rate": 2.5
      },
      "forwarded": {
        "bandwidth": 768,
        "rate": 3.2
      }
    }
  }
}
```

##### bridge.attached
Sent by the server whenever a new bridge connects to the hub.

```json
{
  "jsonrpc": "2.0",
  "method": "bridge.attached",
  "params": {
    "bridge_id": "bridge1"
  }
}
```

##### bridge.detached
Sent by the server whenever a bridge disconnects from the hub.

```json
{
  "jsonrpc": "2.0",
  "method": "bridge.detached",
  "params": {
    "bridge_id": "bridge1",
    "reason": "normal"
  }
}
```

#### Error Codes

| Code    | Message                | Description                                       |
|---------|------------------------|---------------------------------------------------|
| -32001  | Topic not found        | The specified topic does not exist                |
| -32002  | Topic not filterable   | The topic cannot have bandwidth limits applied    |
| -32003  | Invalid bandwidth      | The bandwidth value is invalid                    |
| -32600  | Invalid request        | The JSON sent is not a valid Request object       |
| -32601  | Method not found       | The method does not exist / is not available      |
| -32602  | Invalid params         | Invalid method parameter(s)                       |
| -32603  | Internal error         | Internal JSON-RPC error                           |
| -32700  | Parse error            | Invalid JSON was received by the server           |

#### Example Usage Sequence

1. Connect to WebSocket with token authentication
   ```
   ws://hub.example.com/ro2erl_hub/ws?token=secret123
   ```

2. Request list of topics
   ```json
   // Client -> Server
   {
     "jsonrpc": "2.0",
     "id": 1,
     "method": "topic.list",
     "params": {}
   }

   // Server -> Client
   {
     "jsonrpc": "2.0",
     "id": 1,
     "result": [
       {
         "topic_name": "/sensor/data",
         "filterable": true,
         "bandwidth_limit": 1024,
         "metrics": {
           "dispatched": {"bandwidth": 512, "rate": 2.5},
           "forwarded": {"bandwidth": 768, "rate": 3.2}
         }
       }
     ]
   }
   ```

3. Set bandwidth for a topic
   ```json
   // Client -> Server
   {
     "jsonrpc": "2.0",
     "id": 2,
     "method": "topic.setBandwidth",
     "params": {
       "topic_name": "/sensor/data",
       "bandwidth": 2048
     }
   }

   // Server -> Client
   {
     "jsonrpc": "2.0",
     "id": 2,
     "result": "ok"
   }
   ```

4. Receive notification when topic changes
   ```json
   // Server -> Client (no request needed)
   {
     "jsonrpc": "2.0",
     "method": "topic.updated",
     "params": {
       "topic_name": "/sensor/data",
       "filterable": true,
       "bandwidth_limit": 2048,
       "metrics": {
         "dispatched": {"bandwidth": 600, "rate": 3.0},
         "forwarded": {"bandwidth": 900, "rate": 3.8}
       }
     }
   }
   ```

## Technology Considerations

### Frontend Implementation
- Lightweight SPA using minimal dependencies
- Clean, functional design prioritizing readability
- Optimized for desktop use cases
- Served directly by the ro2erl_hub application

### Backend Integration
- Cowboy web server integrated within the ro2erl_hub application
- JSON-RPC over WebSockets for real-time data exchange
- Token validation through the grisp.io framework
- Direct access to hub's internal state and metrics

## Future Enhancements

### UI Improvements
- Advanced topic filtering and search capabilities
- Detailed topic inspector panel with extended metrics
- Historical data visualization with charts and graphs
- Responsive design for mobile access
