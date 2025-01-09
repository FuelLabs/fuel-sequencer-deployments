# Setting up an Account

Obtain binary from the releases according to the mainnet version that you will use:

- https://github.com/FuelLabs/fuel-sequencer-deployments/releases

Download and try the right binary according to your system's architecture:

```bash
./fuelsequencerd-seq-mainnet-<N>-<ARCH> version
```

Generate an address with a key name:

```bash
./fuelsequencerd-seq-mainnet-<N>-<ARCH> keys add <NAME> # for a brand new key

# or

./fuelsequencerd-seq-mainnet-<N>-<ARCH> keys add <NAME> --recover # to create from a mnemonic
```

This will give you an output with an address (e.g. `fuelsequencer1l7qk9umswg65av0zygyymgx5yg0fx4g0dpp2tl`) and a private mnemonic, if you generated a brand new key. Store the mnemonic safely, and send the generated address to `#validators-chat` on Discord, and tag `@miguelsimplystaking` or `@kribz`.

> **Note**: the binaries used here are not necessarily the final binaries that will run the Sequencer network and Sidecar.
