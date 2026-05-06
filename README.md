# CBDC Performance Analysis: Queueing Theory Simulation for Nepal

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/downloads/)
[![SimPy](https://img.shields.io/badge/SimPy-4.0+-green.svg)](https://simpy.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Thesis](https://img.shields.io/badge/Thesis-red.svg)](thesis/)

## Overview

This repository contains the simulation framework, analytical models, and Hyperledger Fabric prototype developed for the Undergraduate thesis **"Performance Analysis of CBDC Transaction Confirmation Time Using Queueing Theory: A Simulation Study for Nepal."**

The work provides the first quantitative performance evaluation of Central Bank Digital Currency (CBDC) architectures suitable for Nepal Rastra Bank's planned 2028 deployment at 200 transactions per second.

## Research Objectives

1. Develop queueing-theoretic models incorporating validation, consensus, and network delay for CBDC transaction confirmation
2. Implement a discrete-event simulation framework comparing five distinct architectures
3. Validate findings empirically using a Hyperledger Fabric prototype
4. Generate actionable deployment recommendations for Nepal Rastra Bank

## Five Queueing Models

| Model | Description | Use Case |
|-------|-------------|----------|
| Centralized M/M/1 | Single-server baseline | Theoretical reference |
| Permissioned DLT M/M/c | Parallel validators with shared queue | Recommended retail architecture |
| Multi-Stage Pipeline | Validation, Consensus, Commitment | Detailed delay analysis |
| Batch-Based M/G^B/1 | Size-based and time-based batching | Periodic settlement |
| Priority-Based | Three-class non-preemptive scheduling | Disaster relief, government payments |

## Key Findings

- **Permissioned DLT (c=5, q=3) achieves 6.69 ms mean confirmation time** at Nepal's 200 TPS target
- **System tolerates 2 simultaneous validator failures** with only 10.3% latency degradation
- **Critical priority class confirms in under 8 ms** even at 80% utilization
- **Hyperledger Fabric prototype processed 13,699 transactions with zero failures** across four load levels
- **All analytical formulas validated within 0.002% to 5.5% relative error** against simulation

## Repository Structure
.
├── analytical/              # Closed-form queueing formulas
│   ├── mm1_model.py
│   ├── mmc_model.py
│   ├── multistage_model.py
│   ├── batch_model.py
│   └── priority_model.py
├── simulation/              # SimPy discrete-event simulators
│   ├── algorithm_4_1_mm1.py
│   ├── algorithm_4_2_mmc.py
│   ├── algorithm_4_3_multistage.py
│   ├── algorithm_4_4_batch.py
│   ├── algorithm_4_5_priority.py
│   └── algorithm_4_6_runner.py
├── scenarios/               # Six experimental scenarios
│   ├── scenario_1_baseline.py
│   ├── scenario_2_nepal_target.py
│   ├── scenario_3_stress_test.py
│   ├── scenario_4_flash_crowd.py
│   ├── scenario_5_validator_failure.py
│   └── scenario_6_priority_mix.py
├── prototype/               # Hyperledger Fabric prototype
│   ├── network/             # Fabric network configuration
│   ├── chaincode/           # asset-transfer-basic
│   └── caliper/             # Caliper benchmark configurations
├── analysis/                # Statistical analysis and visualization
│   ├── compute_scores.py
│   ├── statistical_tests.py
│   └── multi_criteria_ranking.py
├── figures/                 # Generated plots from thesis
├── results/                 # Raw simulation outputs (CSV)
├── thesis/                  # Thesis document and presentation
└── README.md

## System Parameters

The default configuration models Nepal Rastra Bank's target deployment:

| Parameter | Value | Description |
|-----------|-------|-------------|
| λ | 200 TPS | Arrival rate |
| μ_v | 150 TPS | Validator service rate |
| μ_c | 200 ops/s | Consensus rate per validator |
| μ_l | 300 ops/s | Ledger commit rate |
| c | 5 | Number of validators |
| q | 3 | Quorum threshold |

## Installation

### Requirements

- Python 3.9 or higher
- pip package manager
- For prototype: Docker, Docker Compose, Node.js 16+

### Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/cbdc-queueing-simulation.git
cd cbdc-queueing-simulation

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Dependencies
simpy>=4.0
numpy>=1.21
scipy>=1.7
pandas>=1.3
matplotlib>=3.4
seaborn>=0.11

## Running the Simulations

### Run a single model

```bash
# Permissioned DLT at Nepal target load
python simulation/algorithm_4_2_mmc.py --lambda 200 --c 5 --mu 150 --replications 30

# Multi-stage with consensus order statistic
python simulation/algorithm_4_3_multistage.py --lambda 200 --c 5 --q 3
```

### Run all six scenarios

```bash
python scenarios/run_all_scenarios.py
```

### Generate analysis and figures

```bash
python analysis/multi_criteria_ranking.py
python analysis/generate_figures.py
```

## Hyperledger Fabric Prototype

The prototype validates simulation findings on production-grade software.

```bash
# Start 5-node Fabric network (3 Raft orderers + 2 peers)
cd prototype/network
./network.sh up createChannel -ca

# Deploy chaincode
./network.sh deployCC -ccn basic -ccp ../chaincode/asset-transfer-basic -ccl javascript

# Run Caliper benchmarks at four load levels
cd ../caliper
npx caliper launch manager --caliper-workspace . \
    --caliper-benchconfig benchmarks/load-test.yaml \
    --caliper-networkconfig networks/fabric-network.yaml
```

## Validation Results

| Model | Analytical | Simulated | Relative Error |
|-------|-----------|-----------|----------------|
| M/M/1 | 20.00 ms | 19.95 ms | 0.24% |
| M/M/c | 6.690 ms | 6.689 ms | 0.002% |
| Multi-Stage | 13.940 ms | 13.938 ms | 0.009% |
| Batch (time) | 1013.9 ms | 1018.7 ms | 0.47% |
| Priority (Class 1) | 3.333 ms | 3.291 ms | 1.27% |

## Citation

If you use this code or findings in your research, please cite:

```bibtex
@thesis{chaudhary2025cbdc,
  author       = {Hritik Chaudhary},
  title        = {Performance Analysis of CBDC Transaction Confirmation Time 
                  Using Queueing Theory: A Simulation Study for Nepal}
  school       = {Kathmandu University, Department of Mathematics},
  year         = {2025},
  type         = {Thesis},
  address      = {Dhulikhel, Nepal}
}
```

## Author

**Hritik Chaudhary**  
Department of Mathematics, Kathmandu University  
Email: hritikchaudhary.work@gmail.com  
LinkedIn: [linkedin.com/in/hritikchaudhary](https://www.linkedin.com/in/hritikchaudhary)

## Supervisors

- Assoc. Prof. Dr. Gokul K.C., Department of Mathematics, Kathmandu University
- Assoc. Prof. Dr. Samir Shrestha, Department of Mathematics, Kathmandu University

## Acknowledgments

This research was conducted in fulfillment of the requirements for the Bachelor's degree in Computational Mathematics at Kathmandu University. The author thanks Nepal Rastra Bank for providing the policy context through their CBDC Feasibility Study, and acknowledges the Hyperledger Foundation for the open-source Fabric framework used in prototype validation.

## References

Key references that informed this work:

1. Kleinrock, L. (1975). *Queueing Systems, Volume 1: Theory*. Wiley-Interscience.
2. Ghimire, S., Ghimire, R.P., Thapa, G.B., & Fernandes, S. (2017). Multi-server batch service queueing model with variable service rates. *International Journal of Applied Mathematics & Statistical Sciences*, 6(4), 43-54.
3. Cai, Y. (2018). *Transaction Confirmation Time Analysis of Bitcoin Using Queueing Theory*. Master's Thesis, University of Helsinki.
4. Sukhwani, H., Wang, N., Trivedi, K.S., & Rindos, A. (2018). Performance modeling of Hyperledger Fabric. *IEEE NCA 2018*.
5. Nepal Rastra Bank. (2024). *Central Bank Digital Currency Feasibility Study*.

## License

This work is released under the MIT License. See [LICENSE](LICENSE) file for details.
