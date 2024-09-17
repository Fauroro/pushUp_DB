# Antigüedades DB

### Diagramas 

#### Diagrama Modelo Conceptual
![Diagrama Modelo Conceptual](https://github.com/Fauroro/pushUp_DB/blob/main/DiagramaModeloConceptual.png)

#### Diagrama DER
![DER](https://github.com/Fauroro/pushUp_DB/blob/main/DER.png)

### Creación de la Bade de Datos

A continuación se presenta el script necesario para la creación de la Base de Datos.

```sql
-- Crear base de datos si no existe
CREATE DATABASE IF NOT EXISTS AntiguedadesDB;
USE AntiguedadesDB;

-- Crear tabla pais
CREATE TABLE IF NOT EXISTS pais (
    pais_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);

-- Crear tabla ciudad
CREATE TABLE IF NOT EXISTS ciudad (
    ciudad_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    pais_id INT NOT NULL,
    FOREIGN KEY (pais_id) REFERENCES pais(pais_id)
);

-- Crear tabla origen
CREATE TABLE IF NOT EXISTS origen (
    origen_id INT AUTO_INCREMENT PRIMARY KEY,
    ciudad_id INT NOT NULL,
    FOREIGN KEY (ciudad_id) REFERENCES ciudad(ciudad_id)
);

-- Crear tabla categoria
CREATE TABLE IF NOT EXISTS categoria (
    categoria_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);

-- Crear tabla epoca
CREATE TABLE IF NOT EXISTS epoca (
    epoca_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);

-- Crear tabla Estados de Conservación
CREATE TABLE IF NOT EXISTS estadoconservacion (
    estado_conservacion_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);

-- Crear tabla status
CREATE TABLE IF NOT EXISTS status (
    status_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE
);

-- Crear tabla antiguedades
CREATE TABLE IF NOT EXISTS antiguedades (
    antiguedad_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    descripcion TEXT,
    categoria_id INT NOT NULL,
    epoca_id INT NOT NULL,
    origen_id INT NOT NULL,
    estado_conservacion_id INT NOT NULL,
    status_id INT NOT NULL,
    precio DECIMAL(10, 2) NOT NULL,
    fecha_agregado DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
    FOREIGN KEY (categoria_id) REFERENCES categoria(categoria_id),
    FOREIGN KEY (epoca_id) REFERENCES epoca(epoca_id),
    FOREIGN KEY (origen_id) REFERENCES origen(origen_id),
    FOREIGN KEY (estado_conservacion_id) REFERENCES estadoconservacion(estado_conservacion_id),
    FOREIGN KEY (status_id) REFERENCES status(status_id)
);

-- Crear tabla fotos
CREATE TABLE IF NOT EXISTS fotos (
    foto_id INT AUTO_INCREMENT PRIMARY KEY,
    antiguedad_id INT NOT NULL,
    url VARCHAR(255) NOT NULL,
    FOREIGN KEY (antiguedad_id) REFERENCES antiguedades(antiguedad_id)
);

-- Crear tabla roles
CREATE TABLE IF NOT EXISTS roles (
    rol_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE
);

-- Crear tabla usuario
CREATE TABLE IF NOT EXISTS usuario (
    usuario_id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    rol_id INT NOT NULL,
    fecha_registro DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
    FOREIGN KEY (rol_id) REFERENCES roles(rol_id)
);

-- Crear tabla transacciones
CREATE TABLE IF NOT EXISTS transacciones (
    transaccion_id INT AUTO_INCREMENT PRIMARY KEY,
    comprador_id INT,
    vendedor_id INT,
    antiguedad_id INT NOT NULL,
    fecha_transaccion DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
    precio_venta DECIMAL(10, 2) NOT NULL,
    estado_transaccion_id INT NOT NULL,
    FOREIGN KEY (comprador_id) REFERENCES usuario(usuario_id),
    FOREIGN KEY (vendedor_id) REFERENCES usuario(usuario_id),
    FOREIGN KEY (antiguedad_id) REFERENCES antiguedades(antiguedad_id),
    FOREIGN KEY (estado_transaccion_id) REFERENCES status(status_id)
);

-- Crear tabla Historial de Actividades
CREATE TABLE IF NOT EXISTS historialactividades (
    actividad_id INT AUTO_INCREMENT PRIMARY KEY,
    usuario_id INT NOT NULL,
    accion VARCHAR(255) NOT NULL,
    antiguedad_id INT NOT NULL,
    fecha_actividad DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (usuario_id) REFERENCES usuario(usuario_id),
    FOREIGN KEY (antiguedad_id) REFERENCES antiguedades(antiguedad_id)
);

-- Crear índices (opcional para mejorar el rendimiento)
CREATE INDEX idx_email ON usuario(email);
CREATE INDEX idx_categoria ON antiguedades(categoria_id);
CREATE INDEX idx_epoca ON antiguedades(epoca_id);
CREATE INDEX idx_origen ON antiguedades(origen_id);
CREATE INDEX idx_estado_conservacion ON antiguedades(estado_conservacion_id);
CREATE INDEX idx_status ON antiguedades(status_id);
CREATE INDEX idx_comprador ON transacciones(comprador_id);
CREATE INDEX idx_vendedor ON transacciones(vendedor_id);

```



### Inserción de datos

A continuación, se presentan los insert necesarios para probar la base de datos.

```sql
-- Inserción de países
INSERT INTO pais (nombre) VALUES 
('España'), 
('Francia'), 
('Italia');

-- Inserción de ciudades
INSERT INTO ciudad (nombre, pais_id) VALUES 
('Madrid', 1),
('Barcelona', 1),
('París', 2),
('Roma', 3);

-- Inserción de orígenes
INSERT INTO origen (ciudad_id) VALUES 
(1), -- Madrid
(2), -- Barcelona
(3), -- París
(4); -- Roma

-- Inserción de categorías
INSERT INTO categoria (nombre) VALUES 
('Muebles'), 
('Pinturas'), 
('Esculturas');

-- Inserción de épocas
INSERT INTO epoca (nombre) VALUES 
('Renacimiento'), 
('Barroco'), 
('Moderno');

-- Inserción de estados de conservación
INSERT INTO estadoconservacion (nombre) VALUES 
('Excelente'), 
('Buena'), 
('Regular');

-- Inserción de estados
INSERT INTO status (nombre) VALUES 
('Disponible'), 
('Vendido'), 
('Reservado');

-- Inserción de antigüedades
INSERT INTO antiguedades (nombre, descripcion, categoria_id, epoca_id, origen_id, estado_conservacion_id, status_id, precio) VALUES 
('Silla del Renacimiento', 'Silla antigua del Renacimiento.', 1, 1, 1, 1, 1, 1500.00),
('Retrato de la Familia Real', 'Pintura barroca de la familia real.', 2, 2, 2, 2, 1, 2500.00),
('Estatua de mármol', 'Escultura de mármol de la antigua Roma.', 3, 3, 4, 1, 1, 3500.00);

-- Inserción de roles
INSERT INTO roles (nombre) VALUES 
('Comprador'), 
('Vendedor'), 
('Admin');

-- Inserción de usuarios
INSERT INTO usuario (nombre, email, password_hash, rol_id) VALUES 
('Juan Pérez', 'juan.perez@example.com', 'hash123', 1),
('Ana Martínez', 'ana.martinez@example.com', 'hash456', 2),
('Carlos Gómez', 'carlos.gomez@example.com', 'hash789', 1);

-- Inserción de transacciones
INSERT INTO transacciones (comprador_id, vendedor_id, antiguedad_id, precio_venta, estado_transaccion_id) VALUES 
(1, 2, 1, 1500.00, 2),
(1, 2, 2, 2500.00, 2),
(3, 2, 3, 3500.00, 2);
INSERT INTO transacciones (comprador_id, vendedor_id, antiguedad_id, fecha_transaccion, precio_venta, estado_transaccion_id) VALUES 
(1, 2, 1, '2024-09-5 16:52:05' , 1500.00, 1),
(2, 1, 2, '2024-09-10 16:52:05', 2500.00, 1);



-- Inserción de historial de actividades
INSERT INTO historialactividades (usuario_id, accion, antiguedad_id) VALUES 
(1, 'Compró', 1),
(1, 'Compró', 2),
(3, 'Compró', 3);

```



### Consultas para la Base de Datos del Negocio de Antigüedades

#### Consulta para listar todas las antigüedades disponibles para la venta:

* "Obtén una lista de todas las piezas antiguas que están actualmente disponibles para la venta, incluyendo el nombre de la pieza, su categoría, precio y estado de conservación."

  ```sql
  SELECT a.nombre AS nombre_antiguedad, c.nombre AS categoria, a.precio, e.nombre AS estado_conservacion
  FROM antiguedades a
  INNER JOIN categoria c ON a.categoria_id = c.categoria_id
  INNER JOIN estadoconservacion e ON a.estado_conservacion_id = e.estado_conservacion_id
  WHERE a.status_id = (SELECT status_id FROM status WHERE nombre = 'Disponible');
  
  
  +----------------------------+------------+---------+---------------------+
  | nombre_antiguedad          | categoria  | precio  | estado_conservacion |
  +----------------------------+------------+---------+---------------------+
  | Estatua de mármol          | Esculturas | 3500.00 | Excelente           |
  | Silla del Renacimiento     | Muebles    | 1500.00 | Excelente           |
  | Retrato de la Familia Real | Pinturas   | 2500.00 | Buena               |
  +----------------------------+------------+---------+---------------------+
  3 rows in set (0.01 sec)
  ```

  

#### Consulta para buscar antigüedades por categoría y rango de precio:

* "Busca todas las antigüedades dentro de una categoría específica (por ejemplo, 'Muebles') que tengan un precio dentro de un rango determinado (por ejemplo, entre 500 y 2000 dólares)."

  ```sql
  SELECT a.nombre AS nombre_antiguedad, a.precio 
  FROM antiguedades a
  INNER JOIN categoria c ON a.categoria_id = c.categoria_id
  WHERE c.nombre = 'Muebles' AND a.precio BETWEEN 500 AND 2000;
  
  
  +------------------------+---------+
  | nombre_antiguedad      | precio  |
  +------------------------+---------+
  | Silla del Renacimiento | 1500.00 |
  +------------------------+---------+
  1 row in set (0.00 sec)
  ```

#### Consulta para mostrar el historial de ventas de un cliente específico:

* "Muestra todas las piezas antiguas que un cliente específico ha vendido, incluyendo la fecha de la venta, el precio de venta y el comprador."

  ```sql
  SELECT a.nombre AS nombre_antiguedad,  t.fecha_transaccion, t.precio_venta, u.nombre AS comprador
  FROM transacciones t
  INNER JOIN antiguedades a ON t.antiguedad_id = a.antiguedad_id
  INNER JOIN usuario u ON t.comprador_id = u.usuario_id
  WHERE u.nombre = 'Juan Pérez';
  
  
  +----------------------------+---------------------+--------------+------------+
  | nombre_antiguedad          | fecha_transaccion   | precio_venta | comprador  |
  +----------------------------+---------------------+--------------+------------+
  | Silla del Renacimiento     | 2024-09-17 17:42:09 |      1500.00 | Juan Pérez |
  | Retrato de la Familia Real | 2024-09-17 17:42:09 |      2500.00 | Juan Pérez |
  | Silla del Renacimiento     | 2024-09-05 16:52:05 |      1500.00 | Juan Pérez |
  +----------------------------+---------------------+--------------+------------+
  3 rows in set (0.00 sec)
  ```

  

#### Consulta para obtener el total de ventas realizadas en un periodo de tiempo:

* "Calcula el total de ventas realizadas en un período específico, por ejemplo, durante el último mes."

  ```sql
  SELECT SUM(precio_venta) AS total_ventas
  FROM transacciones
  WHERE fecha_transaccion BETWEEN DATE_SUB(CURDATE(), INTERVAL 1 MONTH) AND CURDATE();
  
  
  +--------------+
  | total_ventas |
  +--------------+
  |      4000.00 |
  +--------------+
  1 row in set (0.00 sec)
  ```

  

#### Consulta para encontrar los clientes más activos (con más compras realizadas):

* "Identifica los clientes que han realizado la mayor cantidad de compras en la plataforma."

  ```sql
  SELECT u.nombre, COUNT(t.transaccion_id) AS cantidad_compras
  FROM transacciones t
  INNER JOIN usuario u ON t.comprador_id = u.usuario_id
  GROUP BY u.nombre
  ORDER BY cantidad_compras DESC;
  
  
  +--------------+------------------+
  | nombre       | cantidad_compras |
  +--------------+------------------+
  | Juan Pérez   |                3 |
  | Ana Martínez |                1 |
  | Carlos Gómez |                1 |
  +--------------+------------------+
  3 rows in set (0.00 sec)
  ```

  

#### Consulta para listar las antigüedades más populares por número de visitas o consultas:

* "Muestra las piezas antiguas que han recibido la mayor cantidad de visitas o consultas por parte de los usuarios.

  ```sql
  
  ```

  

#### Consulta para listar las antigüedades vendidas en un rango de fechas específico:

* "Obtén una lista de todas las piezas antiguas que se han vendido dentro de un rango de fechas específico, incluyendo la información del vendedor y comprador."

  ```sql
  SELECT a.nombre AS nombre_antiguedad, t.fecha_transaccion, u1.nombre AS vendedor, u2.nombre AS comprador
  FROM transacciones t
  INNER JOIN antiguedades a ON t.antiguedad_id = a.antiguedad_id
  INNER JOIN usuario u1 ON t.vendedor_id = u1.usuario_id
  INNER JOIN usuario u2 ON t.comprador_id = u2.usuario_id
  WHERE t.fecha_transaccion BETWEEN '2024-01-01' AND '2024-12-31';
  
  
  +----------------------------+---------------------+--------------+--------------+
  | nombre_antiguedad          | fecha_transaccion   | vendedor     | comprador    |
  +----------------------------+---------------------+--------------+--------------+
  | Silla del Renacimiento     | 2024-09-17 17:42:09 | Ana Martínez | Juan Pérez   |
  | Retrato de la Familia Real | 2024-09-17 17:42:09 | Ana Martínez | Juan Pérez   |
  | Estatua de mármol          | 2024-09-17 17:42:09 | Ana Martínez | Carlos Gómez |
  | Silla del Renacimiento     | 2024-09-05 16:52:05 | Ana Martínez | Juan Pérez   |
  | Retrato de la Familia Real | 2024-09-10 16:52:05 | Juan Pérez   | Ana Martínez |
  +----------------------------+---------------------+--------------+--------------+
  5 rows in set (0.00 sec)
  ```

  

#### Consulta para obtener un informe de inventario actual:

* "Genera un informe del inventario actual de antigüedades disponibles para la venta, mostrando la cantidad de artículos por categoría."

  ```sql
  SELECT c.nombre AS categoria, COUNT(a.antiguedad_id) AS cantidad_articulos
  FROM antiguedades a
  INNER JOIN categoria c ON a.categoria_id = c.categoria_id
  WHERE a.status_id = (SELECT status_id FROM status WHERE nombre = 'Disponible')
  GROUP BY c.nombre;
  
  
  +------------+--------------------+
  | categoria  | cantidad_articulos |
  +------------+--------------------+
  | Esculturas |                  1 |
  | Muebles    |                  1 |
  | Pinturas   |                  1 |
  +------------+--------------------+
  3 rows in set (0.00 sec)
  ```

  