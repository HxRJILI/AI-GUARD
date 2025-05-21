============
Requirements
============

System requirements for running the AI-GUARD system in various configurations.

Hardware Requirements
===================

Server Components
---------------

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Component
     - Minimum Requirements
   * - **CPU**
     - 8 cores (16 recommended for production)
   * - **RAM**
     - 16GB (32GB recommended for production)
   * - **Storage**
     - 100GB SSD (500GB recommended for production)
   * - **GPU**
     - NVIDIA GPU with 4GB VRAM for recognition service
   * - **Network**
     - 1Gbps Ethernet

Edge Devices
-----------

Jetson Nano:
- Quad-core ARM A57 CPU
- 4GB RAM
- 16GB storage (microSD)
- Camera with at least 720p resolution

Raspberry Pi:
- Raspberry Pi 4 with 4GB RAM minimum
- 32GB microSD storage
- Camera module v2 or compatible USB camera

Software Requirements
===================

Server Environment
----------------

Operating Systems:
- Ubuntu 22.04 LTS (recommended)
- Debian 11 or newer
- CentOS/RHEL 8 or newer

Container Technology:
- Docker 24.x or newer
- Docker Compose 2.x or newer
- Kubernetes 1.25.x or newer (for clustered deployment)

Database:
- PostgreSQL 14.x or newer

Edge Environment
--------------

Operating Systems:
- Jetson: JetPack 4.6 or newer
- Raspberry Pi: Raspberry Pi OS 11 (Bullseye) or newer

Device Management:
- SSH access
- Network connectivity
- NTP for time synchronization

Network Requirements
==================

- Dedicated VLAN for security devices (recommended)
- Open ports:
  - TCP 80/443 (HTTP/HTTPS)
  - TCP 5432 (PostgreSQL)
  - TCP 6379 (Redis)
  - TCP/UDP 8883 (MQTT)
- Internet connectivity for system updates (optional)
- Static IP addresses for all devices
- QoS prioritization for camera traffic