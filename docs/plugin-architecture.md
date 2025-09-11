# Plugin-based Architecture

CosmWasm Warp is designed to support custom functionality through a plugin-based architecture. This allows developers to extend the framework with additional features, optimizations, or custom components without modifying the core codebase.

## The Premise

The main goal of this system is to allow for more customization and flexibility for end-users of Warp - the development teams that create smart contracts and complex DApps for various chains.

It shouldn't be a surprise that the more your project grows, the more complex its requirements become. This is where the plugin architecture shines, enabling teams to adapt and extend Warp to meet their specific needs without waiting for core updates or changes.

## Customizable Components

Warp's architecture aims to make several key components of the framework pluggable. This includes the following areas:

- ***[Custom Pipelines](custom-pipelines.md)*** - Define your own build and deployment pipelines to suit your project's needs. This could define custom chains, build steps, configuration, or deployment strategies in conjunction with the Warp.toml workspace configuration file.
- ***Plug-in hooks*** (In Development) - Introduce custom logic at various stages of the development process (see available commands in help output). This could include pre-build checks, post-deployment actions, or custom validation steps.
