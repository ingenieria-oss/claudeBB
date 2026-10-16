# Decision log

Registro de decisiones no triviales tomadas de forma autónoma. Formato: fecha, decisión, motivo.

| Fecha | Decisión | Motivo |
|---|---|---|
| 2026-10-16 | Entregables en este repo bajo `Chainlink/`, rama `claude/mythos-access-capabilities-x0ixpk` | La sesión cloud no alcanza F:\Claude\Chainlink; el repo deja historial y se trae al PC con `git pull` |
| 2026-10-16 | Foundry 1.5.1 estable instalado desde release assets de GitHub en `~/.foundry/bin` | foundry.paradigm.xyz bloqueado; los release assets sí son alcanzables |
| 2026-10-16 | Clones blobless (`--filter=blob:none`) con historial completo de chainlink-ccip y chainlink-evm en `/home/user/work` | Hace falta historial para delta post-auditoría y `git log -S`; blobless reduce disco y tiempo |
| 2026-10-16 | Scope de Immunefi leído vía Firecrawl, no desde el contenedor | immunefi.com bloqueado por la política de red del entorno |
| 2026-10-16 | Reglas `permissions.allow` en `.claude/settings.json` del repo | Evitar confirmaciones en forge/cast/anvil/git/curl en sesiones futuras; documentación oficial indica que solo lo lee una sesión con un único repo |
| 2026-10-16 | Sin RPC: PoC sobre despliegue local de los contratos del repo y reachability por Blockscout; PoC en fork de mainnet queda pendiente y marcado | Todos los RPC públicos probados están bloqueados y no hay `ETH_RPC_URL` como secreto |
| 2026-10-16 | Chequeo de duplicados añadido como gate obligatorio antes de dar un candidato por válido | Pedido del dueño: verificar en registros públicos que no exista un reporte previo |
| 2026-10-16 | add_repo (read) para chainlink-ccip, chainlink-evm, chainlink, chainlink-solana | Confirmado: lectura git ya servida; API de GitHub sigue filtrada por el proxy; se usa Firecrawl/WebFetch como fallback |
