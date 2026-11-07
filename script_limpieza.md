let
    // Paso 1: Fuente de datos original cargada manualmente mediante Table.FromRecords
    Origen = Table.FromRecords({
        [id_venta = 1, nombre_producto = " Laptop Pro 15 ", categoria = "Computación", precio = 1200.00, fecha_venta = "2024-01-05"],
        [id_venta = 2, nombre_producto = "Mouse Inalámbrico", categoria = "accesorios", precio = 28.00, fecha_venta = "2024-01-08"],
        [id_venta = 3, nombre_producto = " Teclado Mecánico", categoria = "PRUEBA", precio = 95.00, fecha_venta = "2024-01-12"],
        [id_venta = 4, nombre_producto = "Monitor 4K ", categoria = "computación", precio = 450.00, fecha_venta = "2024-02-03"],
        [id_venta = 5, nombre_producto = " Auriculares BT", categoria = "Audio", precio = 120.00, fecha_venta = "2024-02-10"],
        [id_venta = 6, nombre_producto = "SSD Externo 1TB ", categoria = "PRUEBA", precio = 130.00, fecha_venta = "2024-03-05"],
        [id_venta = 7, nombre_producto = "Webcam HD", categoria = "Accesorios", precio = 85.00, fecha_venta = "2024-03-12"]
    }),

    // Paso 2: Eliminar espacios en blanco al inicio y al final de la columna nombre_producto usando Text.Trim
    // Impacto analítico: Evita duplicados falsos y asegura uniones exactas entre tablas del modelo.
    LimpiarEspacios = Table.TransformColumns(Origen, {{"nombre_producto", Text.Trim, type text}}),

    // Paso 3: Estandarizar la columna categoria a Title Case (ej: "Computación") usando Text.Proper
    // Impacto analítico: Unifica "computación", "COMPUTACIÓN" y "Computación" bajo una única categoría para segmentación en reportes.
    EstandarizarCategoria = Table.TransformColumns(LimpiarEspacios, {{"categoria", Text.Proper, type text}}),

    // Paso 4: Filtrar y eliminar registros de prueba (excluir filas donde categoria sea exactamente "Prueba") usando Table.SelectRows
    // Impacto analítico: Remueve datos no operativos que distorsionan los totales de ventas y métricas clave.
    EliminarPruebas = Table.SelectRows(EstandarizarCategoria, each ([categoria] <> "Prueba")),

    // Paso 5: Definir tipos de datos correctos en todas las columnas usando Table.TransformColumnTypes
    // Impacto analítico: Permite realizar cálculos numéricos sobre precio e inteligencia de tiempo sobre fecha_venta.
    TiparColumnas = Table.TransformColumnTypes(EliminarPruebas, {
        {"id_venta", Int64.Type},
        {"nombre_producto", type text},
        {"categoria", type text},
        {"precio", type number},
        {"fecha_venta", type date}
    })
in
    TiparColumnas
