# Gen AI SDLC - Kubernetes Maintenance PoC

This repository provides a **Proof of Concept (PoC)** for using **K8sGPT** in a **Gen AI-powered software development lifecycle (SDLC) maintenance** workflow. The PoC demonstrates how to identify and troubleshoot Kubernetes issues using AI-powered analysis.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Step 1: Install Kind](#step-1-install-kind)
  - [Step 2: Install K8sGPT](#step-2-install-k8sgpt)
- [Authenticate AI Backend](#authenticate-ai-backend)
  - [OpenAI](#openai)
  - [Amazon Bedrock](#amazon-bedrock)
- [Deploy a Broken Pod](#deploy-a-broken-pod)
- [Analyze Issues with K8sGPT](#analyze-issues-with-k8sgpt)
- [Security Considerations](#security-considerations)
- [Contributing](#contributing)
- [License](#license)

## Overview

This project sets up a Kubernetes cluster, installs **K8sGPT**, deploys a faulty pod, and uses **AI-backed analysis** to diagnose issues. The workflow supports **OpenAI** and **Amazon Bedrock** as AI backends.

## Prerequisites

Before proceeding, ensure you have the following installed:

- **Docker** (required for running Kind)
- **Homebrew** (for macOS users)
- **kubectl** (to interact with the Kubernetes cluster)
- **AWS CLI** (for Amazon Bedrock authentication)
- **An OpenAI API key** (if using OpenAI backend)
- **Valid AWS credentials** (if using Amazon Bedrock)

## Installation

### Step 1: Install Kind

```sh
brew install kind
kind create cluster --name k8sgpt-demo
```

### Step 2: Install K8sGPT

```sh
brew tap k8sgpt-ai/k8sgpt
brew install k8sgpt
```

## Authenticate AI Backend

Before analyzing Kubernetes issues, authenticate with your preferred AI backend.

### OpenAI

```sh
k8sgpt generate
k8sgpt auth add --backend openai --model gpt-3.5-turbo
```

### Amazon Bedrock

Authenticate your AWS environment:

```sh
aws configure
```

Then, add Bedrock authentication:

```sh
k8sgpt auth add --backend amazonbedrock --model anthropic.claude-v2
```

## Deploy a Broken Pod

Create a pod with an invalid image and incorrect liveness probe settings:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: broken-pod
  namespace: default
spec:
  containers:
    - name: broken-pod
      image: nginx:1.a.b.c  # Invalid image
      livenessProbe:
        httpGet:
          path: /
          port: 81  # Incorrect port
        initialDelaySeconds: 3
        periodSeconds: 3
```

Apply the configuration:

```sh
kubectl apply -f broken-pod.yml
```

## Analyze Issues with K8sGPT

Run K8sGPT analysis to diagnose the broken pod:

```sh
export AI_BACKEND=amazonbedrock  # or openai
k8sgpt auth list  # Verify available AI backends
k8sgpt analyze --explain --backend $AI_BACKEND
```

## Security Considerations

- **Avoid hardcoding API keys**: Use a secrets manager or environment variables.
- **Limit AI model access**: Configure IAM roles for AWS Bedrock instead of static credentials.
- **Monitor AI-generated suggestions**: Verify AI-based insights before applying changes.

## Contributing

Contributions are welcome! Feel free to open an issue or submit a pull request.

## License

This project is licensed under the MIT License. See `LICENSE` for details.
