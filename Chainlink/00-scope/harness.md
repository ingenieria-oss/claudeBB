# Harness de pruebas (reproducible)

Cómo se compila y prueba el código en scope en esta sesión y cómo repetirlo en el PC.
Todo se hace sobre clones locales; nunca contra despliegues reales.

## CCIP EVM (chainlink-ccip, tag contracts-ccip-v2.0.0)

| Dato | Valor |
|---|---|
| Repo | https://github.com/smartcontractkit/chainlink-ccip |
| Tag auditado | `contracts-ccip-v2.0.0` → commit `c2c125c27f056db2e98d21501922b6eff5750f36` (2026-06-17) |
| Raíz Foundry | `chains/evm` (config en `chains/evm/foundry.toml`) |
| Perfil de tests | `FOUNDRY_PROFILE=ccip` (optimizer_runs 1, sin via-ir) |
| Perfil de release | `ccip-compile` (optimizer_runs 80000, via-ir) |
| solc | 0.8.26, evm_version paris |
| Foundry probado | 1.5.1-stable |

Dependencias Solidity que exige `remappings.txt` (todas bajo `chains/evm/node_modules`):

| Remapping | Paquete | Versión |
|---|---|---|
| `forge-std/` | github foundry-rs/forge-std | v1.16.1 (commit 620536f) |
| `@chainlink/contracts/` | npm @chainlink/contracts | 1.5.0 |
| `@chainlink/policy-management/` | npm @chainlink/ace (packages/policy-management/src) | 1.0.0 |
| `@openzeppelin/contracts@4.8.3` | npm @openzeppelin/contracts | 4.8.3 |
| `@openzeppelin/contracts@5.3.0` | npm @openzeppelin/contracts | 5.3.0 |

En un entorno con red normal basta `pnpm install --frozen-lockfile` dentro de `chains/evm`.
En esta sesión `codeload.github.com` está bloqueado, así que la instalación se hizo a mano
con el mismo resultado:

```bash
cd chains/evm
mkdir -p node_modules/@chainlink node_modules/@openzeppelin
git clone --depth 1 --branch v1.16.1 https://github.com/foundry-rs/forge-std node_modules/forge-std
for spec in @chainlink/contracts@1.5.0 @chainlink/ace@1.0.0 @openzeppelin/contracts@4.8.3 @openzeppelin/contracts@5.3.0; do
  npm pack "$spec" --pack-destination /tmp/pk
done
x() { mkdir -p "$2" && tar xzf "$1" -C "$2" --strip-components=1; }
x /tmp/pk/chainlink-contracts-1.5.0.tgz        node_modules/@chainlink/contracts
x /tmp/pk/chainlink-ace-1.0.0.tgz              node_modules/@chainlink/ace
x /tmp/pk/openzeppelin-contracts-4.8.3.tgz     node_modules/@openzeppelin/contracts-4.8.3
x /tmp/pk/openzeppelin-contracts-5.3.0.tgz     node_modules/@openzeppelin/contracts-5.3.0
```

solc: `binaries.soliditylang.org` también está bloqueado; el binario se tomó del release
oficial de GitHub y se dejó donde Foundry lo busca:

```bash
mkdir -p ~/.svm/0.8.26
curl -sSL -o ~/.svm/0.8.26/solc-0.8.26 https://github.com/ethereum/solidity/releases/download/v0.8.26/solc-static-linux
chmod +x ~/.svm/0.8.26/solc-0.8.26
export FOUNDRY_OFFLINE=true
```

Compilar y probar:

```bash
cd chains/evm
FOUNDRY_PROFILE=ccip forge build          # 516 archivos, ~6 min en 4 vCPU
FOUNDRY_PROFILE=ccip forge test           # suite completa
FOUNDRY_PROFILE=ccip forge test --match-path 'contracts/test/offRamp/**'
```

Los PoC de este trabajo se escriben como tests Foundry dentro de `contracts/test/` del
worktree, reutilizando los `Setup` del repo, y se copian a `Chainlink/04-candidates/` o
`Chainlink/05-reports/` con la ruta exacta donde deben pegarse para reproducir.

## CCIP Solana (chainlink-ccip, chains/solana/contracts)

| Dato | Valor |
|---|---|
| Anchor | 0.29.0 (`Anchor.toml`) |
| solana-program en Cargo.lock | 1.17.25 |
| Toolchain instalado aquí | Solana CLI 1.18.26 (release de GitHub anza-xyz/agave), Anchor CLI 0.29.0 (npm @coral-xyz/anchor-cli) |
| Tests | `anchor build` y `anchor test` (levanta `solana-test-validator` local) |

## chainlink-evm (develop/contracts)

Perfiles Foundry por producto en `contracts/foundry.toml`: `vrf` (0.8.6 / 0.8.19),
`automation-cre` (0.8.24), `l2ep` (0.8.24), `llo-feeds` (0.8.19), `operatorforwarder`
(0.8.19), `data-feeds` (0.8.26), `shared` (0.8.26), `payments` (0.8.26),
`data-feeds-v0_6` (0.6.6). Los binarios de solc se precargan en `~/.svm/<versión>/` con el
mismo método. Pendiente de completar cuando termine el mapa del repo.

## Lo que falta para PoC sobre fork de mainnet

Un RPC de archivo (`ETH_RPC_URL`) y red abierta hacia él. Con eso:

```bash
anvil --fork-url "$ETH_RPC_URL" --fork-block-number <bloque_fijo>
forge test --fork-url http://127.0.0.1:8545 --match-test test_PoC_*
```
