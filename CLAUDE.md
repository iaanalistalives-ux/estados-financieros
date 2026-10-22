# Estados Financieros — LIVES

Las reglas generales están en el CLAUDE.md del repo `lives-instrucciones`. Aquí solo lo propio de esta herramienta.

## Qué hace
Dashboard de gerencia con el Balance General y el Estado de Resultados por función, mes a mes (en soles). Una sola pantalla: `index.html`.
- Barra: mes, Mes / Acumulado del año, aviso "Balance no cuadra · S/ X" (lleva a Control del cierre), Descargar Excel, Imprimir, 🌙.
- KPIs: ventas netas, margen bruto, utilidad de operación, utilidad del ejercicio, efectivo al cierre.
- Pestaña Resultados: cascada de ventas a utilidad, márgenes por mes, estado de resultados, ventas y costo, ingresos y gastos financieros. Si hay más de un año cargado aparece el comparativo de ventas en líneas por año.
- Pestaña Balance y caja: liquidez, prueba ácida, pasivo ÷ patrimonio, días de cobro, inventario y pago, ciclo de caja, composición del activo y del financiamiento, balance general, efectivo/clientes/inventarios.
- Pestaña Control del cierre (para Contabilidad): cuadre, cuenta 99 = utilidad, totales impresos = suma de cuentas, líneas del estado de resultados, depreciación en el pasivo, cuenta 45, cuentas sin movimiento, margen del mes. Cada control se pone en verde cuando el dato llega corregido del sistema.

Criterios: la depreciación acumulada (39) resta al activo fijo (el reporte del sistema la pone en el pasivo). Todo % es ponderado. Sin metas. No hay impuesto a la renta en el reporte: la utilidad es antes de impuestos.

## Datos (Supabase, prefijo `ef_`)
- `ef_periodos` (mes, fecha de corte, provisional/cerrado), `ef_saldos` (cuenta por mes: BG saldo; ER mes y acumulado; `vigente=false` guarda lo que se mostró antes de una corrección), `ef_totales` (totales tal como los imprime el reporte), `ef_acceso` (quién puede ver), `ef_config` (`tolerancia_cuadre` = 1 sol).
- Vistas: `ef_v_er`, `ef_v_bg`, `ef_v_control`. Puerta única: `ef_api(p_accion, p)` con acciones `permiso`, `periodos`, `resumen`, `control`.
- **Excepción a la lectura libre** (decisión de Christian, 22/10/2026): las tablas y vistas `ef_` no se leen con la clave pública; solo la puerta entrega datos y solo a usuarios de `ef_acceso` (hoy ctalepcio y rcamogliano). Sin permiso, la pantalla muestra "No tienes permiso…".
- Datos de prueba: jul–set 2026 cargados desde los Excel de Contabilidad (`AD\nuevos\Contabilidad`). Registro SQL en `AD\entregables\estados-financieros` (Ef01 esquema, Ef02 carga).

## Quién la usa
Gerencia General (Rodrigo, supervisión) y Christian. Contabilidad la usará para corregir en el sistema lo que marca Control del cierre (agregarlos a `ef_acceso`).

## Paso a SQL Server
Paquete para Sistemas en `AD\entregables\estados-financieros`. En el HTML solo cambia la constante `PUERTA`. Los saldos por cuenta y mes salen directo del sistema contable (nadie sube archivos).

## Bloques
lives-tema v1, lives-cargando v1.2, lives-sesion v5.2, lives-portal-obligatorio v2, lives-franja v6.1, lives-excel-reporte v1.11 (enganche `window.LS_XLS`), lives-leyenda, lives-actualizar v2.2.
