# Deep Packet Inspection Engine (DPA)

A high-performance Deep Packet Inspection (DPI) system implemented in C++17 that analyzes PCAP traffic, performs TLS SNI-based application classification, enforces rule-based filtering, and generates structured traffic analytics reports.

This project demonstrates strong systems programming, networking fundamentals, and concurrent architecture design suitable for backend and infrastructure roles.

---

## 🔍 Overview

The Deep Packet Inspection Engine processes raw captured network traffic and performs:

- Low-level packet parsing (Ethernet, IPv4, TCP, UDP)
- Five-Tuple based flow identification
- TLS Client Hello inspection for SNI extraction
- Application classification (YouTube, Facebook, Google, DNS, etc.)
- Rule-based traffic blocking (IP / Domain / Application)
- Multi-threaded packet processing with load balancing
- Traffic analytics reporting

The engine simulates core components of enterprise firewall and traffic monitoring systems.

---

## 🏗 System Architecture

### 1️⃣ Single-Threaded Mode


PCAP Reader → Packet Parser → Flow Tracker → DPI Engine → Output Writer


Used for understanding packet lifecycle and debugging.

---

### 2️⃣ Multi-Threaded Mode

                Reader Thread
                       ↓
             Load Balancer Threads
                       ↓
            Fast Path (Worker) Threads
                       ↓
                Output Writer

### Architectural Highlights

- Consistent hashing ensures packets of the same flow are handled by the same worker
- Each worker maintains an independent flow table
- Producer–Consumer model implemented using thread-safe queues
- Synchronization via `std::mutex` and `std::condition_variable`
- Scalable with configurable thread counts

---

## 🌐 Networking Concepts Implemented

| Concept | Implementation |
|----------|----------------|
| Five-Tuple | src_ip, dst_ip, src_port, dst_port, protocol |
| Stateful Flow Tracking | Hash map keyed by Five-Tuple |
| TLS SNI Extraction | Parsing Client Hello extensions |
| Network Byte Order | ntohs / ntohl conversions |
| Flow-Level Blocking | Once blocked, all packets in flow dropped |

---

## 🔎 Deep Packet Inspection Logic

### TLS SNI-Based Application Detection

1. Detect TLS record (Content Type `0x16`)
2. Verify Client Hello handshake
3. Navigate to Extensions section
4. Locate SNI extension (`0x0000`)
5. Extract hostname
6. Map hostname → Application Type

Example:


TLS Client Hello
└── Extension: Server Name Indication
└── Hostname: www.youtube.com


This enables HTTPS traffic classification without decrypting encrypted payloads.

---

## 🚫 Rule Engine

Supports dynamic blocking rules:

| Rule Type | Example | Effect |
|-----------|----------|--------|
| IP Blocking | 192.168.1.50 | Blocks all traffic from source IP |
| App Blocking | YouTube | Blocks identified application |
| Domain Blocking | facebook | Blocks matching SNI domain |

Blocking is enforced at the flow level to maintain connection consistency.

---

## 📁 Project Structure


include/ → Header files
src/ → Implementation files
CMakeLists.txt → Build configuration
generate_test_pcap.py → Test PCAP generator
README.md


Key Components:

- `packet_parser.*` → Protocol parsing
- `pcap_reader.*` → PCAP file handling
- `sni_extractor.*` → TLS/HTTP inspection
- `dpi_mt.cpp` → Multi-threaded engine
- `main_working.cpp` → Single-threaded reference version

---

## ⚙️ Build Instructions

### Requirements

- C++17 compatible compiler
- CMake
- Linux / macOS / WSL (recommended)

### Build

```bash
mkdir build
cd build
cmake ..
make
▶️ Run
./dpi_engine input.pcap output.pcap
With Blocking Rules
./dpi_engine input.pcap output.pcap \
    --block-app YouTube \
    --block-domain facebook \
    --block-ip 192.168.1.50
📊 Sample Output
Total Packets: 77
TCP Packets: 73
UDP Packets: 4

Forwarded: 69
Dropped: 8

Application Breakdown:
HTTPS      50%
DNS        5%
YouTube    5% (Blocked)
Facebook   4%
⚡ Engineering Highlights

Manual byte-level protocol parsing (no external DPI libraries)

Custom TLS SNI extraction implementation

Multi-threaded packet processing pipeline

Consistent hashing for stateful flow handling

Thread-safe bounded queue design

Rule-based filtering architecture

Performance-oriented C++ systems design

🎯 Skills Demonstrated

Systems Programming (C++)

Networking Fundamentals (TCP/IP, TLS)

Concurrent Programming

Data Structures (hash maps, queues)

Load Balancing Design

Scalable Backend Architecture
---

### 📌 Project Focus

This project is built to demonstrate practical understanding of networking internals, concurrent system design, and scalable backend architecture using modern C++.