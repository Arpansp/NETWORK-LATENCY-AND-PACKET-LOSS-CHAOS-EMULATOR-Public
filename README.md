# Network Latency and Packet Loss Chaos Emulator

A C++17-based network chaos emulator for testing how applications behave under unreliable and unstable network conditions.

The project uses **Linux Network Namespaces** and **`tc netem`** to simulate real-world network problems such as latency, jitter, packet loss, outages, unstable connectivity, and bandwidth limitations.

## Features

- Network namespace-based isolated test environment
- Client-server network topology
- Artificial network latency
- Jitter simulation
- Packet loss simulation
- Bandwidth limitation
- Predefined network profiles
- Random network chaos
- Network flapping
- Timeline-based network scenarios
- RTT and packet-loss measurement
- Continuous network monitoring
- Latency experiments
- Packet-loss experiments
- TCP loss and RTT testing
- Chaos testing
- Resilience testing
- CSV-based result generation

## Technologies Used

- **C++17**
- **Linux**
- **CMake**
- **Linux Network Namespaces**
- **iproute2**
- **tc netem**
- **curl**
- **ping**
- **iperf3**
- **VirtualBox** (for the virtualized test environment)

## Project Structure

```text
chaos-cpp/
│
├── CMakeLists.txt
├── README.md
├── scenario.json
│
├── include/
│   ├── emulator.hpp
│   ├── measure.hpp
│   └── json.hpp
│
├── src/
│   ├── main.cpp
│   ├── emulator.cpp
│   ├── profiles.cpp
│   ├── scheduler.cpp
│   ├── measure.cpp
│   ├── experiments.cpp
│   └── util.cpp
│
├── scripts/
│   ├── setup_lab.sh
│   └── teardown_lab.sh
│
├── tests/
│   └── test_emulator.cpp
│
├── results/
│   ├── measure.csv
│   ├── latency.csv
│   ├── loss.csv
│   ├── chaos_ping.csv
│   ├── chaos_iperf.csv
│   ├── chaos_events.csv
│   └── resilience.csv
│
└── build/
    └── chaosctl
```

## Network Topology

The emulator creates an isolated client-server network using Linux namespaces.

```text
        Client Namespace
          10.0.0.1
              |
           veth-c
              |
              |
        tc/netem rules
              |
              |
           veth-s
              |
        Server Namespace
          10.0.0.2
```

Network impairments are applied using `tc netem` to reproduce different network conditions.

## Requirements

The project requires a Linux environment with:

- GCC / G++
- C++17 support
- CMake
- iproute2
- `tc`
- `ping`
- `curl`
- `iperf3`
- Root/sudo privileges

If running on Windows, Ubuntu can be installed inside **VirtualBox** or another Linux virtual machine.

## Building the Project

Clone the repository and enter the project directory:

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd chaos-cpp
```

Create the build directory:

```bash
cmake -S . -B build
```

Build the project:

```bash
cmake --build build -j
```

The executable will be generated as:

```text
build/chaosctl
```

## Setting Up the Network Lab

Run:

```bash
sudo ./scripts/setup_lab.sh
```

This creates the required client and server network namespaces and configures the virtual network connection.

Check connectivity:

```bash
sudo ./build/chaosctl ping
```

## Basic Usage

### View Current Network Configuration

```bash
sudo ./build/chaosctl show
```

### Apply a Network Profile

For example:

```bash
sudo ./build/chaosctl profile 4g
```

Other available profiles include:

```text
clean
broadband
4g
3g
bad_wifi
satellite
disaster
outage
```

Restore the clean network:

```bash
sudo ./build/chaosctl profile clean
```

## Network Measurement

Measure network performance under a selected profile:

```bash
sudo ./build/chaosctl measure --profile 4g
```

Example output:

```text
profile=4g
rtt_min=...
rtt_avg=...
rtt_max=...
jitter=...
loss=...
```

Results are stored in:

```text
results/measure.csv
```

## Network Monitoring

Monitor the network continuously for a specified duration:

```bash
sudo ./build/chaosctl monitor --duration 15
```

This records RTT, packet loss, and network behavior over time.

## Network Flapping

The emulator can repeatedly switch between healthy and unhealthy network states.

Example:

```bash
sudo ./build/chaosctl flap --duration 12 --up 3 --down 2
```

This produces a sequence similar to:

```text
up
down
up
down
up
end
```

## Timeline Scenarios

Network conditions can also be controlled using a scenario file.

Example `scenario.json`:

```json
{
  "events": [
    {
      "profile": "clean",
      "duration": 3
    },
    {
      "profile": "4g",
      "duration": 3
    },
    {
      "profile": "bad_wifi",
      "duration": 3
    },
    {
      "profile": "outage",
      "duration": 3
    }
  ]
}
```

Run the scenario:

```bash
sudo ./build/chaosctl timeline scenario.json
```

## Experiments

The project provides several experiments for evaluating network behavior.

### Latency Experiment

```bash
sudo ./build/chaosctl experiment latency
```

### Packet Loss Experiment

```bash
sudo ./build/chaosctl experiment loss
```

### TCP Loss Experiment

```bash
sudo ./build/chaosctl experiment tcp_loss
```

### TCP RTT Experiment

```bash
sudo ./build/chaosctl experiment tcp_rtt
```

### Network Profile Experiment

```bash
sudo ./build/chaosctl experiment profiles
```

### Burst Loss Experiment

```bash
sudo ./build/chaosctl experiment burst
```

### Chaos Experiment

```bash
sudo ./build/chaosctl experiment chaos
```

### Resilience Experiment

```bash
sudo ./build/chaosctl experiment resilience
```

## Result Files

Experimental results are stored as CSV files inside the `results/` directory.

| File | Purpose |
|---|---|
| `measure.csv` | RTT, jitter and packet-loss measurements |
| `latency.csv` | Latency experiment results |
| `loss.csv` | Packet-loss experiment results |
| `chaos_ping.csv` | Ping results during chaos testing |
| `chaos_iperf.csv` | Throughput results during chaos testing |
| `chaos_events.csv` | Network state changes during chaos |
| `resilience.csv` | Resilience test results |

The CSV files are generated directly by the C++ application.

## Complete Test Workflow

A typical test session can be performed using:

```bash
cd ~/Downloads/chaos-cpp

cmake -S . -B build
cmake --build build -j

sudo ./scripts/setup_lab.sh

sudo ./build/chaosctl ping

sudo ./build/chaosctl profile 4g
sudo ./build/chaosctl show

sudo ./build/chaosctl measure --profile 4g
sudo ./build/chaosctl monitor --duration 15

sudo ./build/chaosctl experiment latency
sudo ./build/chaosctl experiment loss
sudo ./build/chaosctl experiment chaos
sudo ./build/chaosctl experiment resilience

sudo ./build/chaosctl profile clean
sudo ./build/chaosctl ping

sudo ./scripts/teardown_lab.sh
```

## Cleanup

Always restore the network before finishing:

```bash
sudo ./build/chaosctl profile clean
```

Then remove the test namespaces:

```bash
sudo ./scripts/teardown_lab.sh
```

## Project Objective

The main objective of this project is to provide a controlled environment for studying application and network behavior under unreliable network conditions.

Instead of testing only on a stable network, the emulator allows developers and researchers to reproduce conditions such as:

- High latency
- Variable delay
- Packet loss
- Network outages
- Unstable Wi-Fi
- Mobile network conditions
- Bandwidth restrictions
- Bursty network failures

## Advantages

- Lightweight and command-line based
- Uses isolated Linux network namespaces
- Does not require modifying the tested application
- Reproducible network conditions
- Supports multiple predefined profiles
- Supports automated experiments
- Generates structured CSV results
- Suitable for network and resilience testing

## Limitations

- Requires Linux networking features
- Root/sudo privileges are required
- The current version is primarily command-line based
- Results are generated as CSV files rather than through a graphical dashboard
- The emulator is designed for controlled testing rather than production network management

## Future Enhancements

Possible future improvements include:

- Graphical user interface
- More realistic network profiles
- Automated report generation
- Real-time visualization
- Additional traffic models
- More application-level resilience tests
- Automated experiment scheduling
- Extended network statistics

## Conclusion

The Network Latency and Packet Loss Chaos Emulator provides a controlled C++17 environment for simulating unreliable network conditions. By combining Linux network namespaces with `tc netem`, the system can reproduce latency, jitter, packet loss, outages, bandwidth limitations, and unstable network behavior.

The project also provides measurement, monitoring, experiment execution, resilience testing, and CSV-based result generation, making it useful for studying how network-dependent applications behave under adverse conditions.
