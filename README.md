# Bittensor
Quasar:  A decentralized intelligence solution built on Bittensor. This repository contains our submission for the Bittensor Ideathon.
Here's your complete GitHub README. Here's what's covered across the file:
# ⚡ Quasar — Decentralized Quantum Computing Marketplace

> **Bittensor Subnet | Version 1.0 | February 2026**

Quasar is an open, decentralized marketplace for quantum computing built on Bittensor. It connects researchers and developers who need quantum compute power with miners who provide it — all verified automatically and rewarded in TAO.

No queues. No institutional fees. No blind trust.

---

## 📌 Table of Contents

- [What is Quasar?](#what-is-quasar)
- [Why Quasar?](#why-quasar)
- [How It Works](#how-it-works)
- [Miner Types](#miner-types)
- [Scoring System](#scoring-system)
- [Emission Model](#emission-model)
- [Getting Started](#getting-started)
  - [Use Quantum Compute](#use-quantum-compute)
  - [Run a Simulator Miner](#run-a-simulator-miner)
  - [Run a QPU Miner](#run-a-qpu-miner)
  - [Run a Validator](#run-a-validator)
- [Roadmap](#roadmap)
- [Key Performance Indicators](#key-performance-indicators)
- [Security & Fraud Prevention](#security--fraud-prevention)
- [Architecture](#architecture)
- [FAQ](#faq)
- [License](#license)

---

## What is Quasar?

Quasar is a **Bittensor subnet** that creates a decentralized marketplace for quantum computing — like Uber for quantum computers.

- **Users** submit quantum circuits in standard Qiskit / OpenQASM format
- **Miners** execute them on real QPU hardware or GPU simulators
- **Validators** independently verify results using statistical tests
- **TAO rewards** flow to the best miners via Yuma Consensus

```
User submits circuit → Miners compete to execute → Validators verify → TAO rewarded → User gets verified result
```

---

## Why Quasar?

Quantum computing access today is broken:

| Problem | Current Reality | Who Suffers |
|---|---|---|
| Long queues & high fees | Free users wait hours or days. Premium access costs thousands/month | Startups & independent researchers |
| Vendor lock-in | Each provider uses its own tools. Migrating is expensive | Developers |
| Unverifiable results | No independent way to confirm results ran on real hardware | Research integrity |

**Quasar solves all three:**

| Problem | Quasar's Solution |
|---|---|
| Long queues & high prices | Multiple miners compete → faster service, competitive pricing |
| Vendor lock-in | One standard API across IBM, IonQ, Rigetti — change one line of code |
| Unverifiable results | 3+ independent validators check every result mathematically |

---

## How It Works

Quasar processes every quantum job in 6 steps:

```
1. SUBMIT      → User writes circuit in Qiskit/OpenQASM, signs with wallet
2. ROUTE       → Smart router dispatches to best available miner
3. EXECUTE     → Miner runs on QPU hardware or GPU simulator
4. VERIFY      → 3+ validators check accuracy, hardware fingerprint, consensus
5. CONSENSUS   → Yuma stake-weighted median produces final quality rankings
6. REWARD      → Miners earn TAO proportional to quality score
```

All of this happens in **under 60 seconds** for most circuits.

---

## Miner Types

| Tier | Hardware | Max Qubits | Best For | Reward |
|---|---|---|---|---|
| **Premium QPU** | IBM / IonQ / Rigetti cloud API | 27–127 | Authenticity-critical work | **1.5x bonus** |
| **High-Fidelity Sim** | GPU statevector (cuQuantum) | 28–36 | High-volume calculations | Medium |
| **Density Matrix Sim** | Noisy GPU sim (Qiskit Aer) | 20–30 | Tunable noise testing | Medium |
| **Hybrid Router** | Dynamic QPU + Sim dispatch | 20–127 | Cost optimization | Variable |

---

## Scoring System

Every job is scored across four dimensions:

| Dimension | Weight | What It Measures |
|---|---|---|
| **Accuracy (TVD)** | 40% | How close the result is to the mathematically correct answer |
| **Authenticity** | 30% | Real QPU or simulator — verified via hardware noise fingerprinting |
| **Speed / Latency** | 20% | Response time against a 60-second hard limit |
| **Robustness** | 10% | Stability under adversarial and edge-case circuits |

**Score → Reward Flow:**
```
Raw scores collected by validators
        ↓
Normalized to percentile rankings (0–1)
        ↓
Composite quality score via weighted dimensions
        ↓
Yuma Consensus — stake-weighted median across all validators
        ↓
TAO emission weights → Miners rewarded proportionally
```

> **Key insight:** A miner attempting to fake 50-qubit results using a classical GPU would need ~4 petabytes of RAM or several hours of computation — far beyond the 60-second evaluation window. Classical fraud is physically impractical at this scale.

---

## Emission Model

| Recipient | Share | Purpose |
|---|---|---|
| **Miners** | 70% | Rewards best quantum execution |
| **Validators** | 20% | Incentivises honest, thorough scoring |
| **Treasury** | 10% | Price stabilisation + development funding |

From Phase 3 onward, users pay fees to submit jobs. A **5% platform fee** goes to the treasury via Chainlink oracle pricing, creating sustainable revenue beyond token emissions.

---

## Getting Started

### Use Quantum Compute

```bash
pip install quasar-sdk qiskit
```

```python
from qiskit import QuantumCircuit
from quasar import QuasarProvider   # Only this line changes vs standard Qiskit

# Create your circuit
qc = QuantumCircuit(2)
qc.h(0)
qc.cx(0, 1)
qc.measure_all()

# Submit to Quasar
provider = QuasarProvider(wallet="your_bittensor_wallet")
backend = provider.get_backend("quasar_auto")
job = backend.run(qc, shots=1024)
result = job.result()

print(result.get_counts())
```

That's it. **One line change** from your existing Qiskit workflow.

---

### Run a Simulator Miner

**Requirements:**
- GPU with 16GB+ VRAM (RTX 3090 or better)
- NVIDIA cuQuantum or Qiskit Aer installed
- Python 3.10+
- Bittensor wallet with minimum stake

```bash
# Clone the repo
git clone https://github.com/quasar-subnet/quasar
cd quasar

# Install dependencies
pip install -r requirements.txt

# Configure your miner
cp config/miner.example.yaml config/miner.yaml
# Edit miner.yaml with your wallet and backend settings

# Register and start mining
python scripts/register_miner.py --config config/miner.yaml
python neurons/miner.py --config config/miner.yaml
```

---

### Run a QPU Miner

For real quantum hardware access — earns a **1.5x reward bonus.**

**Supported providers:**
- IBM Quantum
- IonQ Cloud
- AWS Braket
- Rigetti Cloud

```bash
# Same setup as simulator miner, but configure QPU adapter
cp config/qpu_miner.example.yaml config/qpu_miner.yaml

# Add your QPU provider credentials
export IBM_QUANTUM_TOKEN="your_token_here"
# or
export IONQ_API_KEY="your_key_here"

# Start QPU miner
python neurons/miner.py --config config/qpu_miner.yaml --backend qpu
```

---

### Run a Validator

**Requirements:**
- Dedicated GPU infrastructure
- TAO staked for validator permit
- Python 3.10+

```bash
# Install and configure validator
git clone https://github.com/quasar-subnet/quasar
cd quasar
pip install -r requirements/validator.txt

cp config/validator.example.yaml config/validator.yaml
# Edit with your wallet and stake details

# Start validator node
python neurons/validator.py --config config/validator.yaml
```

Validators earn **base emission + delegation percentage** from TAO holders who stake with them.

---

## Roadmap

| Phase | Timeline | Key Milestones |
|---|---|---|
| **Testnet MVP** | Months 1–2 | Simulator miners only, security audit, TVD < 0.05 accuracy target |
| **QPU Onboarding** | Months 3–5 | IBM + IonQ integrations, hardware authenticity scoring live, 1K Discord members |
| **Inference Market** | Months 6–9 | Public SDK (`pip install quasar-sdk`), Chainlink fee pricing, 5% platform fee |
| **Scale & Ecosystem** | Month 10+ | 100 miners, 10K circuits/day, $10M TVL, pharma + finance verticals |

---

## Key Performance Indicators

| Metric | Month 3 | Month 6 | Year 1 |
|---|---|---|---|
| Active Miners | 20 | 50 | 100 |
| Calculations / Day | 1,000 | 5,000 | 10,000 |
| Subnet Emissions Share | 2% | 10% | 20% |
| Paid TVL | $0 | $1M | $10M |
| Avg Accuracy (TVD) | < 0.05 | < 0.03 | < 0.02 |
| QPU Miners (Real Hardware) | 0 | 10 | 30 |
| Discord Community | 200 | 1,000 | 5,000 |

---

## Security & Fraud Prevention

Quasar's verification architecture makes cheating both physically impractical and economically catastrophic.

| Attack | How It's Stopped |
|---|---|
| **Classical simulation fraud** | RCS circuits at 50+ qubits are classically intractable within 60 seconds |
| **Result caching** | Validators inject random single-qubit rotations into every circuit before dispatch |
| **Distribution copying** | Different circuit variants sent to different miners — cross-correlation detects copies |
| **Validator collusion** | Yuma median aggregation neutralizes outlier validators |
| **Qubit count inflation** | Graduated challenge circuits progressively increase complexity |
| **Latency spoofing** | Circuit randomization prevents pre-computation; anomalous timings are flagged |

> **Physics-Based Security:** Above 50 qubits, it is physically impossible to fake results using a regular computer within 60 seconds. This is not a policy — it is physics.

---

## Architecture

Quasar is built across five layers:

```
┌─────────────────────────────────────────────────────────┐
│              EXTERNAL CLIENTS                           │
│     Developers | Researchers | AI Agents                │
│     (Submit circuits via OpenQASM or Qiskit)            │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│              MINER LAYER                                │
│  QPU Miners (IBM/IonQ/Rigetti) | Simulator Miners       │
│  Execute circuits, return probability distributions     │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│              VALIDATOR LAYER                            │
│  Dispatch challenges, verify results, submit weights    │
│  TVD Check | Noise Fingerprint | Cross-miner Consensus  │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│              SUBTENSOR CHAIN                            │
│  Yuma Consensus | Weight Aggregation | TAO Emission     │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│              EMISSION FEEDBACK                          │
│  TAO flows back to miners and validators                │
└─────────────────────────────────────────────────────────┘
```

**Three Circuit Pools used by Validators:**

| Pool | Contents | Rotation |
|---|---|---|
| Trap Circuit Pool | Bell state, GHZ, QFT — exact known outputs | Weekly |
| Benchmark Pool | Quantum Volume, Mirror Circuits, Clifford benchmarks | Monthly |
| RCS Challenge Pool | Fresh circuits from validator's private random seed | Dynamic |

---

## FAQ

**Can miners fake results?**
No. Above 50 qubits, classical simulation requires ~4 petabytes of RAM or hours of computation — impossible within our 60-second window. Real QPUs also have unique noise fingerprints that simulators cannot replicate.

**Do I need a physics degree to use Quasar?**
No. If you already use Qiskit, you change one line of code. That's it.

**What is TAO?**
The cryptocurrency of the Bittensor network. Miners earn it by executing calculations well. Validators earn it by scoring miners accurately.

**What is Yuma Consensus?**
Bittensor's stake-weighted median voting algorithm. It combines validator scores fairly — one bad actor cannot skew results.

**Is this legal?**
Yes. IBM, IonQ, and others offer commercial API access for programmatic use. Quasar miners are paying customers. Quasar is a coordination layer — the same legal model as any cloud reseller or API aggregator.

**What if quantum hardware goes offline?**
No single point of failure. If IBM has an outage, IonQ miners and GPU simulators continue serving requests. Miners who cannot respond get zero score for that period.

**What is a quantum circuit?**
A set of instructions for a quantum computer — like a recipe. "Put these qubits in superposition, entangle them, then measure." The output is a probability distribution over possible answers.

---

## Glossary

| Term | Meaning |
|---|---|
| **Qubit** | Basic unit of quantum info — can be 0, 1, or both at once |
| **QPU** | Quantum Processing Unit — the actual quantum hardware chip |
| **OpenQASM** | Open Quantum Assembly Language — standard format for quantum circuits |
| **Qiskit** | IBM's open-source Python toolkit for quantum computing |
| **TVD** | Total Variation Distance — measures how accurate a result is. Lower = better |
| **Noise Fingerprint** | The unique error pattern of real quantum hardware — used to detect fakers |
| **RCS** | Random Circuit Sampling — circuits impossible to fake classically above ~50 qubits |
| **Yuma Consensus** | Bittensor's stake-weighted voting algorithm |
| **ZK-SNARK** | Zero-knowledge proof — proves a calculation ran without revealing it |
| **TAO** | Bittensor's native cryptocurrency |

---

## License

Quasar is open source under the **MIT License**.

See [LICENSE](./LICENSE) for full details.

---

## Contributing

We welcome contributions from quantum engineers, blockchain developers, and researchers.

- Open an issue to discuss features or bugs
- Submit a PR against the `dev` branch
- Join the community on Discord

---

## Community

- 🌐 Website: coming soon
- 💬 Discord: coming soon
- 🐦 Twitter: coming soon
- 📄 Docs: coming soon

---

*Quasar — Decentralized Quantum Computing Infrastructure | Built on Bittensor | 2026*
