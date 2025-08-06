🚀 Comandos Esenciales de SQL Server para Análisis de Datos
Este documento es una guía de referencia rápida con los comandos y conceptos de SQL (específicamente para SQL Server) más solicitados en pruebas técnicas para roles de Analista de Datos. El objetivo no es solo listar la sintaxis, sino mostrar cómo se aplican para resolver problemas de negocio reales.

Tabla de Contenidos
Nivel 1: Fundamentos Indispensables

Nivel 2: Agregación y Agrupación

Nivel 3: Unión de Datos (JOINS)

Nivel 4: Tópicos Avanzados

Ejemplo Práctico: Problema Tipo

Consejos Adicionales para tu Prueba

Nivel 1: Fundamentos Indispensables
La base de cualquier consulta que escribas.

SELECT, FROM, WHERE
SELECT: Elige las columnas que quieres ver (* para todas).

FROM: Especifica la tabla de origen.

WHERE: Filtra los registros según una condición.

-- Seleccionar el nombre y el salario de los empleados del departamento de 'Ventas'.
SELECT
    Nombre,
    Salario
FROM
    Empleados
WHERE
    Departamento = 'Ventas';

ORDER BY
Ordena los resultados en modo ascendente (ASC, por defecto) o descendente (DESC).

-- Obtener los productos ordenados del más caro al más barato.
SELECT
    NombreProducto,
    Precio
FROM
    Productos
ORDER BY
    Precio DESC;

TOP (Específico de SQL Server)
Limita el número de filas en el resultado (equivalente a LIMIT en otros dialectos SQL).

-- Encontrar los 10 empleados con el salario más alto.
SELECT TOP 10
    Nombre,
    Salario
FROM
    Empleados
ORDER BY
    Salario DESC;

Operadores de Filtrado
Comparación: =, <>, !=, >, <, >=, <=

Lógicos: AND, OR, NOT

Rangos: BETWEEN (ej. Precio BETWEEN 100 AND 200)

Listas: IN (ej. Ciudad IN ('Bogotá', 'Medellín'))

Patrones: LIKE con comodines % (cero o más caracteres) y _ (un solo caracter).

Nulos: IS NULL, IS NOT NULL

Nivel 2: Agregación y Agrupación
El corazón del análisis: resumir y agrupar datos para obtener insights.

GROUP BY
Agrupa filas con valores idénticos para resumirlas usando funciones de agregación.

Funciones de Agregación
COUNT(): Cuenta el número de filas. COUNT(DISTINCT columna) cuenta valores únicos.

SUM(): Suma los valores de una columna numérica.

AVG(): Calcula el promedio.

MIN(): Obtiene el valor mínimo.

MAX(): Obtiene el valor máximo.

HAVING
Filtra los resultados después de la agregación (GROUP BY).

-- Mostrar los departamentos con más de 5 empleados y su salario promedio.
SELECT
    Departamento,
    COUNT(EmpleadoID) AS NumeroDeEmpleados,
    AVG(Salario) AS SalarioPromedio
FROM
    Empleados
GROUP BY
    Departamento
HAVING
    COUNT(EmpleadoID) > 5;

Nivel 3: Unión de Datos (JOINS)
Esencial para combinar información de múltiples tablas.

INNER JOIN: Devuelve solo las filas con coincidencias en ambas tablas.

LEFT JOIN: Devuelve todas las filas de la tabla izquierda y las coincidencias de la derecha (o NULL si no hay). Es el más común para análisis.

RIGHT JOIN: Inverso al LEFT JOIN.

FULL OUTER JOIN: Devuelve todas las filas cuando hay una coincidencia en cualquiera de las tablas.

Tip: Usa siempre alias para las tablas (Empleados AS E) para hacer tu código más corto y legible.

-- Obtener todos los clientes y el monto total de sus pedidos (incluir clientes sin pedidos).
SELECT
    C.NombreCliente,
    SUM(P.Monto) AS MontoTotalPedidos
FROM
    Clientes AS C
LEFT JOIN
    Pedidos AS P ON C.ClienteID = P.ClienteID
GROUP BY
    C.NombreCliente
ORDER BY
    MontoTotalPedidos DESC;

Nivel 4: Tópicos Avanzados
Estos conceptos te diferenciarán de otros candidatos.

Common Table Expressions (CTEs) - WITH
Crean un conjunto de resultados temporal y con nombre para hacer las consultas complejas más legibles y modulares.

-- Usar un CTE para encontrar empleados con salarios superiores al promedio de su departamento.
WITH SalariosPorDepto AS (
    SELECT
        Departamento,
        AVG(Salario) AS SalarioPromedioDepto
    FROM
        Empleados
    GROUP BY
        Departamento
)
SELECT
    E.Nombre,
    E.Salario,
    E.Departamento
FROM
    Empleados AS E
INNER JOIN
    SalariosPorDepto AS SPD ON E.Departamento = SPD.Departamento
WHERE
    E.Salario > SPD.SalarioPromedioDepto;

Funciones de Ventana (OVER())
Realizan cálculos sobre un conjunto de filas relacionadas sin colapsarlas.

Ranking: ROW_NUMBER(), RANK(), DENSE_RANK()

Agregación en ventana: SUM(...) OVER (...), COUNT(...) OVER (...)

Valor/Desplazamiento: LAG() (fila anterior), LEAD() (fila siguiente)

-- Asignar un ranking de salario a los empleados dentro de su propio departamento.
SELECT
    Nombre,
    Departamento,
    Salario,
    RANK() OVER (PARTITION BY Departamento ORDER BY Salario DESC) AS RankingSalario
FROM
    Empleados;

Manejo de Nulos y Tipos de Datos
ISNULL(columna, valor_reemplazo): (SQL Server) Reemplaza NULL con un valor.

COALESCE(col1, col2, ...): (Estándar) Devuelve el primer valor no nulo de la lista.

CAST(expresion AS tipo_dato): Convierte un tipo de dato a otro.

CONVERT(tipo_dato, expresion): Similar a CAST pero más flexible en SQL Server.

Ejemplo Práctico: Problema Tipo
Desafío:

"Para los clientes de 'Colombia' que realizaron compras en el último año, calcula el total gastado por cada uno. Luego, muéstrame el top 5 de estos clientes, junto con la fecha de su primera y última compra en ese período."

Posible Solución:

-- Usamos un CTE para mantener la lógica limpia
WITH ComprasClientesColombia AS (
    SELECT
        C.ClienteID,
        C.NombreCliente,
        P.Monto,
        P.FechaPedido
    FROM
        Clientes AS C
    INNER JOIN
        Pedidos AS P ON C.ClienteID = P.ClienteID
    WHERE
        C.Pais = 'Colombia'
        AND P.FechaPedido >= DATEADD(year, -1, GETDATE()) -- Filtra pedidos del último año
)
-- Ahora agregamos los resultados del CTE
SELECT TOP 5
    NombreCliente,
    SUM(Monto) AS TotalGastado,
    MIN(FechaPedido) AS PrimeraCompra,
    MAX(FechaPedido) AS UltimaCompra
FROM
    ComprasClientesColombia
GROUP BY
    ClienteID,
    NombreCliente
ORDER BY
    TotalGastado DESC;

Consejos Adicionales para tu Prueba
✍️ Piensa antes de escribir: Dedica un minuto a entender el problema y el modelo de datos. Dibuja las uniones en tu mente o en un papel.

✨ La legibilidad es clave: Usa alias, indentación y comentarios. Un código limpio demuestra un pensamiento claro.

🧠 Comunica tu razonamiento: Si es una prueba en vivo, explica lo que estás haciendo y por qué. Menciona las alternativas que consideraste.

✔️ Considera los casos extremos: ¿Qué pasa si hay NULLs? ¿Qué pasa si una tabla está vacía? Mencionar esto demuestra experiencia.

🚀 No te limites a lo básico: Si puedes resolver un problema con una subconsulta, piensa si un CTE o una función de ventana lo haría más elegante o eficiente.
