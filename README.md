# DPI Engine – Deep Packet Inspection System

A high-performance Deep Packet Inspection (DPI) engine written in C++17 that parses network traffic from PCAP files, identifies applications using TLS SNI extraction, applies blocking rules, and generates traffic analytics reports.

This project demonstrates strong understanding of:
- Networking fundamentals (TCP/IP stack, TLS handshake, SNI)
- Systems programming
- Multi-threaded architecture
- Producer–Consumer pattern
- Hash-based flow tracking
- Efficient packet parsing

---

## 🚀 Features

- 🔍 Parses raw PCAP files (Ethernet, IPv4, TCP, UDP)
- 🌐 Extracts domain names from TLS Client Hello (SNI)
- 📊 Classifies applications (YouTube, Facebook, Google, DNS, etc.)
- 🚫 Supports rule-based blocking:
  - Block by IP
  - Block by Application
  - Block by Domain
- ⚡ Multi-threaded architecture with load balancing
- 📈 Generates detailed traffic analytics report
- 🧵 Thread-safe queue implementation

---

## 🧠 System Architecture

### Single-threaded Mode

PCAP Reader → Packet Parser → Flow Tracker → DPI Engine → Output Writer


### Multi-threaded Mode

Reader Thread
↓
Load Balancer Threads
↓
Fast Path (FP) Processing Threads
↓
Output Writer Thread


- Consistent hashing ensures all packets of a flow are handled by the same thread.
- Each Fast Path maintains its own flow table for efficiency.
- Producer–Consumer model implemented using mutex + condition variables.

---

## 🏗 Tech Stack

- **Language:** C++17
- **Concurrency:** std::thread, mutex, condition_variable
- **Build System:** CMake
- **Input Format:** PCAP
- **Protocols Supported:** Ethernet, IPv4, TCP, UDP, TLS

---

## 🔍 How SNI-Based Classification Works

1. Detect TLS Client Hello packet
2. Navigate to TLS Extensions section
3. Locate SNI extension (type `0x0000`)
4. Extract hostname
5. Map hostname → Application type

Example:


TLS Client Hello
└── Extension: SNI
└── Server Name: www.youtube.com


This allows identification of HTTPS applications without decrypting traffic.

---

## 📁 Project Structure


include/ → Header files
src/ → Implementation files
CMakeLists.txt → Build configuration
generate_test_pcap.py → Test PCAP generator
README.md


---

## ⚙️ Build & Run

### Build

```bash
mkdir build
cd build
cmake ..
make
Run
./dpi_engine input.pcap output.pcap
Run with Blocking Rules
./dpi_engine input.pcap output.pcap \
    --block-app YouTube \
    --block-domain facebook \
    --block-ip 192.168.1.50
📊 Sample Output Report
Total Packets: 77
Forwarded: 69
Dropped: 8

Application Breakdown:
HTTPS      50%
DNS        5%
YouTube    5% (Blocked)
Facebook   4%