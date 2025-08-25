
## Pasos para implementar una base de datos en SQL Server 2022

### 1. Primero descargamos la imagen de SQL 2022 para poder inicializar con nuestra implementacion.

docker pull mcr.microsoft.com/mssql/server:2022-latest

### 2. Después creamos un contenedor de docker para poner trabajar  debes de configurar una contraseña segura y un nombre para poder conectar con nuestra base de datos.

docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=Admin12345" -p 1433:1433 --name sqlserver -d mcr.microsoft.com/mssql/server:2022-latest

### 3. Después de verificar la conexión   vamos a ir    a verificar y conectar con nuestra base de datos  y completamos los datos que nos pide y damos conectar .

### 4. Cuando conectamos nos manda una lista de archivo del mismo contenedor  donde nos muestra todas las carpetas de  base de datos.

### 5. Después de esto instalamos la extensión de SQL Server(mssql)

### 6. Aquí iniciamos y creamos un archivo y colocamos el código para crear  nuestra  primera Base de Datos y las tablas.

select @@version;

DROP DATABASE IF EXISTS db_SalesClothes;
CREATE DATABASE db_SalesClothes;

USE db_SalesClothes;

CREATE TABLE client
(
    id int,
    type_document char(3),
    number_document char(15),
    names varchar(60),
    last_name varchar(90),
    email varchar(80),
    cell_phone char(9),
    birthdate date,
    active bit
    CONSTRAINT client_pk PRIMARY KEY (id)
);

EXEC sp_columns @table_name = 'client';

SELECT * FROM INFORMATION_SCHEMA.TABLES;

DROP TABLE client;


-- TABLA CLIENT
CREATE TABLE client (
    id INT PRIMARY KEY,
    type_document CHAR(3) NOT NULL,
    number_document CHAR(15) NOT NULL,
    names VARCHAR(60) NOT NULL,
    last_name VARCHAR(90) NOT NULL,
    email VARCHAR(80),
    birthdate DATE,
    active BIT NOT NULL
);

-- TABLA SELLER
CREATE TABLE seller (
    id INT PRIMARY KEY,
    type_document CHAR(3) NOT NULL,
    number_document CHAR(15) NOT NULL,
    names VARCHAR(60) NOT NULL,
    last_name VARCHAR(90) NOT NULL,
    salary DECIMAL(8,2) NOT NULL,
    cell_phone CHAR(9),
    email VARCHAR(80),
    active BIT NOT NULL
);

-- TABLA CLOTHES
CREATE TABLE clothes (
    id INT PRIMARY KEY,
    descriptions VARCHAR(60) NOT NULL,
    brand VARCHAR(60),
    amount INT NOT NULL,
    size VARCHAR(10),
    price DECIMAL(8,2) NOT NULL,
    active BIT NOT NULL
);

-- TABLA SALE
CREATE TABLE sale (
    id INT PRIMARY KEY,
    date_time DATETIME NOT NULL,
    seller_id INT NOT NULL,
    client_id INT NOT NULL,
    active BIT NOT NULL
);

-- TABLA SALE_DETAIL
CREATE TABLE sale_detail (
    id INT PRIMARY KEY,
    sale_id INT NOT NULL,
    clothes_id INT NOT NULL,
    amount INT NOT NULL
);

### 7. De aquí realizamos las relaciones entre las tablas.


/* Relacionar tabla SALE con tabla SELLER */
ALTER TABLE sale
ADD CONSTRAINT sale_seller FOREIGN KEY (seller_id)
REFERENCES seller(id)
ON UPDATE CASCADE
ON DELETE CASCADE;
GO

/* Relacionar tabla SALE con tabla CLIENT */
ALTER TABLE sale
ADD CONSTRAINT sale_client FOREIGN KEY (client_id)
REFERENCES client(id)
ON UPDATE CASCADE
ON DELETE CASCADE;
GO

/* Relacionar tabla SALE_DETAIL con tabla SALE */
ALTER TABLE sale_detail
ADD CONSTRAINT sale_detail_sale FOREIGN KEY (sale_id)
REFERENCES sale(id)
ON UPDATE CASCADE
ON DELETE CASCADE;
GO

/* Relacionar tabla SALE_DETAIL con tabla CLOTHES */
ALTER TABLE sale_detail
ADD CONSTRAINT sale_detail_clothes FOREIGN KEY (clothes_id)
REFERENCES clothes(id)
ON UPDATE CASCADE
ON DELETE CASCADE;
GO

### Y vemos los enlaces con el siguiente comando 


/* Ver relaciones creadas entre las tablas de la base de datos */
SELECT 
    fk.name AS [Constraint],                               -- Nombre de la relación
        OBJECT_NAME(fk.parent_object_id) AS [Tabla],           -- Tabla hija (la que tiene la FK)
            COL_NAME(fc.parent_object_id, fc.parent_column_id) AS [Columna FK], -- Columna que actúa como FK
                OBJECT_NAME(fk.referenced_object_id) AS [Tabla base],  -- Tabla padre (la que tiene la PK)
                    COL_NAME(fc.referenced_object_id, fc.referenced_column_id) AS [Columna PK] -- Columna PK referenciada
                    FROM sys.foreign_keys fk
                    INNER JOIN sys.foreign_key_columns fc 
                        ON fk.OBJECT_ID = fc.constraint_object_id
                        GO

****
