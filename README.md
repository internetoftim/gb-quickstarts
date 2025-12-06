# gb-quickstarts

Quick start examples for training on GB200 GPUs.

## Prerequisites

- Kubernetes cluster with p6e-gb200.36xlarge nodes (GB200 GPUs)
- kubectl access to the cluster
- Weights & Biases account and API key

## Setup

1. Create a `.env` file with your W&B API key:
```bash
WANDB_API_KEY=your_api_key_here
```

2. Make sure you have access to the Kubernetes cluster with GB200 nodes

## Examples

### train-simple.yaml

Single-GPU toy training sample for GB200.

**Description:**
- Trains a simple MLP on synthetic data to demonstrate GB200 capabilities and W&B logging
- Uses 1x GB200 GPU with bfloat16 mixed precision (optimized for tensor cores)
- Synthetic dataset: 8192 samples, 4096-dim input/output
- Model: SimpleMLP (4096→8192→4096)
- Logs metrics to Weights & Biases (batch loss, epoch loss, throughput)
- Expected throughput: ~7,700 samples/sec

**Usage:**

1. Substitute your W&B API key in the YAML:
```bash
export WANDB_API_KEY=your_api_key_here
envsubst < train-simple.yaml | kubectl apply -f -
```

2. Monitor the job:
```bash
# Check job status
kubectl get job train-gb200-simple

# Stream logs
kubectl logs -l job-name=train-gb200-simple -f

# Delete job when done
kubectl delete job train-gb200-simple
```