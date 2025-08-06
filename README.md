Comandos SQL Server para Pruebas Técnicas - Analista de Datos
📋 Tabla de Contenidos

SELECT y Filtrado Básico

JOINs

Funciones de Agregación

GROUP BY y HAVING

Subconsultas

Window Functions

CTEs (Common Table Expressions)

Manipulación de Fechas

CASE WHEN

Manipulación de Strings

Índices y Optimización

Stored Procedures

Vistas

Tablas Temporales

Manejo de NULL

SELECT y Filtrado Básico
SELECT básico
-- Seleccionar todas las columnas
SELECT * FROM Empleados;

-- Seleccionar columnas específicas
SELECT Nombre, Apellido, Salario 
FROM Empleados;

-- Con alias
SELECT 
    Nombre AS NombreEmpleado,
    Salario AS SalarioMensual
FROM Empleados;

WHERE - Filtrado
-- Filtro simple
SELECT * FROM Empleados 
WHERE Departamento = 'Ventas';

-- Múltiples condiciones
SELECT * FROM Empleados 
WHERE Salario > 50000 
  AND Departamento = 'IT'
  AND FechaIngreso >= '2020-01-01';

-- Operador IN
SELECT * FROM Empleados 
WHERE Departamento IN ('Ventas', 'Marketing', 'IT');

-- BETWEEN
SELECT * FROM Empleados 
WHERE Salario BETWEEN 40000 AND 80000;

-- LIKE para búsquedas de patrones
SELECT * FROM Empleados 
WHERE Nombre LIKE 'J%'  -- Nombres que empiezan con J
   OR Email LIKE '%@gmail.com';  -- Emails de Gmail

ORDER BY
-- Ordenar ascendente (por defecto)
SELECT * FROM Empleados 
ORDER BY Salario;

-- Ordenar descendente
SELECT * FROM Empleados 
ORDER BY Salario DESC;

-- Ordenar por múltiples columnas
SELECT * FROM Empleados 
ORDER BY Departamento, Salario DESC;

JOINs
INNER JOIN
-- Join básico
SELECT 
    e.Nombre,
    e.Apellido,
    d.NombreDepartamento
FROM Empleados e
INNER JOIN Departamentos d ON e.DepartamentoID = d.DepartamentoID;

-- Join con múltiples tablas
SELECT 
    e.Nombre,
    d.NombreDepartamento,
    p.NombreProyecto
FROM Empleados e
INNER JOIN Departamentos d ON e.DepartamentoID = d.DepartamentoID
INNER JOIN ProyectosEmpleados pe ON e.EmpleadoID = pe.EmpleadoID
INNER JOIN Proyectos p ON pe.ProyectoID = p.ProyectoID;

LEFT JOIN
-- Todos los empleados, incluso sin departamento asignado
SELECT 
    e.Nombre,
    ISNULL(d.NombreDepartamento, 'Sin Departamento') AS Departamento
FROM Empleados e
LEFT JOIN Departamentos d ON e.DepartamentoID = d.DepartamentoID;

RIGHT JOIN
-- Todos los departamentos, incluso sin empleados
SELECT 
    d.NombreDepartamento,
    COUNT(e.EmpleadoID) AS CantidadEmpleados
FROM Empleados e
RIGHT JOIN Departamentos d ON e.DepartamentoID = d.DepartamentoID
GROUP BY d.NombreDepartamento;

FULL OUTER JOIN
-- Todos los registros de ambas tablas
SELECT 
    ISNULL(e.Nombre, 'Sin Empleado') AS Empleado,
    ISNULL(d.NombreDepartamento, 'Sin Departamento') AS Departamento
FROM Empleados e
FULL OUTER JOIN Departamentos d ON e.DepartamentoID = d.DepartamentoID;

Funciones de Agregación
Funciones básicas
-- COUNT
SELECT COUNT(*) AS TotalEmpleados FROM Empleados;
SELECT COUNT(DISTINCT Departamento) AS DepartamentosUnicos FROM Empleados;

-- SUM
SELECT SUM(Salario) AS SalarioTotal FROM Empleados;

-- AVG
SELECT AVG(Salario) AS SalarioPromedio FROM Empleados;

-- MIN y MAX
SELECT 
    MIN(Salario) AS SalarioMinimo,
    MAX(Salario) AS SalarioMaximo
FROM Empleados;

-- Combinando funciones
SELECT 
    Departamento,
    COUNT(*) AS CantidadEmpleados,
    AVG(Salario) AS SalarioPromedio,
    SUM(Salario) AS SalarioTotal
FROM Empleados
GROUP BY Departamento;

GROUP BY y HAVING
GROUP BY básico
-- Agrupar por una columna
SELECT 
    Departamento,
    COUNT(*) AS CantidadEmpleados
FROM Empleados
GROUP BY Departamento;

-- Agrupar por múltiples columnas
SELECT 
    Departamento,
    YEAR(FechaIngreso) AS Año,
    COUNT(*) AS EmpleadosContratados
FROM Empleados
GROUP BY Departamento, YEAR(FechaIngreso)
ORDER BY Departamento, Año;

HAVING
-- Filtrar grupos con HAVING
SELECT 
    Departamento,
    AVG(Salario) AS SalarioPromedio
FROM Empleados
GROUP BY Departamento
HAVING AVG(Salario) > 60000;

-- HAVING con múltiples condiciones
SELECT 
    Departamento,
    COUNT(*) AS CantidadEmpleados,
    AVG(Salario) AS SalarioPromedio
FROM Empleados
GROUP BY Departamento
HAVING COUNT(*) >= 5 
   AND AVG(Salario) > 50000;

Subconsultas
Subconsulta en WHERE
-- Empleados con salario mayor al promedio
SELECT * FROM Empleados
WHERE Salario > (SELECT AVG(Salario) FROM Empleados);

-- Empleados del departamento con más empleados
SELECT * FROM Empleados
WHERE DepartamentoID = (
    SELECT TOP 1 DepartamentoID
    FROM Empleados
    GROUP BY DepartamentoID
    ORDER BY COUNT(*) DESC
);

Subconsulta en SELECT
SELECT 
    e.Nombre,
    e.Salario,
    (SELECT AVG(Salario) FROM Empleados WHERE DepartamentoID = e.DepartamentoID) AS PromedioDepto
FROM Empleados e;

Subconsulta correlacionada
-- Empleados que ganan más que el promedio de su departamento
SELECT e1.* 
FROM Empleados e1
WHERE e1.Salario > (
    SELECT AVG(e2.Salario)
    FROM Empleados e2
    WHERE e2.DepartamentoID = e1.DepartamentoID
);

Window Functions
ROW_NUMBER()
-- Numerar empleados por departamento según salario
SELECT 
    Nombre,
    Departamento,
    Salario,
    ROW_NUMBER() OVER (PARTITION BY Departamento ORDER BY Salario DESC) AS RankingSalario
FROM Empleados;

RANK() y DENSE_RANK()
-- RANK con saltos en números
SELECT 
    Nombre,
    Salario,
    RANK() OVER (ORDER BY Salario DESC) AS Ranking
FROM Empleados;

-- DENSE_RANK sin saltos
SELECT 
    Nombre,
    Salario,
    DENSE_RANK() OVER (ORDER BY Salario DESC) AS RankingDenso
FROM Empleados;

LAG() y LEAD()
-- Comparar con salario anterior y siguiente
SELECT 
    Nombre,
    Salario,
    LAG(Salario, 1) OVER (ORDER BY Salario) AS SalarioAnterior,
    LEAD(Salario, 1) OVER (ORDER BY Salario) AS SalarioSiguiente
FROM Empleados;

Funciones de agregación como Window Functions
-- Running total y promedio móvil
SELECT 
    Fecha,
    Ventas,
    SUM(Ventas) OVER (ORDER BY Fecha) AS VentasAcumuladas,
    AVG(Ventas) OVER (ORDER BY Fecha ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS PromedioMovil3Dias
FROM VentasDiarias;

CTEs (Common Table Expressions)
CTE básico
WITH EmpleadosIT AS (
    SELECT * FROM Empleados
    WHERE Departamento = 'IT'
)
SELECT * FROM EmpleadosIT
WHERE Salario > 70000;

CTEs múltiples
WITH 
DepartamentoStats AS (
    SELECT 
        DepartamentoID,
        AVG(Salario) AS SalarioPromedio,
        COUNT(*) AS CantidadEmpleados
    FROM Empleados
    GROUP BY DepartamentoID
),
TopDepartamentos AS (
    SELECT TOP 3 * FROM DepartamentoStats
    ORDER BY SalarioPromedio DESC
)
SELECT 
    e.*,
    ds.SalarioPromedio
FROM Empleados e
INNER JOIN TopDepartamentos ds ON e.DepartamentoID = ds.DepartamentoID;

CTE recursivo
-- Jerarquía de empleados
WITH JerarquiaEmpleados AS (
    -- Anchor: CEO (sin jefe)
    SELECT 
        EmpleadoID,
        Nombre,
        JefeID,
        0 AS Nivel
    FROM Empleados
    WHERE JefeID IS NULL
    
    UNION ALL
    
    -- Recursive: empleados con jefe
    SELECT 
        e.EmpleadoID,
        e.Nombre,
        e.JefeID,
        j.Nivel + 1
    FROM Empleados e
    INNER JOIN JerarquiaEmpleados j ON e.JefeID = j.EmpleadoID
)
SELECT * FROM JerarquiaEmpleados
ORDER BY Nivel, Nombre;

Manipulación de Fechas
Funciones de fecha básicas
-- Fecha actual
SELECT GETDATE() AS FechaHoraActual;
SELECT CAST(GETDATE() AS DATE) AS FechaActual;

-- Extraer partes de fecha
SELECT 
    YEAR(FechaIngreso) AS Año,
    MONTH(FechaIngreso) AS Mes,
    DAY(FechaIngreso) AS Dia,
    DATEPART(QUARTER, FechaIngreso) AS Trimestre,
    DATENAME(MONTH, FechaIngreso) AS NombreMes,
    DATENAME(WEEKDAY, FechaIngreso) AS DiaSemana
FROM Empleados;

Operaciones con fechas
-- Agregar/restar tiempo
SELECT 
    DATEADD(DAY, 30, GETDATE()) AS En30Dias,
    DATEADD(MONTH, -1, GETDATE()) AS HaceUnMes,
    DATEADD(YEAR, 1, GETDATE()) AS EnUnAño;

-- Diferencia entre fechas
SELECT 
    Nombre,
    FechaIngreso,
    DATEDIFF(DAY, FechaIngreso, GETDATE()) AS DiasEnEmpresa,
    DATEDIFF(MONTH, FechaIngreso, GETDATE()) AS MesesEnEmpresa,
    DATEDIFF(YEAR, FechaIngreso, GETDATE()) AS AñosEnEmpresa
FROM Empleados;

Formateo de fechas
-- FORMAT (SQL Server 2012+)
SELECT 
    FORMAT(GETDATE(), 'dd/MM/yyyy') AS FechaFormateada,
    FORMAT(GETDATE(), 'MMMM dd, yyyy') AS FechaLarga,
    FORMAT(GETDATE(), 'HH:mm:ss') AS HoraSola;

-- CONVERT con estilos
SELECT 
    CONVERT(VARCHAR, GETDATE(), 103) AS FormatoUK,  -- dd/mm/yyyy
    CONVERT(VARCHAR, GETDATE(), 101) AS FormatoUS;  -- mm/dd/yyyy

CASE WHEN
CASE simple
SELECT 
    Nombre,
    Salario,
    CASE 
        WHEN Salario < 40000 THEN 'Bajo'
        WHEN Salario BETWEEN 40000 AND 70000 THEN 'Medio'
        WHEN Salario > 70000 THEN 'Alto'
        ELSE 'No definido'
    END AS CategoriaSalario
FROM Empleados;

CASE en agregaciones
-- Contar por categorías
SELECT 
    COUNT(CASE WHEN Departamento = 'IT' THEN 1 END) AS EmpleadosIT,
    COUNT(CASE WHEN Departamento = 'Ventas' THEN 1 END) AS EmpleadosVentas,
    COUNT(CASE WHEN Departamento = 'RRHH' THEN 1 END) AS EmpleadosRRHH
FROM Empleados;

-- Suma condicional
SELECT 
    SUM(CASE WHEN Departamento = 'IT' THEN Salario ELSE 0 END) AS SalarioTotalIT,
    SUM(CASE WHEN Departamento = 'Ventas' THEN Salario ELSE 0 END) AS SalarioTotalVentas
FROM Empleados;

CASE anidado
SELECT 
    Nombre,
    Salario,
    Departamento,
    CASE 
        WHEN Departamento = 'IT' THEN
            CASE 
                WHEN Salario > 80000 THEN 'Senior IT'
                WHEN Salario > 60000 THEN 'Mid IT'
                ELSE 'Junior IT'
            END
        WHEN Departamento = 'Ventas' THEN
            CASE 
                WHEN Salario > 70000 THEN 'Senior Ventas'
                ELSE 'Junior Ventas'
            END
        ELSE 'Otro'
    END AS Categoria
FROM Empleados;

Manipulación de Strings
Funciones básicas de string
-- Concatenación
SELECT 
    Nombre + ' ' + Apellido AS NombreCompleto,
    CONCAT(Nombre, ' ', Apellido) AS NombreCompleto2
FROM Empleados;

-- Longitud y recorte
SELECT 
    LEN(Nombre) AS LongitudNombre,
    LEFT(Nombre, 3) AS PrimerosTres,
    RIGHT(Email, 10) AS UltimosDiez,
    SUBSTRING(Telefono, 1, 3) AS CodigoArea
FROM Empleados;

Limpieza y formato
-- Eliminar espacios
SELECT 
    LTRIM(RTRIM(Nombre)) AS NombreLimpio,
    TRIM(Nombre) AS NombreLimpio2  -- SQL Server 2017+
FROM Empleados;

-- Cambiar mayúsculas/minúsculas
SELECT 
    UPPER(Nombre) AS NombreMayusculas,
    LOWER(Email) AS EmailMinusculas,
    UPPER(LEFT(Nombre, 1)) + LOWER(SUBSTRING(Nombre, 2, LEN(Nombre))) AS NombreCapitalizado
FROM Empleados;

Búsqueda y reemplazo
-- CHARINDEX y PATINDEX
SELECT 
    Email,
    CHARINDEX('@', Email) AS PosicionArroba,
    SUBSTRING(Email, 1, CHARINDEX('@', Email) - 1) AS Usuario,
    SUBSTRING(Email, CHARINDEX('@', Email) + 1, LEN(Email)) AS Dominio
FROM Empleados;

-- REPLACE
SELECT 
    Telefono,
    REPLACE(REPLACE(REPLACE(Telefono, '-', ''), '(', ''), ')', '') AS TelefonoLimpio
FROM Empleados;

Índices y Optimización
Crear índices
-- Índice simple
CREATE INDEX IX_Empleados_Departamento 
ON Empleados(Departamento);

-- Índice compuesto
CREATE INDEX IX_Empleados_Depto_Salario 
ON Empleados(Departamento, Salario DESC);

-- Índice único
CREATE UNIQUE INDEX IX_Empleados_Email 
ON Empleados(Email);

-- Índice con columnas incluidas
CREATE INDEX IX_Empleados_Nombre 
ON Empleados(Nombre) 
INCLUDE (Apellido, Email, Telefono);

Analizar rendimiento
-- Ver plan de ejecución
SET STATISTICS IO ON;
SET STATISTICS TIME ON;

SELECT * FROM Empleados WHERE Departamento = 'IT';

-- Ver índices de una tabla
SELECT 
    i.name AS IndexName,
    i.type_desc AS IndexType,
    COL_NAME(ic.object_id, ic.column_id) AS ColumnName
FROM sys.indexes i
INNER JOIN sys.index_columns ic ON i.object_id = ic.object_id AND i.index_id = ic.index_id
WHERE i.object_id = OBJECT_ID('Empleados');

Stored Procedures
Procedimiento básico
CREATE PROCEDURE sp_ObtenerEmpleadosPorDepartamento
    @Departamento NVARCHAR(50)
AS
BEGIN
    SELECT * FROM Empleados
    WHERE Departamento = @Departamento
    ORDER BY Nombre;
END;

-- Ejecutar
EXEC sp_ObtenerEmpleadosPorDepartamento @Departamento = 'IT';

Procedimiento con parámetros de salida
CREATE PROCEDURE sp_EstadisticasDepartamento
    @Departamento NVARCHAR(50),
    @CantidadEmpleados INT OUTPUT,
    @SalarioPromedio DECIMAL(10,2) OUTPUT
AS
BEGIN
    SELECT 
        @CantidadEmpleados = COUNT(*),
        @SalarioPromedio = AVG(Salario)
    FROM Empleados
    WHERE Departamento = @Departamento;
END;

-- Ejecutar
DECLARE @Cantidad INT, @Promedio DECIMAL(10,2);
EXEC sp_EstadisticasDepartamento 
    @Departamento = 'IT',
    @CantidadEmpleados = @Cantidad OUTPUT,
    @SalarioPromedio = @Promedio OUTPUT;
SELECT @Cantidad AS Empleados, @Promedio AS SalarioPromedio;

Procedimiento con manejo de errores
CREATE PROCEDURE sp_ActualizarSalario
    @EmpleadoID INT,
    @NuevoSalario DECIMAL(10,2)
AS
BEGIN
    BEGIN TRY
        BEGIN TRANSACTION;
        
        -- Validación
        IF @NuevoSalario < 0
            THROW 50001, 'El salario no puede ser negativo', 1;
        
        -- Actualización
        UPDATE Empleados
        SET Salario = @NuevoSalario
        WHERE EmpleadoID = @EmpleadoID;
        
        IF @@ROWCOUNT = 0
            THROW 50002, 'Empleado no encontrado', 1;
        
        COMMIT TRANSACTION;
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION;
        THROW;
    END CATCH
END;

Vistas
Vista simple
CREATE VIEW vw_EmpleadosActivos AS
SELECT 
    e.EmpleadoID,
    e.Nombre + ' ' + e.Apellido AS NombreCompleto,
    e.Email,
    d.NombreDepartamento
FROM Empleados e
INNER JOIN Departamentos d ON e.DepartamentoID = d.DepartamentoID
WHERE e.Activo = 1;

-- Usar la vista
SELECT * FROM vw_EmpleadosActivos;

Vista con agregaciones
CREATE VIEW vw_ResumenDepartamentos AS
SELECT 
    d.NombreDepartamento,
    COUNT(e.EmpleadoID) AS CantidadEmpleados,
    AVG(e.Salario) AS SalarioPromedio,
    MIN(e.Salario) AS SalarioMinimo,
    MAX(e.Salario) AS SalarioMaximo
FROM Departamentos d
LEFT JOIN Empleados e ON d.DepartamentoID = e.DepartamentoID
GROUP BY d.DepartamentoID, d.NombreDepartamento;

Tablas Temporales
Tabla temporal local (#)
-- Crear tabla temporal
CREATE TABLE #EmpleadosTemp (
    EmpleadoID INT,
    Nombre NVARCHAR(100),
    Salario DECIMAL(10,2)
);

-- Insertar datos
INSERT INTO #EmpleadosTemp
SELECT EmpleadoID, Nombre, Salario
FROM Empleados
WHERE Departamento = 'IT';

-- Usar tabla temporal
SELECT * FROM #EmpleadosTemp
WHERE Salario > 60000;

-- Se elimina automáticamente al cerrar la conexión
DROP TABLE #EmpleadosTemp;

Tabla temporal global (##)
-- Visible para todas las conexiones
CREATE TABLE ##EmpleadosGlobal (
    EmpleadoID INT,
    Nombre NVARCHAR(100)
);

Variable de tabla
DECLARE @EmpleadosVar TABLE (
    EmpleadoID INT,
    Nombre NVARCHAR(100),
    Salario DECIMAL(10,2)
);

INSERT INTO @EmpleadosVar
SELECT EmpleadoID, Nombre, Salario
FROM Empleados
WHERE Salario > 70000;

SELECT * FROM @EmpleadosVar;

Manejo de NULL
Verificación de NULL
-- IS NULL / IS NOT NULL
SELECT * FROM Empleados
WHERE JefeID IS NULL;  -- Empleados sin jefe

SELECT * FROM Empleados
WHERE Telefono IS NOT NULL;  -- Empleados con teléfono

Funciones para NULL
-- ISNULL
SELECT 
    Nombre,
    ISNULL(Telefono, 'Sin teléfono') AS Telefono
FROM Empleados;

-- COALESCE (primer valor no nulo)
SELECT 
    Nombre,
    COALESCE(TelefonoMovil, TelefonoCasa, TelefonoOficina, 'Sin teléfono') AS Contacto
FROM Empleados;

-- NULLIF (retorna NULL si los valores son iguales)
SELECT 
    Ventas,
    VentasAnterior,
    NULLIF(Ventas, VentasAnterior) AS DiferenciaVentas
FROM VentasMensuales;

💡 Tips para Pruebas Técnicas
1. Optimización de Consultas

Usa índices apropiadamente

Evita SELECT * en producción

Usa EXISTS en lugar de IN para subconsultas grandes

Considera el orden de los JOINs

2. Buenas Prácticas

Usa alias descriptivos

Indenta tu código SQL

Comenta consultas complejas

Usa nombres de columnas explícitos en INSERT

3. Errores Comunes a Evitar

No olvidar GROUP BY cuando usas agregaciones

Cuidado con los NULL en comparaciones

Verificar tipos de datos en JOINs

No confundir HAVING con WHERE

4. Performance
-- Malo
SELECT * FROM Empleados WHERE YEAR(FechaIngreso) = 2023;

-- Bueno (usa índice si existe)
SELECT * FROM Empleados 
WHERE FechaIngreso >= '2023-01-01' 
  AND FechaIngreso < '2024-01-01';

5. Preguntas Frecuentes en Entrevistas

Diferencia entre DELETE, TRUNCATE y DROP

Cuándo usar UNION vs UNION ALL

Explicar tipos de JOINs con ejemplos

Optimización de consultas lentas

Normalización de bases de datos

📚 Recursos Adicionales

Documentación oficial de SQL Server

SQL Server Management Studio (SSMS)

Azure Data Studio

Última actualización: Agosto 2025

Nota: Este documento cubre los comandos más solicitados en pruebas técnicas. Practica estos ejemplos con datos reales para mejor comprensión.
