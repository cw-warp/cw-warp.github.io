# Warp CLI - Introduction

Warp is a command-line tool suite for the CosmWasm Smart Contract platform. It allows developers to prototype and quickly iterate on smart contract projects. It provides an out-of-the box set of commands to build, test, deploy, and customize the development pipeline, as well as integration with a TypeScript-based testing framework based around TS-Mocha

Warp takes advantage of Docker images to streamline the process of building WASM packages, as well as running local node environments for supported blockchains. 

# The Workspace model

The way Warp handles projects stays in sync with how Rust works - a new project is a Rust Workspace with the following directories included:

- `contracts/` - contains all the contracts
- `packages/` - contains packages used by the contracts, but not being built into WASM contract packages. Also contains the `shared` package.
- `tests/` - the TypeScript directory with the testing framework

Additionally, in the root of the project, there is the configuration file named `Warp.toml`. This file contains workspace-specific config that modifies the way Warp works. It also contains keeps track of all the contracts in the project.

## Shared package

By design, the main `contracts` folder doesn't store the `msg.rs` files of contracts. Instead, all the message definitions for each contract are stored in a `shared` package which is referenced by each contract in the workspace. 

This allows all contracts to communicate seamlessly with each other. It also minimizes error potential when making changes to contract messages, since all changes instantly produce feedback in the form of errors across all contracts.

This comes with its own additional considerations, mainly the need to create separate storage structs on per-contract basis and provide manual conversions to messages, if the contract design relies on these.

More information on the trade-offs of this implementation will be described in the more in-depth part of the documentation.