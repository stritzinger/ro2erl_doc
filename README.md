# ro2erl_doc

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

## About This Repository

This repository contains the official documentation for the ROS2 Enhanced Reliability Layer (Target-X) project, developed by [Stritzinger GmbH](https://www.stritzinger.com).

Target-X enables secure and reliable communication between ROS2/DDS systems across distributed sites, with particular focus on optimizing performance in challenging network conditions.

## Documentation

### Core Documentation

| Document | Description |
|----------|-------------|
| [Overview](overview.md) | High-level introduction to Target-X |
| [Design Document](design.md) | Comprehensive technical specification and architecture |
| [Frontend Specification](frontend.md) | ro2erl_hub web interface requirements and integration |

### Guides and References

| Document | Description |
|----------|-------------|
| [Prerequisites](prerequisites.md) | System requirements and technology prerequisites |
| [Getting Started](getting_started.md) | Basic setup instructions for both components |
| [Troubleshooting](troubleshooting.md) | Common issues and solutions |
| [Glossary](glossary.md) | Definitions of key technical terms |

## Project Components

The Target-X project consists of two main components:

| Repository | Purpose |
|------------|---------|
| [ro2erl_bridge](https://github.com/stritzinger/ro2erl_bridge) | Bridge component implementation |
| [ro2erl_hub](https://github.com/stritzinger/ro2erl_hub) | Hub component and web frontend implementation |
| [ro2erl_demo](https://github.com/stritzinger/ro2erl_demo) | Reference implementation for grisp board deployment |

## Contributing

Contributions to this documentation are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

This project builds upon the [rosie_rclerl](https://github.com/rosie-project/rosie_rclerl) project, which provides ROS Client Library functionality in Erlang. 