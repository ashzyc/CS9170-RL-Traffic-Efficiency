# CS9170: Optimizing Urban Traffic Efficiency with Reinforcement Learning

## Problem Definition:
Traditional fixed-time traffic signals are inefficient. The goal is to use RL to dynamically control traffic light timing at intersections based on real-time queue lengths and waiting times, minimizing overall vehicle delay and congestion.

- **State**: Traffic conditions (vehicle counts, waiting vehicles, direction, signal phase)
- **Action**: Traffic light phase (e.g., North-South vs East-West green)
- **Reward**: Negative congestion (based on vehicle count and waiting time)

---

## Quick Start

### CityFlow
```bash
# Step 1 — Install Python dependencies
pip install -r requirements.txt

# Step 2 — Install CityFlow (one-time, handles cmake + build automatically, ~1 min)
bash install_cityflow.sh

# Step 3 — Run full experiment (Fixed baseline + DQN + PPO + plots)
bash run_cityflow.sh
```

### SUMO
```bash
# Step 1 — Install Python dependencies
pip install -r requirements.txt

# Step 2 — Download and install the macOS package (one-time, ~5–15 min)
# https://sumo.dlr.de/docs/Downloads.php  →  macOS  →  download .pkg  →  double-click to install

# Step 3 — Add SUMO to your shell (add to ~/.zshrc, then restart terminal)
# Replace X.XX.X with the version you installed (check /Library/Frameworks/EclipseSUMO.framework/Versions/)
export SUMO_HOME="/Library/Frameworks/EclipseSUMO.framework/Versions/Current/EclipseSUMO"
export PATH="$PATH:$SUMO_HOME/bin"

# Step 4 — Run full experiment (auto-generates network on first run)
bash run_sumo.sh
```

### Both simulators
```bash
bash run_all.sh
```

Optional flags (work on all three scripts):
```bash
bash run_all.sh --episodes 500   # more training
bash run_all.sh --skip-sumo      # CityFlow only
bash run_all.sh --skip-cityflow  # SUMO only
```

Results and plots are saved to `results/`.

### Viewing Results

View results for a single method after training:
```bash
python3 training/plot_results.py --method dqn --sim cityflow
python3 training/plot_results.py --method ppo --sim cityflow
python3 training/plot_results.py --method dqn --sim sumo
python3 training/plot_results.py --method ppo --sim sumo
```

Compare all methods on one plot:
```bash
python3 training/compare_results.py
```

### Showcase (Best for Demo / Presentation)

Run all visualization outputs in one go (metrics + phase timeline + optional live GUI demo):
```bash
bash run_showcase.sh                     # default: sumo
bash run_showcase.sh --sim cityflow      # cityflow version
bash run_showcase.sh --episodes 20       # more stable evaluation metrics
bash run_showcase.sh --no-demo           # only generate plots, no GUI
bash run_showcase.sh --gui-settings data/sumo/gui.settings.xml
```

Note: if SUMO-GUI does not pop up inside Cursor terminal, run demo commands in macOS Terminal.app / iTerm.

Direct commands (if you want each artifact separately):
```bash
# 1) Quantitative metrics comparison (reward / queue / throughput)
python3 training/evaluate.py --sim sumo --episodes 10

# 2) Signal phase decision timeline (fixed vs dqn vs ppo)
python3 training/visualize_phases.py --sim sumo

# 3) Live RL control in SUMO-GUI (most intuitive)
python3 training/demo.py --method dqn --sim sumo --delay 200
python3 training/demo.py --method ppo --sim sumo --delay 200
python3 training/demo.py --method dqn --sim sumo --delay 200 --gui-settings data/sumo/gui.settings.xml
```

Generated files:

Training (`run_cityflow.sh` / `run_sumo.sh`):
- `results/dqn_{sim}_rewards.txt` / `results/dqn_{sim}_losses.txt`
- `results/ppo_{sim}_rewards.txt` / `results/ppo_{sim}_losses.txt`
- `results/fixed_{sim}_rewards.txt`
- `results/dqn_{sim}_plot.png` — reward + loss curves for DQN
- `results/ppo_{sim}_plot.png` — reward + loss curves for PPO
- `results/comparison_{sim}.png` — reward comparison across all methods
- `results/comparison_rewards.png` / `results/comparison_losses.png` — cross-simulator comparison
- `checkpoints/dqn_{sim}_best.pt` / `checkpoints/ppo_{sim}_best.pt` — saved model weights

Showcase (`run_showcase.sh`):
- `results/evaluation_{sim}.json` — raw metrics (reward, queue, throughput) per method
- `results/evaluation_{sim}.png` — bar chart comparing fixed / DQN / PPO
- `results/phase_timeline_{sim}.png` — signal phase decisions over time

---

## Methodology

### Simulation Environment
- **CityFlow** 
- **SUMO (Simulation of Urban MObility)**

### RL Model
- **Deep Q-Network (DQN)**
- **Proximal Policy Optimization(PPO)**

### Training
- Epsilon-greedy exploration
- Gradient clipping for stability
- Reward scaling for better convergence

---

