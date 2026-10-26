# Estados Financieros — LIVES

Las reglas generales están en el CLAUDE.md del repo `lives-instrucciones`. Aquí solo lo propio de esta herramienta.

## Qué hace
Dashboard de gerencia con el Balance General y el Estado de Resultados por función, mes a mes (en soles). Una sola pantalla: `index.html`.
- Barra: año (solo con más de un año cargado; filtra la lista de meses), mes, Mes / Acumulado del año, Soles / % de ventas (desde 26/10/2026, pedido de Rodrigo; solo se ve en la pestaña Resultados fuera del Comparativo y solo cambia la tabla "Estado de resultados": en % cada columna se divide entre las ventas netas de esa misma columna, sin la columna final "% ventas"; el Excel sigue lo que se ve), aviso "Balance no cuadra · S/ X" (lleva a Control del cierre), Descargar Excel, Imprimir. Sin botón 🌙 propio (desde 22/10/2026): el modo oscuro existe y sigue al portal de Sistemas (o al equipo, fuera del portal); el botón lo pone el portal.
- KPIs: ventas netas, margen bruto, utilidad de operación, utilidad del ejercicio, efectivo al cierre.
- Pestaña Resultados: cascada de ventas a utilidad, márgenes por mes, estado de resultados, ventas y costo, ingresos y gastos financieros. Con más de un año cargado, el botón "Comparativo" de la barra abre una vista aparte, solo comparativa (oculta KPIs y pestañas; "← Volver al mes" regresa): líneas por año (ene–dic, una línea por año), con tabla mes a mes, diferencia en soles y variación % del año más reciente contra el anterior (sin % si el año anterior fue pérdida o cero). Un selector en la barra ("Comparar:" ventas netas, utilidad bruta, de operación o del ejercicio) aparece solo en esa vista; la fila de total suma solo los meses presentes en los dos años. Sigue el interruptor Mes / Acumulado del año y va también en el Excel. El Excel siempre trae en Resumen un bloque "Anual" (estado de resultados acumulado de enero al mes elegido contra el mismo período del año anterior), justo después del estado de resultados mes a mes; si se descarga desde la vista Comparativo, el comparativo y el anual van primero. El bloque de Excel compartido solo permite hojas Resumen y Detalle, por eso el anual es un bloque de Resumen y no una pestaña aparte. Con más de 2 años solo el año más reciente lleva etiquetas; en celular se ven al tocar.
- Pestaña Balance y caja: liquidez, prueba ácida, pasivo ÷ patrimonio, días de cobro, inventario y pago, ciclo de caja, composición del activo y del financiamiento, balance general, efectivo/clientes/inventarios.
- Pestaña Control del cierre (para Contabilidad): cuadre, cuenta 99 = utilidad, totales impresos = suma de cuentas, líneas del estado de resultados, depreciación en el pasivo, cuenta 45, cuentas sin movimiento, margen del mes. Cada control se pone en verde cuando el dato llega corregido del sistema.

Criterios: la depreciación acumulada (39) resta al activo fijo (el reporte del sistema la pone en el pasivo). Todo % es ponderado. Sin metas. No hay impuesto a la renta en el reporte: la utilidad es antes de impuestos.

## Datos (Supabase, prefijo `ef_`)
- `ef_periodos` (mes, fecha de corte, provisional/cerrado), `ef_saldos` (cuenta por mes: BG saldo; ER mes y acumulado; `vigente=false` guarda lo que se mostró antes de una corrección), `ef_totales` (totales tal como los imprime el reporte), `ef_acceso` (quién puede ver), `ef_config` (`tolerancia_cuadre` = 1 sol).
- Vistas: `ef_v_er`, `ef_v_bg`, `ef_v_control`. Puerta única: `ef_api(p_accion, p)` con acciones `permiso`, `periodos`, `resumen`, `control`.
- **Lectura libre** (decisión de Christian, 22/10/2026): prueba con 3 meses (jul-set 2026), se ve fuera del portal y sin usuario; solo Christian y Sistemas tienen el link. Las tablas y vistas `ef_` siguen cerradas: la única puerta es `ef_api` y nadie puede escribir. `ef_acceso` queda sin usar; para volver a cerrar, restaurar `ef_puede_ver_` con la versión de Ef01. En SQL Server se exige usuario.
- Datos de prueba: jul–set 2026 cargados desde los Excel de Contabilidad (`AD\nuevos\Contabilidad`). Además, datos FICTICIOS de ene–dic 2025 (22/10/2026, solo para probar el comparativo entre años; no son de la empresa, no se migran; se quitan con el bloque "DESHACER" de Ef04: borrar lo anterior a 2026 en `ef_totales`, `ef_saldos` y `ef_periodos`). Registro SQL en `AD\entregables\estados-financieros` (Ef01 esquema, Ef02 carga, Ef03 lectura libre, Ef04 datos ficticios 2025).

## Quién la usa
Gerencia General (Rodrigo, supervisión) y Christian. Contabilidad la usará para corregir en el sistema lo que marca Control del cierre.

## Paso a SQL Server
Paquete para Sistemas en `AD\entregables\estados-financieros`. En el HTML solo cambia la constante `PUERTA`. Los saldos por cuenta y mes salen directo del sistema contable (nadie sube archivos).

## Bloques
lives-tema v1, lives-cargando v1.2, lives-sesion v5.2, lives-portal-obligatorio v2, lives-franja v6.1, lives-excel-reporte v1.11 (enganche `window.LS_XLS`), lives-leyenda, lives-actualizar v2.2.
