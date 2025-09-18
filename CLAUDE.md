# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the Managed Service Account addon for Open Cluster Management (OCM), built on the addon-framework. It synchronizes ServiceAccount resources to managed clusters and collects their authentication tokens back to the hub cluster as secret resources.

## Commands

### Development
- `make build` - Build the project (includes generate, fmt, vet, and build-bin)
- `make test` - Run tests (includes manifests, generate, fmt, vet)
- `make fmt` - Format Go code
- `make vet` - Run go vet
- `make generate` - Generate DeepCopy methods
- `make manifests` - Generate CRDs, RBAC, and webhook configurations

### Building
- `make build-bin` - Build binary to bin/msa
- `make build-e2e` - Build e2e test binary to bin/e2e
- `make images` - Build Docker image
- `make images-amd64` - Build AMD64 Docker image

### Testing
- `make test` - Run unit tests with coverage
- `make test-e2e` - Run end-to-end tests
- `make test-integration` - Run integration tests (placeholder)

### Code Generation
- `make code-gen` - Generate clientset, listers, and informers
- `hack/code_gen.sh` - Script for code generation

### Deployment
- `make install` - Install CRDs to cluster
- `make deploy` - Deploy controller to cluster
- `make uninstall` - Remove CRDs from cluster
- `make undeploy` - Remove controller from cluster

## Architecture

The addon follows standard OCM addon architecture with two main components:

### Manager (Hub Side)
- Located in `cmd/manager/` and `pkg/addon/manager/`
- Automatically installs addon agent into managed clusters
- Manages required resources and dependencies

### Agent (Spoke Side)
- Located in `cmd/agent/` and `pkg/addon/agent/`
- Monitors ManagedServiceAccount API resources
- Periodically projects service account tokens as secret resources to hub cluster
- Handles token refresh according to rotation policy

### Key Components
- **APIs**: `apis/authentication/v1alpha1` and `apis/authentication/v1beta1` - Define ManagedServiceAccount CRD
- **Controllers**: `pkg/controllers/` - Event handlers for ServiceAccount and Secret resources
- **Common Controllers**: `pkg/addon/commoncontroller/` - Shared controllers including ephemeral identity controller
- **Generated Code**: `pkg/generated/` - Auto-generated clientset, informers, and listers
- **Features**: `pkg/features/` - Feature gate definitions
- **Utils**: `pkg/util/` - Utility functions including namespace helpers

## Installation Methods

The addon supports two installation approaches:
1. **default (manager - agent)**: Full deployment with both components
2. **addontemplate (only agent)**: Lightweight deployment with agent only

## E2E Testing

E2E tests are organized by feature in `e2e/`:
- `e2e/install/` - Installation tests
- `e2e/token/` - Token management tests
- `e2e/ephemeral_identity/` - Ephemeral identity tests
- `e2e/framework/` - Test framework and utilities

## Configuration

Kubernetes manifests are in `config/`:
- `config/crd/` - Custom Resource Definitions
- `config/manager/` - Manager deployment configuration
- `config/default/` - Default kustomization

Helm charts are in `charts/managed-serviceaccount/`.