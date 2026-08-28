# Quick Start Guide

This guide provides the basic steps required to set up the Edge-Local Pull-Based GitOps experimental testbed.

## Prerequisites

Install the following tools before starting:

- Docker
- K3d
- kubectl
- Git
- Helm (if required by the installation scripts)

The testbed was developed as a containerised Kubernetes environment and is intended for research and experimental use.

---

## 1. Clone the Repository

```bash
git clone https://github.com/williancb-alt/edge-gitops-argocd-testbed.git
cd edge-gitops-argocd-testbed
```

## 2. Provision the K3s/K3d Test Environment

Run:

```bash
./infra/scripts/setup-k3s.sh
```

Verify the cluster:

```bash
kubectl get nodes
```

## 3. Install Argo CD

Run:

```bash
./infra/scripts/install-argocd.sh
```

Verify the Argo CD components:

```bash
kubectl get pods -n argocd
```

## 4. Deploy the Sample Application

The application configuration is maintained using Kubernetes manifests and Kustomize.

Apply the appropriate configuration from the repository:

```bash
kubectl apply -k clusters/edge-node-1/apps/sample-app/base
```

Verify the Deployment and Pods:

```bash
kubectl get deployment
kubectl get pods -o wide
```

## 5. Verify Argo CD Synchronisation

Inspect the application:

```bash
argocd app get <app-name>
```

Argo CD retrieves the desired application configuration from Git and reconciles it with the Kubernetes cluster.

## 6. Test GitOps Reconciliation

Modify the application configuration in the Git repository. For example, change the requested replica count and commit the change.

Once the new revision is available to Argo CD, monitor the resulting state:

```bash
kubectl get deployment
kubectl get pods -o wide
```

## 7. Observe Node and Workload State

Check node status:

```bash
kubectl get nodes
```

Check workload placement:

```bash
kubectl get pods -o wide
```

## 8. Run the Degradation Experiment

The experiment scripts are located in:

```text
experiments/scripts/
```

Follow the procedure in:

```text
docs/experiment-design.md
```

The principal combined scenario introduces a Git-based application configuration change while a Kubernetes node is experiencing simulated network disruption.

Monitor:

```bash
kubectl get nodes
kubectl get pods -o wide
kubectl get deployment
argocd app get <app-name>
```

These commands allow node status, workload placement, Deployment state, Argo CD synchronisation, and application-state convergence to be observed.

## 9. Experimental Results

Recorded results are stored under:

```text
experiments/results/
```

Where applicable:

```text
experiments/results/metrics.csv
experiments/results/plots/
```

See `docs/results.md` for a summary of the experimental observations.

---

## Important Scope Note

This testbed evaluates an edge-local pull-based GitOps workflow under defined and controlled experimental conditions.

The architecture remains dependent on upstream Git connectivity when new desired-state revisions need to be retrieved. The experiments do not establish GitOps behaviour during prolonged complete disconnection from the upstream Git repository.

Measured timings are specific to the K3d/K3s environment and lightweight workload used in the research and should not be interpreted as universal production performance values.

---

## Troubleshooting

### Kubernetes nodes are not Ready

```bash
kubectl get nodes
kubectl describe node <node-name>
```

### Pods are not running

```bash
kubectl get pods -o wide
kubectl describe pod <pod-name>
```

### Argo CD is not synchronising

```bash
argocd app get <app-name>
kubectl get pods -n argocd
```

---

## Further Documentation

See:

- `README.md`
- `docs/architecture.md`
- `docs/experiment-design.md`
- `docs/results.md`
