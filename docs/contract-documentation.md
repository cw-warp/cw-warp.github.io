# Contract Documentation

## Overview

Warp provides powerful tools for generating comprehensive documentation for your CosmWasm smart contracts. This documentation system consists of two main components:

1. **Schema Generation** - Extracts type definitions and interface schemas from your contracts
2. **Interactive Documentation** - Creates visual, interactive API documentation using Swagger UI

These tools help bridge the gap between Web3 smart contract development and traditional enterprise documentation standards, making your contracts more accessible to both technical and non-technical stakeholders.

## The Need for Smart Contract Documentation

In enterprise environments, up-to-date documentation is critical for several reasons:

### 1. Stakeholder Communication
- **Non-technical stakeholders**: Product managers, clients, and investors need to understand what your smart contracts do without diving into code
- **Progress demonstration**: Visual documentation allows you to showcase backend functionality and development progress
- **Quality assurance**: QA teams can interact with contract endpoints in a straightforward, visual manner

### 2. Frontend Integration
- **Developer guidelines**: Frontend teams need clear specifications for integrating with smart contract backends
- **API consistency**: Standardized documentation format familiar to Web2 developers
- **Interactive testing**: Developers can test contract interactions before implementing frontend code

### 3. DApp Development
Smart contracts serve as the backend for decentralized applications (DApps), and like any backend service, they benefit from comprehensive API documentation. Warp brings the familiar Swagger UI experience to the Cosmos ecosystem, leveraging tools that developers already know and trust.

## Schema Generation

The `warp schema` command automatically generates JSON schema files for all contracts in your workspace. These schemas describe the structure and types of your contract's messages and queries.

### How Schema Generation Works

1. **Contract Discovery**: Warp scans your `contracts/` directory for all contract subdirectories
2. **Schema Execution**: For each contract, it runs `cargo run --bin schema` to generate type definitions
3. **Schema Collection**: Generated schemas are collected from each contract's `schema/` directory
4. **Global Aggregation**: All schemas are copied to a global `schema/` directory at the project root

### Usage

```bash
warp schema
```

This command:
- Creates a `schema/` directory in your project root if it doesn't exist
- Generates schema files for each contract (e.g., `my-contract.json`)
- Validates that each contract has a proper schema generation script
- Provides clear feedback on success or failure for each contract

### Schema File Structure

Each generated schema file contains:
- **Instantiate messages**: Parameters required to deploy the contract
- **Execute messages**: All state-changing operations the contract supports
- **Query messages**: All read-only operations for retrieving contract state
- **Type definitions**: Complete type information for all message parameters

## Interactive Documentation Generation

Warp integrates with `cw-swaggy` to transform your contract schemas into interactive, web-based documentation using Swagger UI.

### What is cw-swaggy?

`cw-swaggy` is a specialized tool that:
- Converts CosmWasm contract schemas to OpenAPI 3.0 specifications
- Generates interactive Swagger UI documentation
- Enables direct contract interaction through the browser
- Supports wallet integration for transaction signing

### The Documentation Generation Process

The `warp docgen` command orchestrates a complete documentation pipeline:

1. **Schema Generation**: First runs `warp schema` to ensure all schemas are up-to-date
2. **OpenAPI Conversion**: Processes schemas into a `swagger.json` OpenAPI specification
3. **Interactive Server**: Launches a local web server with Swagger UI interface

### Usage

```bash
warp docgen
```

This command will:
1. Generate schemas for all contracts
2. Create an `openapi.json` file with complete API documentation
3. Start a web server at `http://localhost:8008`
4. Open interactive documentation in your browser

## Step-by-Step Tutorial

Let's walk through setting up documentation for a complete Warp project.

### Prerequisites

Ensure you have:
- Warp CLI installed (`cargo install cw-warp`)
- A Warp workspace with at least one contract
- Rust and Cargo properly configured

### Step 1: Prepare Your Contracts

Make sure each contract in your workspace has a schema generation binary. This is typically included in the contract's `Cargo.toml`:

```toml
[[bin]]
name = "schema"
path = "src/bin/schema.rs"
```

And the corresponding `src/bin/schema.rs` file:

```rust
use cosmwasm_schema::write_api;
use my_contract::msg::{ExecuteMsg, InstantiateMsg, QueryMsg};

fn main() {
    write_api! {
        instantiate: InstantiateMsg,
        execute: ExecuteMsg,
        query: QueryMsg,
    }
}
```

### Step 2: Generate Schemas

From your project root directory, run:

```bash
warp schema
```

You should see output like:
```
✔️  Schema for contract 'my-contract' generated successfully!
✔️  Schema for contract 'another-contract' generated successfully!
```

This creates a `schema/` directory containing JSON files for each contract.

### Step 3: Generate Interactive Documentation

Run the documentation generation command:

```bash
warp docgen
```

This will:
1. Regenerate schemas to ensure they're current
2. Process schemas into OpenAPI format
3. Launch a web server with interactive documentation

### Step 4: Explore Your Documentation

Open your browser to `http://localhost:8008` to see:

- **Contract Overview**: All contracts in your workspace
- **Message Documentation**: Detailed descriptions of all instantiate, execute, and query messages
- **Interactive Testing**: Forms to test contract messages directly
- **Type Definitions**: Complete schema information for all message types
- **Wallet Integration**: Connect with Keplr wallet to send real transactions

### Step 5: Customize and Share

The generated documentation includes:

- **Network Selection**: Switch between mainnet, testnet, and localhost
- **Chain Selection**: Support for multiple Cosmos chains
- **Export Options**: Download OpenAPI specs for integration with other tools
- **Deployment Ready**: Host the documentation on any web server

## Advanced Features

### Custom Port Configuration

Serve documentation on a different port:

```bash
# This is handled internally by warp docgen, but cw-swaggy supports:
# cw-swaggy serve --port 9000 path/to/openapi.json
```

### WASM Binary Integration

If you have optimized WASM binaries (from `cw-optimize`), they can be embedded in the documentation for complete contract interaction:

```bash
# cw-swaggy serve --wasm path/to/contract.wasm path/to/openapi.json
```

### Continuous Documentation

Integrate documentation generation into your CI/CD pipeline:

```bash
# In your GitHub Actions or similar:
warp schema
warp docgen --no-serve  # Generate docs without starting server
```

## Troubleshooting

### Schema Generation Issues

**Problem**: "Error generating schema for contract"

**Solutions**:
- Ensure contract has a `schema` binary defined in `Cargo.toml`
- Verify the schema generation script exists and compiles
- Check that all message types are properly imported

### Documentation Server Issues

**Problem**: Port already in use

**Solution**: 
- Stop any existing documentation servers
- Use a different port if needed
- Check for other applications using port 8008

### Missing Contract Information

**Problem**: Contract doesn't appear in documentation

**Solutions**:
- Verify schema was generated successfully
- Check that the schema file is valid JSON
- Ensure contract name matches directory name

## Integration with Development Workflow

### During Development
- Run `warp schema` after modifying message types
- Use `warp docgen` to verify contract interfaces
- Share documentation URL with frontend developers

### Before Deployment
- Generate final documentation with production-ready schemas
- Export OpenAPI specs for client SDK generation
- Host documentation for stakeholder review

### After Deployment
- Update documentation with deployed contract addresses
- Configure network settings for live contract interaction
- Maintain documentation versions alongside contract versions

The Warp documentation system transforms smart contract development from code-only to a collaborative process where all stakeholders can understand, test, and integrate with your contracts through professional-grade documentation.
