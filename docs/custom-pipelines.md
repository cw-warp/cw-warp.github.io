# Custom Pipelines

## Overview

Warp's custom pipeline system allows you to integrate with any blockchain CLI tool or custom deployment workflow beyond the built-in chain profiles. This powerful feature enables:

1. **Custom Chain Integration** - Support for new Cosmos chains not yet included in Warp
2. **Alternative CLI Tools** - Integration with different command-line interfaces and toolchains
3. **Custom Deployment Workflows** - Tailored deployment processes for specific project requirements
4. **Toolchain Flexibility** - Adaptability to evolving blockchain ecosystems and tools

Custom pipelines abstract the underlying CLI commands and provide a standardized interface for Warp to interact with any blockchain infrastructure, making Warp truly chain-agnostic and extensible.

## The Need for Custom Pipelines

### 1. Ecosystem Diversity
The Cosmos ecosystem is rapidly evolving with new chains, each potentially having:
- **Unique CLI tools**: Different command structures and argument patterns
- **Custom features**: Chain-specific functionality not available in standard profiles
- **Modified workflows**: Specialized deployment or interaction patterns
- **Experimental tooling**: Beta or development versions of blockchain tools

### 2. Enterprise Requirements
Organizations often need:
- **Custom deployment processes**: Integration with existing CI/CD pipelines
- **Proprietary tools**: Support for internally developed blockchain tools
- **Modified security models**: Custom key management or signing processes
- **Compliance workflows**: Specialized audit trails or approval processes

### 3. Development Flexibility
Developers benefit from:
- **Tool choice**: Freedom to use preferred CLI tools or versions
- **Rapid prototyping**: Quick integration with new or experimental chains
- **Legacy support**: Continued use of older tool versions when needed
- **Custom optimizations**: Tailored workflows for specific use cases

## How Custom Pipelines Work

Custom pipelines in Warp work by defining a configuration that maps Warp's standard operations to arbitrary CLI commands. The system consists of:

### 1. Pipeline Configuration
A TOML file that defines how to translate Warp operations into CLI commands for your chosen tool.

### 2. Command Mapping
Warp maps its internal operations (store, instantiate, execute, query) to the CLI commands specified in your pipeline configuration.

### 3. Argument Handling
The pipeline system handles the composition of CLI arguments, including transaction parameters, network settings, and authentication details.

### 4. Response Processing
Results from CLI commands are parsed and normalized to provide consistent responses regardless of the underlying tool.

## Pipeline Configuration Structure

A custom pipeline configuration file contains the following key sections:

### Basic Configuration
```toml
[name]
# The display name for your custom pipeline
name = "my-custom-chain"

[config]
# Core CLI executable (e.g., "osmosisd", "gaiad", "custom-cli")
cli_executable = "osmosisd"

# Subcommand for contract operations (usually "wasm")
cli_contract_subcommand = "wasm"

# Base arguments applied to all commands
cli_args = "--output json"

# Additional arguments for transaction commands
cli_args_tx = "--broadcast-mode block --yes"

# Network-specific arguments (node URLs, chain IDs, etc.)
cli_args_network = "--node https://rpc.osmosis.zone:443 --chain-id osmosis-1"

# Arguments specific to contract storage operations
cli_args_store = "--gas auto --gas-adjustment 1.3"
```

### Configuration Fields Explained

- **`cli_executable`**: The main CLI binary (e.g., `osmosisd`, `junod`, `archwayd`)
- **`cli_contract_subcommand`**: Usually `wasm` for CosmWasm operations
- **`cli_args`**: Global arguments applied to all commands (output format, etc.)
- **`cli_args_tx`**: Transaction-specific arguments (gas, broadcast mode, confirmation)
- **`cli_args_network`**: Network connection parameters (RPC endpoints, chain ID)
- **`cli_args_store`**: Additional arguments for contract storage operations

## Step-by-Step Tutorial

Let's create a custom pipeline for a hypothetical new Cosmos chain called "NewChain".

### Prerequisites

Ensure you have:
- Warp CLI installed (`cargo install cw-warp`)
- The target chain's CLI tool installed and configured
- A Warp workspace ready for deployment

### Step 1: Create Pipeline Configuration

Generate a pipeline template:

```bash
warp pipeline create newchain
```

This creates a file named `newchain.toml` with empty configuration fields that you need to fill in.

### Step 2: Configure the Pipeline

Edit the generated `newchain.toml` file:

```toml
name = "newchain"

[config]
cli_executable = "newchaind"
cli_contract_subcommand = "wasm"
cli_args = "--output json"
cli_args_tx = "--broadcast-mode block --yes --gas auto --gas-adjustment 1.3"
cli_args_network = "--node https://rpc.newchain.io:443 --chain-id newchain-1"
cli_args_store = "--from alice"
```

### Step 3: Test the Pipeline

Use your custom pipeline with any Warp command:

```bash
warp --pipeline newchain.toml build
warp --pipeline newchain.toml deploy
```

### Step 4: Validate Configuration

Test basic operations to ensure your pipeline works correctly:

```bash
# Test contract storage
warp --pipeline newchain.toml deploy --store-only

# Test full deployment
warp --pipeline newchain.toml deploy
```

### Step 5: Production Usage

Once validated, you can use your custom pipeline for all Warp operations:

```bash
# Schema generation
warp --pipeline newchain.toml schema

# Documentation generation  
warp --pipeline newchain.toml docgen

# Testing
warp --pipeline newchain.toml test
```

## Real-World Examples

### Example 1: Osmosis Integration

```toml
name = "osmosis"

[config]
cli_executable = "osmosisd"
cli_contract_subcommand = "wasm"
cli_args = "--output json"
cli_args_tx = "--broadcast-mode block --yes --gas auto --gas-adjustment 1.3"
cli_args_network = "--node https://rpc.osmosis.zone:443 --chain-id osmosis-1"
cli_args_store = ""
```

### Example 2: Local Development Chain

```toml
name = "localdev"

[config]
cli_executable = "gaiad"
cli_contract_subcommand = "wasm"
cli_args = "--output json --keyring-backend test"
cli_args_tx = "--broadcast-mode block --yes --gas auto"
cli_args_network = "--node http://localhost:26657 --chain-id testing"
cli_args_store = ""
```

### Example 3: Custom Tool Integration

```toml
name = "custom-deployer"

[config]
cli_executable = "my-custom-tool"
cli_contract_subcommand = "contract"
cli_args = "--format json --verbose"
cli_args_tx = "--confirm --wait-for-confirmation"
cli_args_network = "--endpoint https://my-custom-rpc.com --network mainnet"
cli_args_store = "--optimize"
```

## Advanced Configuration

### Environment Variable Integration

Use environment variables in your pipeline configuration:

```toml
name = "production"

[config]
cli_executable = "archwayd"
cli_contract_subcommand = "wasm"
cli_args = "--output json"
cli_args_tx = "--broadcast-mode block --yes --gas auto"
cli_args_network = "--node $RPC_ENDPOINT --chain-id $CHAIN_ID"
cli_args_store = "--from $DEPLOYER_KEY"
```

### Multiple Network Configurations

Create different pipeline files for different environments:

```bash
# Development
warp --pipeline dev-newchain.toml deploy

# Staging  
warp --pipeline staging-newchain.toml deploy

# Production
warp --pipeline prod-newchain.toml deploy
```

### Custom Authentication

Configure custom key management:

```toml
name = "secure-chain"

[config]
cli_executable = "securechaind"
cli_contract_subcommand = "wasm"
cli_args = "--output json --keyring-backend file"
cli_args_tx = "--broadcast-mode block --yes"
cli_args_network = "--node https://secure-rpc.com --chain-id secure-1"
cli_args_store = "--from secure-deployer --keyring-dir /secure/keys"
```

## Troubleshooting

### Pipeline Not Found

**Problem**: "Pipeline configuration file not found"

**Solutions**:
- Verify the file path is correct
- Ensure the file has `.toml` extension
- Check file permissions

### CLI Executable Issues

**Problem**: "Command not found" or executable errors

**Solutions**:
- Verify the CLI tool is installed and in PATH
- Test the CLI tool independently before using with Warp
- Check the executable name in the pipeline configuration

### Authentication Failures

**Problem**: Transaction signing or key-related errors

**Solutions**:
- Verify key names and keyring configuration
- Test authentication with the CLI tool directly
- Check network connectivity and RPC endpoints

### Invalid Arguments

**Problem**: CLI command fails with argument errors

**Solutions**:
- Test CLI commands manually to verify syntax
- Check the chain's CLI documentation for correct arguments
- Validate argument spacing and formatting in the configuration

## Integration Patterns

### CI/CD Integration

```yaml
# GitHub Actions example
- name: Deploy with Custom Pipeline
  run: |
    warp --pipeline production.toml deploy
  env:
    RPC_ENDPOINT: ${{ secrets.RPC_ENDPOINT }}
    CHAIN_ID: ${{ secrets.CHAIN_ID }}
    DEPLOYER_KEY: ${{ secrets.DEPLOYER_KEY }}
```

### Multi-Chain Deployment

```bash
# Deploy to multiple chains
for pipeline in chains/*.toml; do
  echo "Deploying to $(basename $pipeline .toml)"
  warp --pipeline $pipeline deploy
done
```

### Pipeline Validation Script

```bash
#!/bin/bash
# validate-pipeline.sh
PIPELINE=$1

echo "Validating pipeline: $PIPELINE"

# Test basic CLI connectivity
warp --pipeline $PIPELINE node status

# Test key access
warp --pipeline $PIPELINE config show

echo "Pipeline validation complete"
```