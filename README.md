# spot-contracts-abis

ABIs and deployed addresses for the spot-router smart contracts supporting spot
trading on the Arcus DEX.

## Network

| Field       | Value                   |
|-------------|-------------------------|
| Network     | Robinhood mainnet       |
| Chain ID    | `4663`                  |

There is no public RPC for this network — supply your own endpoint.

## Deployed contracts

Integrate against the **address** column. Contracts marked ERC-1967 are deployed
behind upgradeable proxies — bind to the proxy address.

| Contract | Address (integrate here) | Proxy | Purpose |
|----------|--------------------------|-------|---------|
| SwapShell | `0x4262efBd176F02824af27010bEa218429c33c7E8` | ERC-1967 | Entry-point router wrapper for all swaps. |
| ArcusSettlement | `0x006102b16A04c20306A28b652745D3973D7D24fa` | ERC-1967 | Settlement + balance-delta accounting. |
| RfqExecutor | `0xf4da3c42D9c9F494d4688A0112a9DB3C9b18a914` | none | Ordinary RFQ executor. |
| WrappedTokenRfqExecutor | `0x43cF43056C33128329B54F66CA3649CF2975f1A6` | ERC-1967 | RFQ executor for wrapped-token mints. |
| WrappedTokenFactory | `0x8bc71aE8EaC8B25F30c2990930Cc3A80E72e169e` | ERC-1967 | Deploys wrapped-token BeaconProxy instances. |
| WrappedTokenEscrow | `0x6d56Ab475069B7E93886b3D3F06c5435B87Ba158` | ERC-1967 | Wrapped-token settlement state / deadlines. |
| WrappedToken | *(per-token BeaconProxy)* | Beacon | Interface for every wrapped token — see below. |
| WrappedTokenBeacon | `0x27fEB332759F8d2f351D7fC72D29af37664ffd77` | none | UpgradeableBeacon for all wrapped tokens. |

A machine-readable version of this table — including the implementation address
behind each proxy — is in [`deployments.json`](./deployments.json).

### Wrapped tokens

Each wrapped token is an OpenZeppelin `BeaconProxy` deployed by
`WrappedTokenFactory` at a `CREATE2`-predicted address, all pointing at the
`WrappedTokenBeacon` above. There is no single wrapped-token address — bind
`abis/WrappedToken.json` to whichever wrapped-token address you are working with.

## Usage examples

### viem

```ts
import { createPublicClient, http, getContract } from "viem";
import abi from "./abis/SwapShell.json";

const client = createPublicClient({ transport: http(RPC_URL) });
const swapShell = getContract({
  address: "0x4262efBd176F02824af27010bEa218429c33c7E8",
  abi,
  client,
});
```

### ethers v6

```ts
import { Contract, JsonRpcProvider } from "ethers";
import abi from "./abis/SwapShell.json" assert { type: "json" };

const provider = new JsonRpcProvider(RPC_URL);
const swapShell = new Contract(
  "0x4262efBd176F02824af27010bEa218429c33c7E8",
  abi,
  provider,
);
```

## Layout

```
abis/               # one <Contract>.json per contract — a raw ABI array
deployments.json    # network + per-contract address/proxy/implementation manifest
```
