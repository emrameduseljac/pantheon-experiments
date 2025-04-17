# My Pantheon Experiment Report

This file documents the congestion control protocol experiments for CS Networking Assignment Part B and C.

---

## Chosen Algorithms

- TCP Cubic  
- TCP BBR  
- PCC-Allegro  

---

## Network Profiles Tested

1. **High-bandwidth, low-latency**:  
   - 50 Mbps  
   - 10 ms delay  

2. **Low-bandwidth, high-latency**:  
   - 1 Mbps  
   - 200 ms delay  

---

## 🧪 How to Reproduce the Experiments

### 1. Install Dependencies

```bash
sudo apt install -y git python2 build-essential mahimahi iproute2 iperf apache2 \
  libprotobuf-dev protobuf-compiler libssl-dev libtinfo-dev libncurses5-dev

### Generate Trace Files

mkdir -p /tmp/traces

# 50 Mbps trace
python2 -c "import sys; [sys.stdout.write('1500\n') for _ in range(4167)]" > /tmp/traces/50mbps.trace

# 1 Mbps trace
python2 -c "import sys; [sys.stdout.write('1500\n') for _ in range(83)]" > /tmp/traces/1mbps.trace

## Running the Tests

python2 src/experiments/test.py local \
  --schemes "cubic bbr" \
  --runtime 60 \
  --uplink-trace /tmp/traces/50mbps.trace \
  --downlink-trace /tmp/traces/50mbps.trace \
  --prepend-mm-cmds "mm-delay 5"
python2 src/experiments/test.py local \
  --schemes "pcc" \
  --runtime 60 \
  --uplink-trace /tmp/traces/50mbps.trace \
  --downlink-trace /tmp/traces/50mbps.trace \
  --prepend-mm-cmds "mm-delay 5"
python2 src/experiments/test.py local \
  --schemes "cubic bbr" \
  --runtime 60 \
  --uplink-trace /tmp/traces/1mbps.trace \
  --downlink-trace /tmp/traces/1mbps.trace \
  --prepend-mm-cmds "mm-delay 100"
python2 src/experiments/test.py local \
  --schemes "pcc" \
  --runtime 60 \
  --uplink-trace /tmp/traces/1mbps.trace \
  --downlink-trace /tmp/traces/1mbps.trace \
  --prepend-mm-cmds "mm-delay 100"

## Analyzing Results
python2 src/analysis/analyze.py --data-dir src/experiments/<data_dir>
python2 src/analysis/plot.py --data-dir src/experiments/<data_dir>

Then the output will be generated in src/experiments/<data_dir>/

Each contains:

Throughput over time

Delay statistics

Loss rate

One-page summary (pantheon_report.pdf)
