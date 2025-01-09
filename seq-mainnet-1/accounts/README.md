# Setting up an Account

Clone this repository and navigate into the binaries folder according to the mainnet version that you will use:

```bash
git clone https://github.com/fuel-infrastructure/networks
cd networks/seq-mainnet-<N>/binaries
```

Try the right binary according to your system's architecture (`darwin-amd64`, `linux-amd64`, and `linux-arm64` are provided):

```bash
./fuelsequencerd-seq-mainnet-<N>-<ARCH>
```

Generate an address with a key name:

```bash
./fuelsequencerd-seq-mainnet-<N>-<ARCH> keys add <NAME> # for a brand new key

# or

./fuelsequencerd-seq-mainnet-<N>-<ARCH> keys add <NAME> --recover # to create from a mnemonic
```

This will give you an output with an address (e.g. `fuelsequencer1l7qk9umswg65av0zygyymgx5yg0fx4g0dpp2tl`) and a private mnemonic, if you generated a brand new key. Store the mnemonic safely, and send the generated address to `#validators-chat` on Discord, and tag `@miguelsimplystaking` or `@kribz`.

> **Note**: the binaries used here are not necessarily the final binaries that will run the Sequencer network and Sidecar.
