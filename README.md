# Decentralized Pull‑Based GitOps for Edge Clusters

A reference implementation and experimental testbed for a pull‑based GitOps architecture using K3s and Argo CD, designed for resource‑constrained edge nodes with unstable networks.

Edge clusters often operate under severe network instability (high latency, packet loss, intermittent blackouts). Traditional push‑based CI/CD pipelines assume reliable connectivity and a central controller, leading to high deployment failure rates and security concerns. This project implements and evaluates a decentralized, pull‑based GitOps pattern where each edge node autonomously reconciles its state from Git.

## Architecture

- Central Git repository as source of truth.
- Multiple K3s clusters representing edge nodes.
- Each cluster runs its own Argo CD instance that pulls manifests from Git and reconciles locally.
- No inbound ports required from outside; all connections are outbound from the cluster to Git.

## Quickstart

Prerequisites:
- Docker / VMs
- kubectl, helm (if used)
- Git

1. Clone the repo:
   ```bash
   git clone https://github.com/williancb-alt/edge-gitops-argocd-testbed.git
   cd edge-gitops-argocd-testbed
   ```

2. Provision edge clusters (example):
   ```bash
   ./infra/scripts/setup-k3s.sh
   ```

3. Install Argo CD on each cluster:
   ```bash
   ./infra/scripts/install-argocd.sh
   ```

4. Deploy the sample application via GitOps:
   ```bash
   kubectl apply -f clusters/edge-node-1/apps/sample-app/base
   ```

5. Simulate network issues:
   ```bash
   ./experiments/scripts/simulate-packet-loss.sh edge-node-1
   ```

6. Observe Argo CD reconciliation:
   - Open Argo CD UI or use `argocd app get <app-name>`.

## Experiments

See [Experiment Design](docs/experiment-design.md) for details.

Key scenarios:
- Healthy network
- High latency
- Packet loss
- Intermittent blackouts

Results are summarised in `experiments/results/metrics.csv` and visualised in `experiments/results/plots/`.

## Mapping to Dissertation

- Chapter 3 (Design): see `docs/architecture.md` and `docs/diagrams/`.
- Chapter 4 (Implementation): see `infra/`, `clusters/`, and `apps/`.
- Evaluation: see `experiments/` and `docs/experiment-design.md`.
