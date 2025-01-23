# Getting Started

To get started with Warp, you first need to download the required dependencies, followed by the Warp executable instead.

# Installation

## Warp Dependencies

The main dependencies for Warp are the Rust programming language with `cargo`, as well as the Docker engine.

On UNIX-based systems, you can download and install rust with the following command:

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

For Windows, please refer to the [official website](https://rustup.rs/) for installers, or use WSL2.

To install Docker, follow [the instruction from the official documentation for your operating system](https://docs.docker.com/engine/install/).


## The Warp binary

By far the easiest way to install Warp is by using `cargo` and downloading it from crates.io:

```sh
cargo install cw-warp
```

This will ensure the most recent release. Alternatively, Warp can be download through the official repo.

```sh
cargo install --git https://github.com/cw-warp/warp-cli
```

# Setting up a new project

With the Warp installed, we are ready to start building! First, navigate to a suitable directory. The next step is running the `init` subcommand. This command clones the workspace template and creates the main config file. For this example, we're going to build a Junø Network project with the following command:

```sh
cd ~/projects && warp init hello-warp --chain juno
```

calling `warp init` initializes a new, empty workspace that includes the `shared` library and the `Warp.toml` configuration file. More on that will be covered in a section below.

Next step is scaffolding a new contract into the workspace after changing directory to the newly created workspace. This can be achieved with the following command:

```sh
warp new hi
```

The command above will add the new contract into the workspace and perform a first-time build to check for any errors. Once that is finished, the project is ready and can be opened in the code editor.

```sh
code ~/projects/hello-warp
```

> **Tip:** To initialize the testing framework, you need to `cd` into the `tests/` directory and run `npm i` to load all the required packages.

# Building the workspace

Once you made some changes to the project, you can recompile the `wasm` binaries with either:

```sh
warp build # <- debug binaries
```
or:
```sh
warp build -o # <- optimized, release binaries
```

# Using the testing framework

While contract tests can easily be written in the specific contract's Rust module using the `#[cfg(test)]` attribute, this is not the best way to write tests that require a "live" node environment. That's why the JavaScript testing framework is provided with the workspace. The framework works great in the following scenarios:

- The project requires testing of interactions between multiple contracts
- End-to-end tests of the project
- The contract flow requires performing calls over time (eg. checking how the code behaves across blocks mined)

To create a new test, you can refer to the `tests/src/1-hello.test.ts` file - it contains some example tests. The testing environment provides for you the `consts` field that you can reference in your tests to get things like the COsmWasm client, the signer wallet address, as well as any other values you might need throught the testing process.

As an example, let's look at a custom test:

```ts
it('checks if the chain is up and running', async () => {
    let chainId = await consts.stargateClient.getChainId();
    expect(chainId).to.be.eq("testing");
});
```

# Project deployment

Once the contract is ready, you can start the deployment process. Deployment is controlled by the autodeploy script from the `Warp.toml` config file.

## Deployment configuration

Before deploying to a network it is good practice to set up the configuration properly.

Let's assume that in our scenario we want to deploy to the local Juno testnet (with chain ID `testing`). For that we need to adjust the `[network]` section of the `Warp.toml` config file. To make this process easier, there's a command that automatically populates the config with the correct values for a local testnet:

```sh
warp config set --network local
```

> **Tip:** Other possible values for `--network` are: `testnet` and `mainnet`. This works for all supported chains.

Executing the above command will set the following values:

```toml
[network]
[network]
profile = "juno"
chain_id = "testing"
rpc_url = "http://localhost:26657"
denom = "ujunox"
gas_prices = "0.000001ujunox"
```

## Auto-deploy configuration

The next step is configuring the deployment process itself

```toml;
[autodeploy]
account_id = "dev" # <- this is the account that you have registered in the junod keyring (junod keys list)
make_labels_unique = true # <- Some chains require that each contract deployed has a unique label. It can be disregarded for Juno

[[autodeploy.steps]] # Each contract to deploy is represented with a `steps` entry
id = "$_contr" # <- this defines how the contract is being referred to internally by oither contracts that come after it
contract = "artifacts/contr.wasm" # <- path to the wasm artifact
label = "contr" # <- on-chain contract label
store_only = false # <- whether this contract should be automatically instantiated, or just stored (useful for factories)
migrate_msg = "{}" # <- migration msg to pass to the wasm call
init_msg = '{ "owner": "$account_id", "message": "" }' # <- instantiate message to pass to the wasm call
funds = '10000ujunox' <- # optional
```

After the script is configured, it's just a matter of one short command to execute it against the target network:

```sh
warp deploy
```

And that's it for the basic usage of the Warp Command Line Interfacee! This workflow can be adjusted based on the needs of individual projects, but overall it should cover most requirements throughout the development cycle. More advanced guides can be found in the later sections of this documentation.

Happy developing!