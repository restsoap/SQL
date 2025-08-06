# Comandos SQL más solicitados en pruebas técnicas para Analistas de Datos (SQL Server)

Este documento resume los comandos SQL más frecuentes en pruebas técnicas para el rol de **Analista de Datos**, específicamente usando **SQL Server**.

---

## 🔹 1. Selección de datos

```sql
-- Seleccionar todas las columnas de una tabla
SELECT * FROM nombre_tabla;

-- Seleccionar columnas específicas
SELECT columna1, columna2 FROM nombre_tabla;

-- Filtros con WHERE
SELECT * FROM nombre_tabla WHERE columna1 = 'valor';

-- Operadores lógicos
SELECT * FROM nombre_tabla WHERE columna1 = 'valor' AND columna2 > 100;
```

---

## 🔹 2. Ordenamiento y límite

```sql
-- Orden ascendente y descendente
SELECT * FROM nombre_tabla ORDER BY columna1 ASC;
SELECT * FROM nombre_tabla ORDER BY columna1 DESC;

-- Limitar resultados (TOP en SQL Server)
SELECT TOP 10 * FROM nombre_tabla;
```

---

## 🔹 3. Funciones de agregación y GROUP BY

```sql
-- Suma, promedio, conteo, máximo y mínimo
SELECT COUNT(*) FROM nombre_tabla;
SELECT AVG(columna_numerica) FROM nombre_tabla;
SELECT SUM(columna_numerica) FROM nombre_tabla;

-- Agrupar por una columna
SELECT columna1, COUNT(*)
FROM nombre_tabla
GROUP BY columna1;

-- Filtro después del agrupamiento
SELECT columna1, COUNT(*)
FROM nombre_tabla
GROUP BY columna1
HAVING COUNT(*) > 1;
```

---

## 🔹 4. JOINs (Uniones)

```sql
-- INNER JOIN
SELECT a.columna1, b.columna2
FROM tablaA a
INNER JOIN tablaB b ON a.id = b.id_a;

-- LEFT JOIN
SELECT a.columna1, b.columna2
FROM tablaA a
LEFT JOIN tablaB b ON a.id = b.id_a;

-- RIGHT JOIN
SELECT a.columna1, b.columna2
FROM tablaA a
RIGHT JOIN tablaB b ON a.id = b.id_a;

-- FULL OUTER JOIN
SELECT a.columna1, b.columna2
FROM tablaA a
FULL OUTER JOIN tablaB b ON a.id = b.id_a;
```

---

## 🔹 5. Subconsultas

```sql
-- Subconsulta en SELECT
SELECT nombre, (SELECT AVG(salario) FROM empleados) AS salario_promedio
FROM empleados;

-- Subconsulta en WHERE
SELECT *
FROM empleados
WHERE salario > (SELECT AVG(salario) FROM empleados);
```

---

## 🔹 6. Funciones útiles

```sql
-- CONCATENAR
SELECT CONCAT(nombre, ' ', apellido) AS nombre_completo FROM empleados;

-- FECHAS
SELECT GETDATE(); -- Fecha y hora actual
SELECT DATEPART(YEAR, fecha_nacimiento) FROM empleados;
SELECT FORMAT(fecha_ingreso, 'yyyy-MM') FROM empleados;

-- CONVERT y CAST
SELECT CAST(salario AS INT) FROM empleados;
SELECT CONVERT(VARCHAR, fecha_ingreso, 23) FROM empleados;
```

---

## 🔹 7. CASE (Condicional)

```sql
SELECT nombre,
       salario,
       CASE
           WHEN salario > 5000 THEN 'Alto'
           WHEN salario BETWEEN 3000 AND 5000 THEN 'Medio'
           ELSE 'Bajo'
       END AS rango_salarial
FROM empleados;
```

---

## 🔹 8. CTEs y Window Functions (Avanzado)

```sql
-- CTE: Common Table Expression
WITH EmpleadosActivos AS (
    SELECT * FROM empleados WHERE estado = 'activo'
)
SELECT * FROM EmpleadosActivos;

-- ROW_NUMBER
SELECT nombre, ROW_NUMBER() OVER(ORDER BY salario DESC) AS fila
FROM empleados;

-- RANK
SELECT nombre, RANK() OVER(ORDER BY salario DESC) AS ranking
FROM empleados;
```

---

## 🔹 9. Inserción, actualización y eliminación

```sql
-- INSERT
INSERT INTO empleados (nombre, apellido, salario)
VALUES ('Ana', 'Pérez', 4000);

-- UPDATE
UPDATE empleados SET salario = 4500 WHERE nombre = 'Ana';

-- DELETE
DELETE FROM empleados WHERE nombre = 'Ana';
```

---

## 📌 Recomendaciones para pruebas técnicas

- Practica en SQL Server Management Studio (SSMS).
- Comprende bien los `JOIN`, `GROUP BY` y subconsultas.
- Familiarízate con funciones de fecha y texto.
- Si la prueba es práctica, optimiza tus consultas para rendimiento.

---

## 📚 Recursos

- [SQLBolt](https://sqlbolt.com/)
- [Mode SQL Tutorial](https://mode.com/sql-tutorial/)
- [w3schools SQL](https://www.w3schools.com/sql/)

---

¡Éxito en tu prueba técnica! 🚀

