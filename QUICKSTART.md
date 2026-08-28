# Quick Start

This guide provides the basic steps for setting up and inspecting the **Edge-Local Pull-Based GitOps** experimental environment.

The artefact uses **K3d/K3s, Argo CD, Kubernetes, and Kustomize** to provide a reproducible environment for evaluating application-state reconciliation and workload convergence under controlled node and network disruption.

---

## Prerequisites

The following tools are required:

- Docker
- K3d
- kubectl
- Git
- Argo CD CLI

The experimental environment was developed using a local containerised K3s/K3d environment.

> **Note:** The exact software versions and experimental configuration used for the dissertation are documented in the dissertation.

---

## 1. Clone the Repository

Clone the GitHub repository:

```bash
git clone https://github.com/williancb-alt/edge-gitops-argocd-testbed.git
cd edge-gitops-argocd-testbed
```

---

## 2. Create the K3d/K3s Environment

Create the multi-node K3d cluster used for the experimental environment.

After creating the cluster, verify that the Kubernetes nodes are available:

```bash
kubectl get nodes
```

The nodes should be displayed by Kubernetes and report a `Ready` status before continuing.

> **Note:** The cluster configuration used for the dissertation experiments is described in the dissertation's experimental methodology.

---

## 3. Install Argo CD

Install Argo CD into the K3s cluster.

After installation, verify that the Argo CD components are running:

```bash
kubectl get pods -n argocd
```

The Argo CD components should reach a running state before continuing.

---

## 4. Configure the Application

The sample application is located under:

```text
apps/sample-app/
```

The application configuration is organised using Kustomize.

The repository contains a base configuration and an edge-specific overlay:

```text
apps/
└── sample-app/
    ├── base/
    └── overlays/
        └── edge-node-1/
```

The Argo CD application definition is located under:

```text
clusters/edge-node-1/argocd/apps/sample-app.yaml
```

This file defines the application managed by Argo CD and specifies the Git-based desired state used for reconciliation.

---

## 5. Configure Argo CD

Apply the Argo CD application definition:

```bash
kubectl apply -f clusters/edge-node-1/argocd/apps/sample-app.yaml
```

Verify the application:

```bash
argocd app get <sample-app>
```

Replace `<sample-app>` with the application name defined in the Argo CD application manifest.

Argo CD retrieves the application configuration from Git and begins reconciling it with the Kubernetes cluster.

---

## 6. Verify the Deployment

Verify the Kubernetes nodes:

```bash
kubectl get nodes
```

Check the application Deployment:

```bash
kubectl get deployment
```

Check the application Pods:

```bash
kubectl get pods -o wide
```

The `-o wide` option provides additional information about Pod placement, including the Kubernetes node on which each Pod is running.

The Argo CD application can also be checked using:

```bash
argocd app get <sample-app>
```

These observations allow the application state, Pod placement, node status, and Argo CD synchronisation state to be inspected.

---

## 7. Test GitOps Reconciliation

The application configuration is maintained declaratively in the Git repository.

To perform a GitOps configuration-change experiment:

1. Modify the desired application configuration under `apps/sample-app/`.
2. Commit the change:

```bash
git add .
git commit -m "Update application configuration"
```

3. Push the change to the configured Git repository:

```bash
git push
```

4. Allow Argo CD to detect the updated desired state.

5. Monitor the Kubernetes Deployment:

```bash
kubectl get deployment
```

6. Monitor the application Pods:

```bash
kubectl get pods -o wide
```

7. Check the Argo CD application:

```bash
argocd app get <sample-app>
```

Argo CD retrieves the updated desired state from Git and reconciles it with the local Kubernetes environment.

---

## 8. Replica Scaling Experiment

The replica-scaling experiment modifies the desired number of application replicas through the Git-managed configuration.

After modifying the appropriate Kustomize configuration, commit and push the change:

```bash
git add .
git commit -m "Update replica configuration"
git push
```

Monitor the Deployment and Pods:

```bash
kubectl get deployment
kubectl get pods -o wide
```

The experiment observes the transition from the existing replica state to the requested state and the resulting application-state convergence.

---

## 9. Node Degradation Experiment

The experimental evaluation includes a controlled node/network degradation scenario.

A selected Kubernetes node is subjected to simulated connectivity disruption.

During the experiment, monitor the Kubernetes nodes:

```bash
kubectl get nodes -w
```

Monitor Pod placement:

```bash
kubectl get pods -o wide -w
```

The resulting node condition and workload placement can then be observed.

---

## 10. Deployment Change During Node Disruption

The principal combined experiment introduces a Git-based application configuration change while a Kubernetes node is experiencing simulated network disruption.

The experiment observes:

- Argo CD synchronisation
- Kubernetes node status
- Deployment state
- Pod placement
- Workload rescheduling
- Application-state convergence

During the experiment, monitor:

```bash
kubectl get nodes -w
```

and:

```bash
kubectl get pods -o wide -w
```

The Argo CD application can be monitored using:

```bash
argocd app get <sample-app>
```

The purpose of the experiment is to determine whether the requested application state is achieved under the defined node/network disruption condition.

---

## 11. Inspect the Final Application State

The following commands can be used to inspect the final state of the experimental environment.

### Kubernetes Nodes

```bash
kubectl get nodes
```

### Deployment

```bash
kubectl get deployment
```

### Pods

```bash
kubectl get pods -o wide
```

### Argo CD Application

```bash
argocd app get <sample-app>
```

These observations provide evidence of node status, workload placement, Deployment state, Argo CD synchronisation, and application-state convergence.

---

## Scope and Limitations

This artefact evaluates an **edge-local pull-based GitOps workflow** under defined and controlled experimental conditions.

The architecture remains dependent on connectivity to the upstream Git repository when new desired-state revisions need to be retrieved.

The experiments do not establish GitOps behaviour during prolonged complete disconnection from the upstream Git repository.

The experimental environment uses containerised K3d/K3s nodes and a lightweight baseline workload. Consequently, measured timings and recovery behaviour should be interpreted as observations from the experimental environment rather than universal production performance measurements.

---

## Repository Structure

```text
edge-gitops-argocd-testbed/
│
├── README.md
├── QUICKSTART.md
│
├── apps/
│   └── sample-app/
│       ├── base/
│       └── overlays/
│           └── edge-node-1/
│
└── clusters/
    └── edge-node-1/
        └── argocd/
            └── apps/
                └── sample-app.yaml
```

For an overview of the project, architecture, experimental scope, and relationship to the dissertation, see `README.md`.
