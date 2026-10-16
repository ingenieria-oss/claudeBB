# Chainlink — Immunefi bounty workspace

Programa: https://immunefi.com/bug-bounty/chainlink/ (última actualización 13-oct-2026,
25 assets, 36 impactos, Triaged by Immunefi, PoC obligatorio, KYC/KYB, pago en USDC/Ethereum).

Filtro de trabajo: solo Smart Contract High o Critical. Nada se envía a Immunefi sin el
"ok" explícito del dueño del repo. Pruebas únicamente en forks locales; nunca contra
mainnet ni testnet desplegados.

## Reglas de operación

- Autonomía total dentro del bounty: investigación, clonado, instalación en zonas de
  trabajo, commits y push a la rama designada, PoC en forks locales, lecturas onchain,
  entregables, agentes y sesiones auxiliares. No se pide confirmación para nada de esto.
- Se consulta al dueño solo si el PC corre riesgo: borrar o mover archivos fuera de
  F:\Claude\Chainlink y las carpetas de trabajo; tocar software de sistema, servicios o
  ajustes de seguridad; ejecutar en el PC binarios o PoC ajenos a los repos auditados;
  cualquier cosa que roce wallets, claves, keystores o firme transacciones; acciones con
  costo o efecto externo (incluido enviar a Immunefi, que exige "ok" explícito); exponer
  secretos; abrir enlaces de origen desconocido.
- Cada decisión no trivial queda en `00-scope/decision-log.md` con fecha y motivo.

## Estructura

| Carpeta / archivo | Contenido |
|---|---|
| `00-scope/` | Scope-gate: assets, impactos, reglas de exclusión, commits/tags fijados por repo |
| `01-audit-density/` | Inventario datado de auditorías públicas y divulgaciones previas (base para el chequeo de duplicados) |
| `02-delta/` | Delta de código posterior a la última auditoría por subsistema y priorización |
| `03-audit-notes/` | Notas de auditoría por subsistema, con línea de cierre por asset |
| `04-candidates/` | Candidatos High/Critical con PoC, reachability, chequeo de duplicados y veredicto |
| `05-reports/` | Reportes listos para Immunefi (PoC + sugerencia de fix), pendientes de "ok" |

## Entorno de ejecución

Sesión cloud (entorno BB). Foundry 1.5.1 estable. Repos de trabajo clonados en
`/home/user/work/` (no versionados aquí). Estado de accesos y bloqueos documentado en
`00-scope/access-status.md` cuando se genere.

## Cómo traer esto al PC

```
git clone https://github.com/ingenieria-oss/claudeBB F:\Claude\Chainlink
cd F:\Claude\Chainlink
git checkout claude/mythos-access-capabilities-x0ixpk
git pull
```
