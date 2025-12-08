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

### hello.yaml

Minimal "Hello World" example for GB200.

**Description:**
- Simple pod that prints GPU information and runs a basic PyTorch test
- Uses 1x GB200 GPU
- Verifies CUDA availability and GPU access
- Quick sanity check for cluster setup

**Usage:**
```bash
kubectl apply -f hello.yaml
kubectl logs -l app=hello-gb200 -f
kubectl delete job hello-gb200
```

### finetune_gpt_oss.yaml

Fine-tune GPT-OSS 20B model with LoRA on a single GPU.

**Description:**
- Fine-tunes OpenAI's GPT-OSS 20B model using LoRA (Low-Rank Adaptation)
- Uses 1x GB200 GPU with MXFP4 quantization
- Dataset: HuggingFaceH4/Multilingual-Thinking
- LoRA config: r=16, alpha=32, targets q_proj and v_proj
- Training: 3 epochs, batch size 4, gradient accumulation 4
- Logs to Weights & Biases
- Saves fine-tuned model to /workspace/output/final_model

**Usage:**
```bash
# Set environment variables
export HF_TOKEN=your_huggingface_token
export WANDB_API_KEY=your_wandb_api_key

# Launch job
envsubst < finetune_gpt_oss.yaml | kubectl apply -f -

# Monitor
kubectl get job finetune-gpt-oss
kubectl logs -l job-name=finetune-gpt-oss -f

# Delete when done
kubectl delete job finetune-gpt-oss
```

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