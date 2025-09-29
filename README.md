<!-- Quality & Security Overview -->
[![CodeQL](https://github.com/CalebSargeant/gns3/actions/workflows/github-code-scanning/codeql/badge.svg)](https://github.com/CalebSargeant/gns3/actions/workflows/github-code-scanning/codeql)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=alert_status)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Security Rating](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=security_rating)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Known Vulnerabilities](https://snyk.io/test/github/CalebSargeant/gns3/badge.svg)](https://snyk.io/test/github/CalebSargeant/gns3)

<!-- Code Quality & Maintainability -->
[![Maintainability Rating](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=sqale_rating)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Reliability Rating](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=reliability_rating)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Technical Debt](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=sqale_index)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)

<!-- Code Metrics -->
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=coverage)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=bugs)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Vulnerabilities](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=vulnerabilities)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Code Smells](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=code_smells)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)

<!-- Project Stats -->
[![Lines of Code](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=ncloc)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)
[![Duplicated Lines (%)](https://sonarcloud.io/api/project_badges/measure?project=CalebSargeant_gns3&metric=duplicated_lines_density)](https://sonarcloud.io/summary/new_code?id=CalebSargeant_gns3)

# GNS3 Network Labs

This repository contains a collection of GNS3 networking labs for learning and practicing various network technologies, focusing on enterprise networking scenarios including firewall configurations, routing protocols, and high availability designs.

## Overview

This project provides hands-on network laboratory exercises using GNS3 (Graphical Network Simulator-3) to create realistic network topologies for educational and testing purposes.

## Repository Structure

```
├── projects/           # GNS3 project files and lab scenarios
│   └── asa-active-passive/  # Cisco ASA failover lab
├── configs/           # Base configuration templates
├── appliances/        # GNS3 appliance definitions
└── README.md         # This file
```

## Available Labs

### 1. Cisco ASA Active/Passive Failover Lab
**Location:** `projects/asa-active-passive/`
**Status:** 🔄 In Development

A comprehensive network laboratory demonstrating Cisco ASA firewall high availability using active/passive failover configuration.

**Features:**
- Dual ASA firewalls in active/passive configuration
- Multiple inside and outside networks
- EIGRP routing protocol integration
- Multi-layer switching with HSRP/MHSRP
- NAT configurations for internet connectivity
- Network segmentation with VLANs

**Topology Includes:**
- 2x Cisco ASAv firewalls (Primary/Secondary)
- 4x Layer 2/3 switches
- 2x ISP routers
- Multiple VLAN segments (10, 20, 30, 40)
- Host devices for testing

## Prerequisites

- GNS3 installed and configured
- Cisco ASAv image (asav981.qcow2)
- Cisco IOSv images for routers and switches
- Basic understanding of Cisco networking technologies

## Configuration Templates

The `configs/` directory contains starter configuration templates for:
- Basic IOS router configuration
- IOS Layer 2 switch configuration  
- IOS Layer 3 switch configuration
- EtherSwitch module configuration
- VPCS host configuration

## Getting Started

1. Clone this repository
2. Import the desired project into GNS3
3. Ensure you have the required appliance images
4. Follow the specific lab documentation in each project folder

## Contributing

Feel free to contribute additional labs, configuration improvements, or documentation updates through pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
