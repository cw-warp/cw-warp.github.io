# Plugin-based Architecture

CosmWasm Warp is designed to support custom functionality through a plugin-based architecture. This allows developers to extend the framework with additional features, optimizations, or custom components without modifying the core codebase.

## The Premise

The main goal of this system is to allow for more customization and flexibility for end-users of Warp - the development teams that create smart contracts and complex DApps for various chains.

It shouldn't be a surprise that the more your project grows, the more complex its requirements become. This is where the plugin architecture shines, enabling teams to adapt and extend Warp to meet their specific needs without waiting for core updates or changes.

## Implementation Details

The plugin system is built around Rust-based libraries. These libraries are required to implement an entrypoint function, as well as plugin metadata that can be read by the core executable.

> Feature under development. Documentation coming soon!
