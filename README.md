# Garden Database

Creación de la base de datos para Garden

## DATA DEFINITION LANGUAJE (DDL)
```sql
CREATE TABLE producto (
    codigo_producto VARCHAR(15) PRIMARY KEY UNIQUE,
    nombre VARCHAR(70),
    id_gama VARCHAR(50),
    id_dimensiones VARCHAR(25),
    id_proveedor VARCHAR(50) NULL,
    descripcion TEXT NULL,
    cantidad_en_stock SMALLINT(6),
    precio_venta DECIMAL(15, 2),
    precio_proveedor DECIMAL(15, 2),
    FOREIGN KEY (id_gama) REFERENCES gama_producto(gama),
    FOREIGN KEY (id_dimensiones) REFERENCES dimensiones(id_dimensiones)
);

CREATE TABLE gama_producto (
    gama VARCHAR(50) PRIMARY KEY UNIQUE,
    descripcion_texto TEXT,
    descripcion_html TEXT,
    imagen VARCHAR(256)
);

CREATE TABLE dimensiones (
    id_dimensiones VARCHAR(15) PRIMARY KEY UNIQUE,
    ancho DECIMAL(5, 2) NULL,
    alto DECIMAL(5, 2) NULL,
    profundo INTEGER NULL,
    diametro DECIMAL(5, 2) NULL
);

CREATE TABLE detalle_pedido (
    codigo_pedido INT(11),
    codigo_producto VARCHAR(15),
    cantidad INT(11),
    precio_unidad DECIMAL(15, 2),
    numero_linea SMALLINT(6),
    PRIMARY KEY (codigo_pedido, codigo_producto),
    FOREIGN KEY (codigo_pedido) REFERENCES pedido(codigo_pedido),
    FOREIGN KEY (codigo_producto) REFERENCES producto(codigo_producto)
);

CREATE TABLE pedido (
    codigo_pedido INT(11) PRIMARY KEY UNIQUE,
    fecha_pedido DATE,
    fecha_esperada DATE,
    fecha_entrega DATE NULL,
    estado VARCHAR(15),
    comentarios TEXT NULL,
    codigo_cliente INT(11),
    FOREIGN KEY (codigo_cliente) REFERENCES cliente(codigo_cliente)
);

CREATE TABLE cliente (
    codigo_cliente INT(11) AUTO_INCREMENT PRIMARY KEY UNIQUE,
    nombre_cliente VARCHAR(50),
    nombre_contacto VARCHAR(30) NULL,
    apellido_contacto VARCHAR(30) NULL,
    id_contacto INT(11),
    id_direccion INT(11),
    codigo_empleado_rep_ventas INT(11) NULL,
    limite_credito DECIMAL(15, 2),
    FOREIGN KEY (id_contacto) REFERENCES contacto(id_contacto),
    FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion),
    FOREIGN KEY (codigo_empleado_rep_ventas) REFERENCES empleado(codigo_empleado)
);

CREATE TABLE contacto (
    id_contacto INT(11) PRIMARY KEY,
    telefono VARCHAR(15),
    fax VARCHAR(15)
);

CREATE TABLE pago (
    id_transaccion VARCHAR(50),
    codigo_cliente INT(11),
    forma_pago VARCHAR(40),
    fecha_pago DATE,
    total DECIMAL(15, 2),
    PRIMARY KEY (id_transaccion),
    FOREIGN KEY (codigo_cliente) REFERENCES cliente(codigo_cliente)
);

CREATE TABLE pais(
    id_pais INT(11) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL
    );

CREATE TABLE region(
    id_region INT(11) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    id_pais INT(11) NOT NULL,
    CONSTRAINT FK_region_pais FOREIGN KEY (id_pais) REFERENCES pais(id_pais));

CREATE TABLE ciudad(
    id_ciudad INT(11) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    id_region INT(11) NOT NULL,
    CONSTRAINT FK_ciudad_region FOREIGN KEY (id_region) REFERENCES region(id_region)
);

CREATE TABLE direccion(
    id_direccion INT(11) PRIMARY KEY NOT NULL AUTO_INCREMENT,
    linea_direccion1 VARCHAR(50) NOT NULL,
    linea_direccion2 VARCHAR(50),
    barrio VARCHAR(100) NOT NULL,
    codigo_postal VARCHAR(10),
    id_ciudad INT(11) NOT NULL,
    CONSTRAINT FK_direccion_ciudad FOREIGN KEY(id_ciudad) REFERENCES ciudad(id_ciudad)
);

CREATE TABLE empleado (
    codigo_empleado INT(11) PRIMARY KEY UNIQUE,
    nombre_empleado VARCHAR(50),
    apellido_empleado1 VARCHAR(50),
    apellido_empleado2 VARCHAR(50) NULL,
    extension VARCHAR(10),
    email VARCHAR(100),
    puesto VARCHAR(50) NULL,
    codigo_jefe INT(11) NULL,
    CONSTRAINT FK_empleado_jefe FOREIGN KEY (codigo_jefe) REFERENCES empleado(codigo_empleado),
    codigo_oficina VARCHAR(10),
    CONSTRAINT FK_empleado_oficina FOREIGN KEY (codigo_oficina) REFERENCES oficina(codigo_oficina)
);

CREATE TABLE oficina (
    codigo_oficina INT(11) NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    telefono VARCHAR(20),
    PRIMARY KEY (codigo_oficina)
);

CREATE TABLE oficina_direccion (
    codigo_oficina INT(11) NOT NULL,
    id_direccion INT(11) NOT NULL,
    PRIMARY KEY (codigo_oficina, id_direccion),
    FOREIGN KEY (codigo_oficina) REFERENCES oficina(codigo_oficina),
    FOREIGN KEY (id_direccion) REFERENCES direccion(id_direccion)
);
```

## DATA MANIPULATION LANGUAJE (DML)
```sql
-- Países
INSERT INTO pais (nombre) VALUES 
    ('Estados Unidos'), ('Canadá'), ('México'), ('Brasil'), ('Argentina'), ('Chile');

-- Regiones
INSERT INTO region (nombre, id_pais) VALUES 
    ('California', 1),
    ('Quebec', 2),
    ('Jalisco', 3),
    ('São Paulo', 4),
    ('Buenos Aires', 5),
    ('Santiago', 6);

-- Ciudades
INSERT INTO ciudad (nombre, id_region) VALUES 
    ('Los Angeles', 1),
    ('Montreal', 2),
    ('Guadalajara', 3),
    ('São Paulo', 4),
    ('Buenos Aires', 5),
    ('Santiago', 6);

-- Direcciones
INSERT INTO direccion (linea_direccion1, barrio, id_ciudad) VALUES 
    ('123 Hollywood Blvd', 'Hollywood', 1),
    ('456 Maple Street', 'Downtown', 2),
    ('789 Pine Ave', 'Centro', 3),
    ('1011 Banana Blvd', 'Market', 4),
    ('1213 Apple Ave', 'North Side', 5),
    ('1415 Orange Street', 'South Side', 6);

-- Gama de productos
INSERT INTO gama_producto (gama, descripcion_texto, descripcion_html, imagen) VALUES 
    ('Electrónica', 'Dispositivos y gadgets electrónicos.', '<p>Dispositivos y gadgets electrónicos.</p>', 'electronica.jpg'),
    ('Hogar', 'Artículos para el hogar.', '<p>Artículos para el hogar.</p>', 'hogar.jpg'),
    ('Jardinería', 'Herramientas y decoraciones para jardín.', '<p>Herramientas y decoraciones para jardín.</p>', 'jardineria.jpg'),
    ('Deporte', 'Equipamiento deportivo.', '<p>Equipamiento deportivo.</p>', 'deporte.jpg'),
    ('Moda', 'Ropa y accesorios de moda.', '<p>Ropa y accesorios de moda.</p>', 'moda.jpg'),
    ('Juguetes', 'Juguetes para todas las edades.', '<p>Juguetes para todas las edades.</p>', 'juguetes.jpg');

-- Dimensiones
INSERT INTO dimensiones (id_dimensiones, ancho, alto, profundo) VALUES 
    ('D001', 30.00, 40.00, 15),
    ('D002', 50.00, 60.00, 20),
    ('D003', 10.00, 20.00, 5),
    ('D004', 100.00, 200.00, 50),
    ('D005', 5.00, 10.00, 2),
    ('D006', 120.00, 130.00, 100);

-- Productos
INSERT INTO producto (codigo_producto, nombre, id_gama, id_dimensiones, descripcion, cantidad_en_stock, precio_venta, precio_proveedor) VALUES 
    ('P001', 'Teléfono Inteligente', 'Electrónica', 'D001', 'Último modelo con cámara HD.', 100, 500.00, 300.00),
    ('P002', 'Laptop ProMax', 'Electrónica', 'D002', 'Alta capacidad y rendimiento.', 50, 1200.00, 800.00),
    ('P003', 'Blender Turbo', 'Hogar', 'D003', 'Licuadora de alta velocidad.', 150, 99.99, 60.00),
    ('P004', 'Set de Jardinería', 'Jardinería', 'D004', 'Todo lo necesario para el cuidado del jardín.', 200, 80.00, 40.00),
    ('P005', 'Pelota de Fútbol', 'Deporte', 'D005', 'Pelota oficial tamaño y peso.', 300, 25.00, 15.00),
    ('P006', 'Conjunto de Ropa', 'Moda', 'D006', 'Incluye camisa, pantalón y accesorios.', 150, 100.00, 50.00);

-- Contactos
INSERT INTO contacto (id_contacto, telefono, fax) VALUES 
    (1, '2345678901', '1098765432'),
    (2, '3456789012', '2109876543'),
    (3, '4567890123', '3210987654'),
    (4, '5678901234', '4321098765'),
    (5, '6789012345', '5432109876'),
    (6, '7890123456', '6543210987');

-- Oficinas
INSERT INTO oficina (nombre, telefono) VALUES 
    ('Oficina Central', '1234567890'),
    ('Oficina Regional', '2345678901'),
    ('Oficina Norte', '3456789012'),
    ('Oficina Sur', '4567890123'),
    ('Oficina Este', '5678901234'),
    ('Oficina_direccion Oeste', '6789012345');

-- Empleados
INSERT INTO empleado (codigo_empleado, nombre_empleado, apellido_empleado1, extension, email, codigo_oficina, codigo_jefe, puesto) VALUES 
    (1, 'Juan', 'Pérez', '1234', 'juan.perez@empresa.com', 1, 12, 'Gerente'),
    (2, 'Luisa', 'Martínez', '5678', 'luisa.martinez@empresa.com', 2, 123, 'Secretaria'),
    (3, 'Carlos', 'Gomez', '9012', 'carlos.gomez@empresa.com', 3, 1234 , 'Coordinador'),
    (4, 'María', 'Garcia', '3456', 'maria.garcia@empresa.com', 4), 12345, 'Directora',
    (5, 'Ana', 'Lopez', '7890', 'ana.lopez@empresa.com', 5, 123456, 'Asistente'),
    (6, 'Miguel', 'Hernández', '1234', 'miguel.hernandez@empresa.com', 6, 123456, 'Asistente');

-- Clientes
INSERT INTO cliente (nombre_cliente, nombre_contacto, apellido_contacto, id_contacto, id_direccion, codigo_empleado_rep_ventas, limite_credito) VALUES 
    ('Cliente 1', 'José', 'Ramírez', 1, 1, 1, 1000.00),
    ('Cliente 2', 'Carlos', 'Gomez', 2, 2, 2, 2000.00),
    ('Cliente 3', 'Marie', 'Dupon', 3, 3, 3, 3000.00),
    ('Cliente 4', 'Luigi', 'Rossi', 4, 4, 4, 4000.00),
    ('Cliente 5', 'John', 'Smith', 5, 5, 5, 5000.00),
    ('Cliente 6', 'Sara', 'Johnson', 6, 6, 6, 6000.00);

-- Pedidos
INSERT INTO pedido (codigo_pedido, fecha_pedido, fecha_esperada, estado, codigo_cliente) VALUES 
    (1001, '2024-04-21', '2024-04-28', 'Enviado', 1),
    (1002, '2024-04-21', '2024-04-28', 'Enviado', 2),
    (1003, '2024-04-22', '2024-04-29', 'Cancelado', 3),
    (1004, '2024-04-23', '2024-04-30', 'Pendiente', 4),
    (1005, '2024-04-24', '2024-05-01', 'Enviado', 5),
    (1006, '2024-04-25', '2024-05-02', 'Cancelado', 6);

-- Detalles de pedido
INSERT INTO detalle_pedido (codigo_pedido, codigo_producto, cantidad, precio_unidad, numero_linea) VALUES 
    (1001, 'P001', 2, 500.00, 1),
    (1002, 'P002', 1, 1200.00, 1),
    (1003, 'P003', 3, 99.99, 1),
    (1004, 'P004', 5, 80.00, 1),
    (1005, 'P005', 10, 25.00, 1),
    (1006, 'P006', 4, 100.00, 1);

-- Pagos
INSERT INTO pago (id_transaccion, codigo_cliente, forma_pago, fecha_pago, total) VALUES 
    ('T001', 1, 'Tarjeta de crédito', '2024-04-21', 1000.00),
    ('T002', 2, 'Efectivo', '2024-04-21', 1200.00),
    ('T003', 3, 'Tarjeta de crédito', '2024-04-22', 299.97),
    ('T004', 4, 'Efectivo', '2024-04-23', 400.00),
    ('T005', 5, 'Cheque', '2024-04-24', 250.00),
    ('T006', 6, 'Transferencia', '2024-04-25', 400.00);

-- Relaciones oficina-dirección
INSERT INTO oficina_direccion (codigo_oficina, id_direccion) VALUES 
    (1, 1),
    (2, 2),
    (3, 3),
    (4, 4),
    (5, 5),
    (6, 6);
```

## **CONSULTAS**

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.
```sql
SELECT o.codigo_oficina, c.nombre AS ciudad
FROM oficina_direccion o
JOIN oficina_direccion od ON o.codigo_oficina = od.codigo_oficina
JOIN direccion d ON od.id_direccion = d.id_direccion
JOIN ciudad c ON d.id_ciudad = c.id_ciudad;

+----------------+--------------+
| codigo_oficina | ciudad       |
+----------------+--------------+
|              1 | Los Angeles  |
|              2 | Montreal     |
|              3 | Guadalajara  |
|              4 | São Paulo    |
|              5 | Buenos Aires |
|              6 | Santiago     |
+----------------+--------------+
```

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.
```sql
SELECT c.nombre AS ciudad, o.telefono
FROM oficina_direccion o
JOIN oficina_direccion od ON o.codigo_oficina = od.codigo_oficina
JOIN direccion d ON od.id_direccion = d.id_direccion
JOIN ciudad c ON d.id_ciudad = c.id_ciudad
WHERE c.nombre LIKE '%España%' OR c.nombre LIKE '%Spain%';
```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.
```sql
SELECT nombre_empleado, apellido_empleado1, email
FROM empleado
WHERE codigo_jefe = 7;
```

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.
```sql
SELECT e.nombre_empleado as nombre , e.apellido1_empleado as apellido1, e.apellido2_empleado as apellido2, e.puesto
FROM empleado AS e WHERE e.puesto = 'Gerente';
+--------+-----------+-----------+---------+
 | nombre | apellido1 | apellido2 | puesto  |
 +--------+-----------+-----------+---------+
 | Juan   | Pérez     | Null      | Gerente |
 +--------+-----------+-----------+---------+
```

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.
```sql
SELECT nombre_empleado, apellido_empleado1, puesto
FROM empleado
WHERE puesto != 'Representante de ventas';
+-----------------+--------------------+-------------+
| nombre_empleado | apellido_empleado1 | puesto      |
+-----------------+--------------------+-------------+
| Juan            | Pérez              | Gerente     |
+-----------------+--------------------+-------------+
| Luisa           | Martínez           | Secretaria  |
+-----------------+--------------------+-------------+
| Carlos          | Gomez              | Coordinador |
+-----------------+--------------------+-------------+
| María           | Garcia             | Directora   |
+-----------------+--------------------+-------------+
| Ana             | Lopez              | Asistente   |
+-----------------+--------------------+-------------+
| Miguel          | Hernández          | Asistente   |
+-----------------+--------------------+-------------+
```

6. Devuelve un listado con el nombre de los todos los clientes españoles.
```sql
SELECT nombre_cliente
FROM cliente
JOIN direccion d ON cliente.id_direccion = d.id_direccion
JOIN ciudad c ON d.id_ciudad = c.id_ciudad
WHERE c.nombre LIKE '%España%' OR c.nombre LIKE '%Spain%';
```

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.
```sql
SELECT DISTINCT estado
FROM pedido;
+-----------+
| estado    |
+-----------+
| Enviado   |
| Cancelado |
| Pendiente |
+-----------+
```

8. Clientes que pagaron en 2008. Tres métodos.
```sql
-- Usando YEAR
SELECT DISTINCT codigo_cliente
FROM pago
WHERE YEAR(fecha_pago) = 2008;

-- Usando DATE_FORMAT
SELECT DISTINCT codigo_cliente
FROM pago
WHERE DATE_FORMAT(fecha_pago, '%Y') = '2008';

-- Sin usar funciones de fecha
SELECT DISTINCT codigo_cliente
FROM pago
WHERE fecha_pago BETWEEN '2008-01-01' AND '2008-12-31';
```

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.
```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE fecha_entrega > fecha_esperada;
```

10. Pedidos entregados antes de tiempo.
```sql
-- Usando ADDDATE
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE fecha_entrega <= ADDDATE(fecha_esperada, INTERVAL -2 DAY);

-- Usando DATEDIFF
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
FROM pedido
WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;

-- Usando operadores
-- No es posible en *SQL estándar*; se debe usar una función.
```

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.
```sql
SELECT *
FROM pedido
WHERE estado = 'Rechazado' AND YEAR(fecha_pedido) = 2009;
```

12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.
```sql
SELECT *
FROM pedido
WHERE MONTH(fecha_entrega) = 1;
```

13. Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.
```sql
SELECT *
FROM pago
WHERE forma_pago = 'Paypal' AND YEAR(fecha_pago) = 2008
ORDER BY total DESC;
```

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.
```sql
SELECT DISTINCT forma_pago
FROM pago;
+---------------------+
| forma_pago          |
+---------------------+
| Tarjeta de crédito  |
| Efectivo            |
| Cheque              |
| Transferencia       |
+---------------------+
```

15. Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.
```sql
SELECT *
FROM producto
WHERE id_gama = 'Ornamentales' AND cantidad_en_stock > 100
ORDER BY precio_venta DESC;
```

16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.
```sql
SELECT nombre_cliente, apellido_contacto
FROM cliente
WHERE codigo_cliente IN (
SELECT codigo_cliente
FROM cliente_direccion
WHERE id_direccion IN (SELECT id_direccion FROM direccion WHERE 
id_ciudad IN (SELECT id_ciudad FROM ciudad WHERE nombre = 'Madrid'))
) AND codigo_empleado_rep_ventas IN (11, 30);
```

## **Consultas multitabla (composición interna)**

Resuelva todas las consultas utilizando la sintaxis de SQL1 y SQL2. Las consultas con sintaxis de SQL2 se deben resolver con INNER JOIN y NATURAL JOIN.

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.
```sql
--SQL1
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1
FROM cliente c, empleado e
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado;

--SQL2
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado;


+----------------+-----------------+--------------------+
| nombre_cliente | nombre_empleado | apellido_empleado1 |
+----------------+-----------------+--------------------+
| Cliente 1      | Juan            | Pérez              |
| Cliente 2      | Luisa           | Martínez           |
| Cliente 3      | Carlos          | Gomez              |
| Cliente 4      | María           | Garcia             |
| Cliente 5      | Ana             | Lopez              |
| Cliente 6      | Miguel          | Hernández          |
+----------------+-----------------+--------------------+
```

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.
```sql
--SQL1 
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1
FROM cliente c, empleado e, pago p
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND c.codigo_cliente = p.codigo_cliente;

--SQL2
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
INNER JOIN pago p ON c.codigo_cliente = p.codigo_cliente;

+----------------+-----------------+--------------------+
| nombre_cliente | nombre_empleado | apellido_empleado1 |
+----------------+-----------------+--------------------+
| Cliente 1      | Juan            | Pérez              |
| Cliente 2      | Luisa           | Martínez           |
| Cliente 3      | Carlos          | Gomez              |
| Cliente 4      | María           | Garcia             |
| Cliente 5      | Ana             | Lopez              |
| Cliente 6      | Miguel          | Hernández          |
+----------------+-----------------+--------------------+
```
3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.
```sql
--SQL1
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1
FROM cliente c, empleado e
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND c.codigo_cliente NOT IN (SELECT codigo_cliente FROM pago);

--SQL2
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;

```
4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
```sql
--SQL1
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c, empleado e, pago p, oficina_direccion o, direccion d
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND c.codigo_cliente = p.codigo_cliente
AND e.codigo_oficina = o.codigo_oficina
AND o.id_direccion = d.id_direccion;

--SQL2
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
INNER JOIN pago p ON c.codigo_cliente = p.codigo_cliente
INNER JOIN oficina_direccion o ON e.codigo_oficina = o.codigo_oficina
INNER JOIN direccion d ON o.id_direccion = d.id_direccion;

+----------------+-----------------+--------------------+-----------+
| nombre_cliente | nombre_empleado | apellido_empleado1 | id_ciudad |
+----------------+-----------------+--------------------+-----------+
| Cliente 1      | Juan            | Pérez              |         1 |
| Cliente 2      | Luisa           | Martínez           |         2 |
| Cliente 3      | Carlos          | Gomez              |         3 |
| Cliente 4      | María           | Garcia             |         4 |
| Cliente 5      | Ana             | Lopez              |         5 |
| Cliente 6      | Miguel          | Hernández          |         6 |
+----------------+-----------------+--------------------+-----------+
```
5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
```sql
--SQL1
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c, empleado e, oficina_direccion o, direccion d
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND c.codigo_cliente NOT IN (SELECT codigo_cliente FROM pago)
AND e.codigo_oficina = o.codigo_oficina
AND o.id_direccion = d.id_direccion;

--SQL2
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
INNER JOIN oficina_direccion o ON e.codigo_oficina = o.codigo_oficina
INNER JOIN direccion d ON o.id_direccion = d.id_direccion
WHERE p.codigo_cliente IS NULL;

```
6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.
```sql
--SQL1
SELECT DISTINCT d.*
FROM direccion d, cliente c, ciudad ci
WHERE d.id_ciudad = ci.id_ciudad
AND ci.nombre = 'Fuenlabrada';

--SQL2
SELECT DISTINCT d.*
FROM direccion d
INNER JOIN ciudad ci ON d.id_ciudad = ci.id_ciudad
INNER JOIN cliente c ON ci.id_ciudad = c.id_ciudad
WHERE ci.nombre = 'Fuenlabrada';

```
7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
```sql
--SQL1
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c, empleado e, oficina_direccion o, direccion d
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND e.codigo_oficina = o.codigo_oficina
AND o.id_direccion = d.id_direccion;

--SQL2
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
INNER JOIN oficina_direccion o ON e.codigo_oficina = o.codigo_oficina
INNER JOIN direccion d ON o.id_direccion = d.id_direccion;

+----------------+-----------------+--------------------+-----------+
| nombre_cliente | nombre_empleado | apellido_empleado1 | id_ciudad |
+----------------+-----------------+--------------------+-----------+
| Cliente 1      | Juan            | Pérez              |         1 |
| Cliente 2      | Luisa           | Martínez           |         2 |
| Cliente 3      | Carlos          | Gomez              |         3 |
| Cliente 4      | María           | Garcia             |         4 |
| Cliente 5      | Ana             | Lopez              |         5 |
| Cliente 6      | Miguel          | Hernández          |         6 |
+----------------+-----------------+--------------------+-----------+
```
8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.
```sql
--SQL1
SELECT c.nombre_cliente, e.nombre_empleado, e.apellido_empleado1, d.id_ciudad
FROM cliente c
INNER JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
INNER JOIN oficina_direccion o ON e.codigo_oficina = o.codigo_oficina
INNER JOIN direccion d ON o.id_direccion = d.id_direccion;

--SQL2
SELECT e1.nombre_empleado AS empleado, e2.nombre_empleado AS jefe
FROM empleado e1
INNER JOIN empleado e2 ON e1.codigo_jefe = e2.codigo_empleado;

+----------------+-----------------+--------------------+-----------+
| nombre_cliente | nombre_empleado | apellido_empleado1 | id_ciudad |
+----------------+-----------------+--------------------+-----------+
| Cliente 1      | Juan            | Pérez              |         1 |
| Cliente 2      | Luisa           | Martínez           |         2 |
| Cliente 3      | Carlos          | Gomez              |         3 |
| Cliente 4      | María           | Garcia             |         4 |
| Cliente 5      | Ana             | Lopez              |         5 |
| Cliente 6      | Miguel          | Hernández          |         6 |
+----------------+-----------------+--------------------+-----------+
```
9. Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.
```sql
--SQL1
SELECT e1.nombre_empleado AS empleado, e2.nombre_empleado AS jefe, e3.nombre_empleado AS jefe_de_jefe
FROM empleado e1, empleado e2, empleado e3
WHERE e1.codigo_jefe = e2.codigo_empleado
AND e2.codigo_jefe = e3.codigo_empleado;

--SQL2
SELECT e1.nombre_empleado AS empleado, e2.nombre_empleado AS jefe, e3.nombre_empleado AS jefe_de_jefe
FROM empleado e1
INNER JOIN empleado e2 ON e1.codigo_jefe = e2.codigo_empleado
INNER JOIN empleado e3 ON e2.codigo_jefe = e3.codigo_empleado;

```
10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.
```sql
--SQL1
SELECT DISTINCT c.nombre_cliente
FROM cliente c, pedido p
WHERE c.codigo_cliente = p.codigo_cliente
AND p.fecha_entrega > p.fecha_esperada;

--SQL2
SELECT DISTINCT c.nombre_cliente
FROM cliente c
INNER JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
WHERE p.fecha_entrega > p.fecha_esperada;

```
11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.
```sql
--SQL1
SELECT DISTINCT c.nombre_cliente, p.id_gama
FROM cliente c, pedido pe, detalle_pedido dp, producto p
WHERE c.codigo_cliente = pe.codigo_cliente
AND pe.codigo_pedido = dp.codigo_pedido
AND dp.codigo_producto = p.codigo_producto;

--SQL2
SELECT DISTINCT c.nombre_cliente, p.id_gama
FROM cliente c
INNER JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
INNER JOIN detalle_pedido dp ON pe.codigo_pedido = dp.codigo_pedido
INNER JOIN producto p ON dp.codigo_producto = p.codigo_producto;

+----------------+--------------+
| nombre_cliente | id_gama      |
+----------------+--------------+
| Cliente 1      | Electrónica  |
| Cliente 2      | Electrónica  |
| Cliente 3      | Hogar        |
| Cliente 4      | Jardinería   |
| Cliente 5      | Deporte      |
| Cliente 6      | Moda         |
+----------------+--------------+
```