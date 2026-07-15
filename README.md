# Módulo `warehouse` — WMS avanzado (OPCIONAL, futuro)

> ⚠️ **Repo reservado. Aquí todavía no hay código, y es a propósito.**
> La frontera funcional está decidida (**ADR-0135**, 2026-07-15) pero el diseño del
> módulo (tablas, contratos, comandos) es **columna del humano** y está **bloqueado**
> por los contratos públicos de `inventory` que aún no existen (issues
> [inventory#6/#7/#10](https://github.com/ERPlora/inventory/issues)).

## Qué será

Módulo opcional de WMS avanzado sobre `inventory`. `depends_on: ["inventory"]`:
instalarlo auto-instala inventory (ADR-0060); sin él, inventory basta completo para
una tienda pequeña o un restaurante.

**Regla estructural (ADR-0135):** warehouse opera **solo** mediante los comandos y
eventos públicos de inventory. **Nunca** mantiene un segundo saldo ni un segundo
libro de movimientos, ni toca tablas privadas de otro módulo. Inventory es la única
autoridad del stock; warehouse añade la dimensión física y operativa encima.

## Alcance (del ADR-0135, punto 4)

- Múltiples almacenes, ubicaciones físicas, zonas y bins.
- Transferencias internas con salida/entrada correlacionadas y estado auditable.
- Picking, packing y expediciones.
- Lotes, series y caducidades.
- Recuentos cíclicos por ubicación.
- Reservas y disponibilidad multiubicación.
- Valoración FIFO / coste medio avanzado.
- Stock cross-store y conciliación.

**Transferencias entre hubs = visión condicionada, NO prometida:** requieren una
decisión transversal de sincronización (ADR-0040: los Hub Local no tienen canal entre
sí; en Hub Cloud por organización podría ser viable en la BD compartida). No entran
en el alcance hasta resolver esa arquitectura.

## Por qué no se implementa todavía

1. **Los contratos de los que depende no existen.** El saldo canónico location-ready
   (`location_id` + ubicación predeterminada), el libro de movimientos y el contrato
   idempotente de consumo/reversión se implementan en inventory
   ([#7](https://github.com/ERPlora/inventory/issues/7),
   [#10](https://github.com/ERPlora/inventory/issues/10),
   [#11](https://github.com/ERPlora/inventory/issues/11)). Construir warehouse antes
   obligaría a inventarlos aquí — justo lo que el ADR-0135 prohíbe.
2. **Norte Lean:** la solución vendible (TPV restaurante/peluquería + VeriFactu) no
   necesita WMS. Este módulo se activa cuando un cliente real lo pida.

## Cuándo y cómo se arranca

Orden de desbloqueo: `inventory#6` (modos operativos) → `inventory#7` (ledger
location-ready) → `inventory#10` (decimales/unidades/contrato de consumo) →
**diseño de warehouse** (humano: tablas de almacén/ubicación, máquina de estados de
transferencias, contratos) → implementación por fases contra ese diseño y sus tests.

El trabajo abierto vive en las **Issues de este repo** (epic #1) y en el
[board #3](https://github.com/orgs/ERPlora/projects/3) — no en este fichero.

Referencias: `architecture/00-overview/decision-log.md` (ADR-0135) ·
`architecture/modules/inventory.md` · README de
[`inventory`](https://github.com/ERPlora/inventory).
