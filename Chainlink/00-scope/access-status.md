# Estado de accesos de la sesión (verificado 2026-10-16)

Resumen: la sesión cloud (entorno BB) puede clonar repos públicos, bajar release assets de
GitHub y usar registros de paquetes. No puede llegar a Immunefi, docs de Chainlink,
explorers ni a ningún RPC de Ethereum. Web y onchain se cubren con conectores (Firecrawl,
Exa, Blockscout) que corren fuera del contenedor. El RPC para forks de mainnet depende de
que el dueño del entorno abra la red y cargue `ETH_RPC_URL` como secreto.

## Pruebas realizadas

| Destino | Resultado | Uso en el bounty |
|---|---|---|
| git clone github.com (chainlink-ccip, chainlink-evm) | OK | código fuente, historial |
| raw.githubusercontent.com | OK | archivos sueltos |
| release-assets.githubusercontent.com | OK | binarios Foundry, Agave |
| npm, PyPI, crates.io, proxy Go | OK | dependencias, analizadores |
| api.github.com (repos de terceros) | 403 del proxy, filtrado por repo de sesión | releases, issues, advisories: se usa Firecrawl/WebFetch |
| immunefi.com | bloqueado (CONNECT 403) | se lee vía Firecrawl |
| docs.chain.link, chain.link, blog.chain.link | bloqueado | se lee vía Firecrawl/Exa |
| etherscan.io y explorers | bloqueado | Blockscout MCP cubre lecturas de contratos |
| RPC: llamarpc, publicnode, cloudflare, ankr, 1rpc, drpc, infura, alchemy, flashbots, merkle | bloqueados | fork de mainnet pendiente de red + secreto |
| release.anza.xyz, sh.rustup.rs, foundry.paradigm.xyz | bloqueados | se instalan desde release assets de GitHub |

## Toolchain presente

| Herramienta | Versión | Origen |
|---|---|---|
| forge / cast / anvil / chisel | 1.5.1-stable | release asset GitHub, instalado en ~/.foundry/bin |
| Go | 1.24.7 | imagen base |
| Rust / cargo | 1.97.0 | imagen base |
| Node / npm | 22.22.0 / 10.9.4 | imagen base |
| Python | 3.13.16 | imagen base |
| Docker | cliente 29.8.2, sin daemon | no utilizable |
| Solana CLI / Anchor | pendiente | se instala desde release assets de GitHub |

## Secretos de entorno

Ninguna variable `*_RPC_URL`, `ETHERSCAN_API_KEY` ni token de GitHub propio está definida.
El `GITHUB_TOKEN` presente lo inyecta el proxy de la sesión y solo sirve para el repo
adjunto (ingenieria-oss/claudeBB).

## Acciones pendientes del dueño del entorno

Detalladas en el plan aprobado: abrir Network access del entorno BB, cargar `ETH_RPC_URL`
(y opcionales por cadena) como secretos, pegar el setup script de toolchains, y decidir
el canal de entrega (este repo por defecto; Remote Control si se quiere escritura directa
en F:\Claude\Chainlink).

## Consecuencia operativa mientras no haya RPC

- Los PoC se escriben en Foundry sobre despliegue local de los contratos del repo
  (mismo patrón que los tests oficiales). Un PoC de ese tipo es aceptado por Immunefi.
- La verificación de reachability contra despliegues reales (roles, config, versiones
  desplegadas, límites) se hace leyendo mainnet y L2 vía Blockscout, sin transacciones.
- El PoC sobre fork de mainnet se marca como pendiente y se corre en cuanto exista RPC.
