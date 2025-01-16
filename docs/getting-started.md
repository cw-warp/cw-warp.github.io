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

With the Warp installed, we are ready to start building! First, navigate to a suitable directory. The next step is running the `init` subcommand. This command clones the workspace template and creates the main config file. For this example, we're going to build a Secret Network project with the following command:

```sh
cd ~/projects && warp init hello-warp --chain scrt
```

Next step is scaffolding a new contract into the workspace with the following command:

```sh
warp new hi
```

The command above will add the new contract into the workspace and perform a first-time build to check for any errors. Once that is finished, the project is ready and can be opened in the code editor.

```sh
code ~/projects/hello-warp
```