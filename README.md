# Apuntes DDL y DML

## Índice

- [DDL](#DDL)

    - [Comandos principales DDL](#Comandos-principales-DDL)

    - [CREATE](#CREATE)

    - [DROP](#DROP)

    - [ALTER](#ALTER)

        - [Usar ALTER para modificar una constraint incorrecta](#Usar-ALTER-para-modificar-una-constraint-incorrecta)

- [DML](#DML)

    - [Comandos principales DML](#Comandos-principales-DML)

    - [INSERT](#INSERT)

    - [UPDATE](#UPDATE)

    - [DELETE](#DELETE)

- [EXTRA](#EXTRA)

- [Ejercicio de ejemplo](#ejercicio-de-ejemplo)

- [MariaDB](#MariaDB)

    - [Instalación de MariaDB](#instalación-de-mariaDB)

    - [Creación de base de datos Ejemplo 1](#Creación-de-base-de-datos-Ejemplo-1)

    - [Creación de base de datos Ejemplo 2](#Creación-de-base-de-datos-Ejemplo-2)

    - [Meta uso MariaDB](#Meta-uso-MariaDB)

        - [SHOW DATABASES](#SHOW-DATABASES)

        - [SHOW TABLES](#SHOW-TABLES)

        - [SHOW COLUMNS](#SHOW-COLUMNS)

        - [SHOW CREATE TABLE](#SHOW-CREATE-TABLE)

        - [WHERE y LIKE](#WHERE-y-LIKE)



> ----------------------------------------



# DDL


## Comandos principales DDL


- CREATE

- DROP

- ALTER


> -------


## CREATE

Sirve para crear algo, como una base de datos:

```
CREATE DATABASE myDB,
```
```
CREATE SCHEMA myOtherDB,
```

El comando completo sería:

```
CREATE (DATABASE|SCHEMA)
[IF NOT EXISTS] nombreDB
[CHARACTER SET nombreDeCharset],
```

También sirve para crear una tabla:

```
CREATE TABLE <nombre de tabla> (
<atributo1> <dominio1> [NOT NULL] [DEFAULT <x>],
...
<atributoN> <dominioN> [NOT NULL] [DEFAULT <x>],
[restricción1],
...
[restricciónN]
);
```

Una constraint para hacer una clave primaria sería:

```
[restricción1]:  
[CONSTRAINT <nombre de restricción>]
PRIMARY KEY (<atributos>),
```

Mientras que una constraint para una clave ajena sería:

```
[<CONSTRAINT <nombe-de-restricción>]
  FOREIGN KEY (<atributos>)
    REFERENCES <nombre-de-tabla-referencias>
      [(<atributos-referencia>)]
  [MATCH FULL|PARTIAL]
  [ON DELETE CASCADE|NO ACTION|SET NULL|SET DEFAULT]
  [ON UPDATE CASCADE|NO ACTION|SET NULL|SET DEFAULT]
```

> ON DELETE y ON UPDATE es por defecto NO ACTION si no se introduce nada.

> MATCH FULL respeta los NULL y NO ACTION


UNIQUE permite crear claves secundarias, es decir, claves candidatas a clave primarias. (Lo cual significa que no se pueden repetir.)

```
[<CONSTRAINT <nombe-de-restricción>]
  UNIQUE (<atributos>),
```

Un ejemplo:

```
CONTRAINT Persona_DNI_UNIQUE    

UNIQUE DNI,
```


> -------


### CHECK

>No cae en el examen, pero la añado igualmente.

Comprueba una constraint:

```
<CONSTRAINT <nombe-de-restricción>]

CHECK (atributoA IN  ('valor1',...,'valorN'))

[[NOT] DEFERRABLE]

[INITIALLY INMEDIATE|DEFERRED],
```

Ejemplos:

```
CONSTRAINT check_objetivo_positivo
CHECK objetivo > 0,
```

```
CONSTRAINT check_sueldo_maximo_dept_A
CHECK sueldo (
  SELECT suelo
  FROM empregado
  WHERE dept='A')
DEFERRABLE
INITIALLY DEFERRED,
```


> -------


## DROP

Sirve para **borrar**. Ya sea una tabla, base de datos o una constraint. (De lo cual hay ejemplos en el ALTER).

```
DROP TABLE
[IF EXISTS] <nombre-de-la-tabla>
[CASCADE|RESTRICT];
```

```
DROP SCHEMA
[IF EXISTS] <nombre-de-la-base-de-datos>
[CASCADE|RESTRICT]
```

> CASCADE

Borra aunque esté llena.

> RESTRICT

No permite borrar si está llena.


> -------


## ALTER

ALTER sirve para editar tablas ya creadas.

```
ALTER TABLE <nombre-de-la-tabla>

ADD [COLUMN] <atributo1> <dominio1> [NOT NULL] [DEFAULT <x>]
```

Añade columna

```
ALTER TABLE <nombre-de-la-tabla>

DROP [COLUMN] <atributo1> [CASCADE|RESTRICT]
```

Borra columna

```
ALTER TABLE <nombre-de-la-tabla>

ADD <restricción>
```

Añade restricción

```
ALTER TABLE <nombre-de-la-tabla>

DROP <restricción>
```

Borra restricción


### Usar ALTER para modificar una constraint incorrecta

Para cambiar una constraint ya creada lo mejor es no modificarla, si no primero hacer un DROP a la constraint:

```
ALTER TABLE Profesor
	DROP CONSTRAINT FK_Grupo_Profesor;
```

Y después añadirla correctamente con un ADD, lo cual nos evita que pueda haber problemas inesperados.

```
ALTER TABLE Profesor
	ADD CONSTRAINT FK_Grupo_Profesor
		FOREIGN KEY			(N_Grupo, N_Departamento)
		REFERENCES Grupo	(Nome_Grupo, Nome_Departamento)
		ON DELETE SET NULL
		ON UPDATE NO ACTION;
```


> ----------------------------------------


# DML

## Comandos principales DDL


- INSERT

- UPDATE

- DELETE


> -------


## INSERT

```
INSERT INTO <nome-da-táboa>

[(<atributo1>, <atributo2>,...)]

VALUES
 (<valor1A>, <valor2A,...),
 (<valor1B>, <valor2B,...),
 ...
 ```

 O bien podemos usar

 ```
 SELECT ... );
```

Por ejemplo:


```
INSERT INTO world
(name,continent,area)
VALUES
 ('Spain','Europe',100),
 ('Portugal,'Europe',10);
 ```

Mete España y Portugal en world.

```
INSERT INTO world
(name,continent,area)
SELECT nombre, continente, area
FROM mundo
WHERE continente='Europa';
```

Pasa todos los paises que tienen como continente Europa de la tabla mundo a world.

```
INSERT INTO world
(name,continent,area, gdp)
SELECT nombre, continente, area
FROM mundo
WHERE continente='Europa';
```

>Este **NO** funciona

gpd sobra y como no hay cuatro en la tabla a la que estas copiando, falla.


> -------


## UPDATE

```
UPDATE <nome-da-taboa>
SET <atributo1> = <valor1>,
    <atributo2> = <valor2>
[WHERE <predicado>];
```

Por ejemplo:

```
UPDATE world
SET continent='Africa'
WHERE name='Spain'
OR name='Portugal';
```

Esto pone el continente de todos los países llamados Spain o Portugal.

Para borrar los datos de una tabla, se puede usar:

```
UPDATE world
SET continent=NULL
WHERE name='Spain'
OR name='Portugal';
```

O bien lo siguiente si no acepta NULLs

```
UPDATE world
SET continent='Vacio'
WHERE name='Spain'
OR name='Portugal';
```


> -------


## DELETE

```
DELETE FROM <nombre-da-taboa>
[WHERE <predicados>];
```

Por ejemplo:

```
DELETE FROM world
WHERE continent='Europe';
```

Esto borra todos los países que tengan como continente Europe


> ----------------------------------------


## EXTRA

En un esquema de base de datos, las líneas siguen este diseño:

```
Original
   ^
   |
"Copia"
```

La "copia" recibe el valor desde el original. La flecha apunta a donde se coje el valor, no es una flecha que señala como se "propaga".


> -------


En un esquema de base de datos, las siguientes letras indican que se necesita poner al crear una tabla:

**C** ascade (CASCADE)

**R** estricted (NO ACTION)

**N** ull (SET NULL)

**D** efault (SET DEFAULT) *(No es importante)*


> -------


- NÚMEROS:

    - INTERGER

    - DECIMAL

    - REAL

- TEXTO:

    - CHAR (Fija, limitada)

    - VARCHAR (Variable, limitada)

    - TEXT (variable, ilimitada)

- FECHAS:

    - DATE (dia, mes, año)

    - TIME (hora, minuto, segundo)

    - TIMESTAMP (DATE+TIME)

- OTROS:

    - BOOLEAN: (true, false o NULL)

    - MONEY


> -------


>Recordatorios generales para el examen.


Elephant SQL no permite claves ajenas compuestas (de dos o más claves primarias) y necesitas crear el mismo número de claves ajenas que claves principales. (Mal: A1 + B1 -> A2 Bien: A1 + B1 -> A2 + B2)


> -------


CREATE DOMAIN es opcional, no hace falta estudiarlo, pero evita tener que poner CHAR(x) en todos.

```
CREATE DOMAIN Nome_Valido CHAR(30);

CREATE DOMAIN Tipo_Codigo CHAR (5);

CREATE DOMAIN Tipo_DNI CHAR(9);
```

```
CREATE TABLE Departamento (
 Nome_Departamento 	Nome_Valido 	PRIMARY KEY,
 Teléfono		CHAR(9) 	NOT NULL,
 Director		Nome_Valido,
 -- FK Director 
);
```

>**RECUERDA:** Aunque Tipo_DNI sea un DOMAIN CHAR(9), no lo uses para "Teléfono". Ambos son de longitud 9, pero es una chapuza. Es mejor crear otro DOMAIN llamado Tipo_Telefono que sea CHAR(9) también.


> -------


**No meter los FOREIGN KEY** al principio, poner notas y usar ALTER TABLE después ayuda a evitar problemas.

Ejemplo:

```
CREATE TABLE Departamento (
 Nome_Departamento 	Nome_Valido 	PRIMARY KEY,
 Teléfono		CHAR(9) 	NOT NULL,
 Director		Nome_Valido,
 -- FK Director 
);
```

"-- FK Director" nos indica que tenemos que añadir la FOREIGN KEY para este más tarde.

```
ALTER TABLE Departamento
 ADD CONSTRAINT FK_Profesor_Departamento
  FOREIGN KEY (Director) 
  REFERENCES Profesor (DNI) 
  ON DELETE SET NULL
  ON UPDATE CASCADE;
```


> ----------------------------------------


## Ejercicio de ejemplo


Ejercicio: 
![404](https://github.com/davidgchaves/first-steps-with-git-and-github-wirtz-asir1-and-dam1/blob/master/exercicios-ddl/1-proxectos-de-investigacion/img/1-proxectos-de-investigacion-relacional.jpeg)

> En el esquema la relación Proxecto a Grupo está mal en la imagen del esquema, no es Cascada, es borrado a NULL por que es opcional.


```
CREATE DOMAIN Nome_Valido CHAR(30);

CREATE DOMAIN Tipo_Codigo CHAR (5);

CREATE DOMAIN Tipo_DNI CHAR(9);
```

```
CREATE TABLE Sede (
 -- Nome_Sede 	           Nome_Valido 	       PRIMARY KEY,   <--- Esta manera o la otra primary key, pero esta no permit primary keys de más de 1 campo.
 Nome_Sede	                Nome_Valido,
 Campus		                   Nome_Valido,
 CONSTRAINT PK_Sede
  PRIMARY KEY (Nome_Sede)
);
```

```
CREATE TABLE Departamento (
 Nome_Departamento 	        Nome_Valido 	PRIMARY KEY,
 Teléfono		                        CHAR(9) 	        NOT NULL,
 Director		                         Nome_Valido,
 -- FK Director 
);
```

```
CREATE TABLE Ubicacion (
 Nome_Sede		                   Nome_Valido,
 Nome_Departamento 	        Nome_Valido,
 CONSTRAINT PK_Ubicación
  PRIMARY KEY (Nome_Sede, Nome_Departamento),
 CONSTRAINT FK_Sede_Ubicacion   <--- A partir de aquí añadiríamos con un ALTER pero al poner las otras dos tables antes permtie hacerlo sin romperlo
  FOREIGN KEY (Nome_Sede)
  REFERENCES Sede (Nome_Sede)
  ON DELETE CASCADE
  ON UPDATE CASCADE,
 CONSTRAINT FK_Departamento_Ubicacion
  FOREIGN KEY (Nome_Departamento)
  REFERENCES Departamento (Nome_Departamento)
  ON DELETE CASCADE
  ON UPDATE CASCADE
);
```

```
CREATE TABLE Grupo (
 Nome_Grupo		             Nome_Valido,
 Nome_Departamento	   Nome_Valido,
 Area			                      Nome_Valido 	       NOT NULL,
 Lider			                       Nome_Valido,
 CONSTRAINT PK_GrupoDepartamento
  PRIMARY KEY (Nome_Grupo, Nome_Departamento),
 -- FK Departamento
 CONSTRAINT FK_Departamento_Grupo
  FOREIGN KEY (Nome_Departamento)
  REFERENCES Departamento (Nome_Departamento)
  ON DELETE CASCADE
  ON UPDATE CASCADE
 -- FK Profesor
);
```

```
CREATE TABLE Profesor (
 DNI			                     Tipo_DNI,
 Nom_Profesor		          Nome_Valido 	      NOT NULL,
 Titulacion		                   VARCHAR(20) 		   NOT NULL,
 Experiencia		              Integer,
 N_Grupo		                  Nome_Valido,
 N_Departamento	        	Nome_Valido,
 -- FK Grupo
 CONSTRAINT FK_Grupo_Profesor
  FOREIGN KEY Grupo (N_Grupo, N_Departamento)
  REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
  ON DELETE SET NULL
  ON UPDATE CASCADE
);
```

```
CREATE TABLE Proxecto (
 Codigo_Proxecto             Tipo_Código	       PRIMARY KEY,
 Nome_Proxecto		         Nome_Válido	      NOT NULL,
 Orzamento		               	MONEY 	            	NOT NULL,
 Data_Inicio	                   DATE 	            	  NOT NULL,
 Data_Fin	                    	DATE,
 N_Gr			                      Nome_Valido,
 N_Dep		                    	Nome_Valido,
 CONSTRAINT UNI_NomeProxecto
  UNIQUE (Nome_Proxecto),
 CONSTRAINT Check_Dates
  CHECK (Data_Inicio < Data_Fin),
 CONSTRAINT FK_Grupo_Proxecto
  FOREIGN KEY (N_Gr, N_Dep)
  REFERENCES Grupo
  ON DELETE CASCADE
  ON UPDATE CASCADE
);
```

```
ALTER TABLE Departamento
 ADD CONSTRAINT FK_Profesor_Departamento
  FOREIGN KEY (Director) 
  REFERENCES Profesor (DNI) 
  ON DELETE SET NULL
  ON UPDATE CASCADE;
```

```  
ALTER TABLE Grupo
 ADD CONSTRAINT FK_Profesor_Grupo
  FOREIGN KEY (Lider)
  REFERENCES Profesor (DNI)
  ON DELETE SET NULL
  ON UPDATE CASCADE;
```

```
  CREATE TABLE Participa (
 DNI			                	Tipo_DNI,
 Codigo_Proxecto           Tipo_Código,		
 Data_Inicio		             DATE		        	NOT NULL,
 Data_Cese		             	DATE,		
 Dedicacion		                INTEGER		    	NOT NULL,
 CONSTRAINT PK_DNICodigoProxecto
  PRIMARY KEY (DNI, Codigo_Proxecto),
 CONSTRAINT FK_Profesor_Participa
  FOREIGN KEY (DNI)
  REFERENCES Profesor (DNI)
  ON DELETE NO ACTION
  ON UPDATE CASCADE,
 CONSTRAINT FK_Proxecto_Participa
  FOREIGN KEY (Codigo_Proxecto)
  REFERENCES Proxecto (Codigo_Proxecto)
  ON DELETE NO ACTION
  ON UPDATE CASCADE,
 CONSTRAINT Check_Dates2
  CHECK (Data_Inicio < Data_Cese),
);
```



> ----------------------------------------



# MariaDB


## Instalación de MariaDB


Empezamos abriendo una terminal.

![FOTO1](./img/Foto1.PNG)

Vamos a la página de MariaDB y seleccionamos nuestra versión de Ubuntu. Lo cual nos enseña los pasos que debemos de seguir.

![FOTO1A](./img/Foto1A.PNG)

Escribimos el primer paso que nos pone en la página: `sudo apt-get install software-properties-common`

![FOTO1B](./img/Foto1B.PNG)

Escribimos el siquiente paso: `sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'`

![FOTO1C](./img/Foto1C.PNG)

Y escribimos el último paso de esta parte: `sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] http://mariadb.mirror.triple-it.nl/repo/10.4/ubuntu bionic main'`

![FOTO1D](./img/Foto1D.PNG)

Después escribimos sudo apt-get update y dejamos que se actualize.

![FOTO2](./img/Foto2.PNG)

Una vez actualizado escribimos sudo apt install mariadb-server (Le damos a Y en si queremos continuar.)

![FOTO3](./img/Foto3.PNG)

Cuando se haya instalado, usamos sudo mysql para iniciar el monitor de MariaDB.

![FOTO 4](./img/Foto4.PNG)

Escribir \h (o también \?) nos abre una lista con los comandos que se pueden usar.

![FOTO 5](./img/Foto5.PNG)

Y una vez comprobado que funciona, escribimos exit para salir y MariaDB se despide.

![FOTO 6](./img/Foto6.PNG)



> ----------------------------------------



## Creación de base de datos Ejemplo 1

Abrimos MariaDB y empezamos creando la base de datos sobre la que vamos a trabajar.

```
CREATE DATABASE Investigacion;
```

Usamos el comando USE para seleccionar la base de datos que acabamos de crear y poder crear tablas dentro de esta.

```
USE Investigacion
```

![Foto 1](./img/ejemplo1/Captura1.PNG)
Una vez que tenemos la base de datos seleccionada, podemos empezar a crear las tablas.

La síntaxis de CREATE TABLE es la siguiente:
```
CREATE [OR REPLACE] [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...) [table_options    ]... [partition_options]
```

Para empezar, crearemos la tabla Sede. En mi caso siempre usaré el modo "complejo" de crear las claves primarias para acostumbrarme a ese y que no haya problemas.
Otro detalle importante es que no se puede usar CREATE DOMAIN en MariaDB, por lo tanto tendremos que usar al expresión completa cada vez.

```
CREATE TABLE Sede (
Nome_Sede VARCHAR(30),
Campus    VARCHAR(30)  NOT NULL,
PRIMARY KEY (Nome_Sede)
);
```

![Foto 2](./img/ejemplo1/Captura2.PNG)

Seguimos con Departamento.

```
CREATE TABLE Departamento (
Nome_Departamento VARCHAR(30),
Teléfono          CHAR(9)      NOT NULL,
Director          CHAR(9),
PRIMARY KEY (Nome_Departamento)
);
```

![Foto 3](./img/ejemplo1/Captura3.PNG)



Seguimos con Ubicacion.

```
CREATE TABLE Ubicacion (
Nome_Sede         VARCHAR(30),
Nome_Departamento VARCHAR(30),
PRIMARY KEY (Nome_Sede, Nome_Departamento)
);
```

![Foto 4](./img/ejemplo1/Captura4.PNG)


Seguimos con Grupo.

```
CREATE TABLE Grupo (
Nome_Grupo        VARCHAR(30),
Nome_Departamento VARCHAR(30),
Area              VARCHAR(30) NOT NULL,
Lider             VARCHAR(9),
PRIMARY KEY (Nome_Grupo, Nome_Departamento)
);
```

![Foto 5](./img/ejemplo1/Captura5.PNG)


Seguimos con Profesor.

```
CREATE TABLE Profesor (
DNI            VARCHAR(9),
Nome_Profesor  VARCHAR(30) NOT NULL, 
Titulación     VARCHAR(20) NOT NULL,
Experiencia    INTEGER,
N_Grupo        VARCHAR(30),
N_Departamento VARCHAR(30),
PRIMARY KEY (DNI)
);
```

![Foto 6](./img/ejemplo1/Captura6.PNG)


Seguimos con Proxecto.

```
CREATE TABLE Proxecto (
Codigo_Proxecto VARCHAR(5),
Nome_Proxecto   VARCHAR(30)   NOT NULL,
Orzamento       DECIMAL(13,2) NOT NULL,
Data_Inicio     DATE          NOT NULL,
Data_Fin        DATE,
N_Gr            VARCHAR(30),
N_Dep           VARCHAR(30),
UNIQUE (Nome_Proxecto),
PRIMARY KEY (Codigo_Proxecto)
);
```

![Foto 7](./img/ejemplo1/Captura7.PNG)


Seguimos con Participa

```
CREATE TABLE Participa (
DNI             VARCHAR(9),
Codigo_Proxecto VARCHAR(5),
Data_Inicio     DATE        NOT NULL,
Data_Cese       DATE,
Dedicación      INTEGER     NOT NULL,
PRIMARY KEY (DNI, Codigo_Proxecto)
);
```

![Foto 8](./img/ejemplo1/Captura8.PNG)


Seguimos con Programa.

```
CREATE TABLE Programa (
Nome_Programa VARCHAR(30),
PRIMARY KEY (Nome_Programa)
); 
```

![Foto 9](./img/ejemplo1/Captura9.PNG)

Y terminamos con Financia.

```
CREATE TABLE Financia (
  Nome_Programa        VARCHAR(30),
  Codigo_Proxecto      VARCHAR(9),
  Numero_Programa      VARCHAR(9)     NOT NULL,
  Cantidade_Financiada DECIMAL(13,2)  NOT NULL,
  PRIMARY KEY (Nome_Programa, Código_Proxecto)
);
```

![Foto 10](./img/ejemplo1/Captura10.PNG)

Una vez que hemos terminado creando las tablas, tenemos que crear las claves ajenas alterando las tablas ya creadas.

Sintaxis:

```
ALTER [ONLINE] [IGNORE] TABLE [IF EXISTS] tbl_name
    [WAIT n | NOWAIT]
    alter_specification [, alter_specification] ...
```

Empezamos con la tabla Ubicacion.

```
ALTER TABLE Ubicacion
ADD CONSTRAINT FK_Sede_Ubicacion
FOREIGN KEY (Nome_Sede)
REFERENCES Sede (Nome_Sede)
ON DELETE CASCADE
ON UPDATE CASCADE,
ADD CONSTRAINT FK_Departamento_Ubicacion
FOREIGN KEY (Nome_Departamento)
REFERENCES Departamento (Nome_Departamento)
ON DELETE CASCADE
ON UPDATE CASCADE
;
```

![Foto A](./img/ejemplo1/CapturaA.PNG)

Seguimos con la tabla Grupo.

```
ALTER TABLE Grupo
ADD CONSTRAINT FK_Departamento_Grupo
FOREIGN KEY (Nome_Departamento) 
REFERENCES Departamento (Nome_Departamento)
ON DELETE CASCADE
ON UPDATE CASCADE
;
```

![Foto B](./img/ejemplo1/CapturaB.PNG)

Seguimos con la tabla Profesor.

```
ALTER TABLE Profesor
ADD CONSTRAINT FK_Grupo_Profesor
FOREIGN KEY      (N_Grupo,    N_Departamento)
REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
ON DELETE SET NULL
ON UPDATE CASCADE
;
```

![Foto C](./img/ejemplo1/CapturaC.PNG)

Seguimos con la tabla Proxecto

```
ALTER TABLE Proxecto
ADD CONSTRAINT FK_Grupo_Proxecto
FOREIGN KEY (N_Gr, N_Dep)
REFERENCES Grupo (Nome_Grupo, Nome_Departamento)
ON DELETE SET NULL
ON UPDATE CASCADE
);
```

![Foto D](./img/ejemplo1/CapturaD.PNG)

Serguimos con la tabla Departamento.

```
ALTER TABLE Departamento
ADD CONSTRAINT FK_Profesor_Departamento
FOREIGN KEY (Director)
REFERENCES Profesor (DNI)
ON DELETE SET NULL
ON UPDATE CASCADE
;
```

![Foto E](./img/ejemplo1/CapturaE.PNG)

Seguimos con la tabla Grupo.

```
ALTER TABLE Grupo
ADD CONSTRAINT FK_Profesor_Grupo
FOREIGN KEY (Lider)
REFERENCES Profesor (DNI)
ON DELETE SET NULL
ON UPDATE CASCADE
;
```

![Foto F](./img/ejemplo1/CapturaF.PNG)

Seguimos con la tabla Participa.

```
ALTER TABLE Participa
ADD CONSTRAINT FK_Profesor_Participa
FOREIGN KEY (DNI)
REFERENCES Profesor (DNI)
ON DELETE NO ACTION
ON UPDATE CASCADE,
ADD CONSTRAINT FK_Proxecto_Participa
FOREIGN KEY (Codigo_Proxecto)
REFERENCES Proxecto (Codigo_Proxecto)
ON DELETE NO ACTION
ON UPDATE CASCADE
;
```

![Foto G](./img/ejemplo1/CapturaG.PNG)

Terminamos con la tabla Financia.

```
ALTER TABLE Financia
ADD CONSTRAINT FK_Proxecto_Financia
FOREIGN KEY (Codigo_Proxecto)
REFERENCES Proxecto (Codigo_Proxecto)
ON DELETE CASCADE
ON UPDATE CASCADE,
ADD CONSTRAINT FK_Programa_Financia
FOREIGN KEY (Nome_Programa)
REFERENCES Programa (Nome_Programa)
ON DELETE CASCADE
ON UPDATE CASCADE;
```

![Foto H](./img/ejemplo1/CapturaH.PNG)

Y añadimos un par de CHECKS al final, usando ALTER.

```
ALTER TABLE Proxecto
ADD CONSTRAINT Check_Dates
CHECK (Data_Inicio < Data_Fin),
```

![Check1](./img/ejemplo1/Check1.PNG)

```
ALTER TABLE Participa
ADD CONSTRAINT Check_Fecha
CHECK (Data_Inicio < Data_Cese)
```

![Check2](./img/ejemplo1/Check2.PNG)


> ----------------------------------------

## Creación de base de datos Ejemplo 2


Abrimos MariaDB y otra vez, creamos la base de datos con la que vamos a trabajar.

```
CREATE DATABASE Naves;
```

Y otra vez, usamos el comando USE para seleccionar esa base de datos.

```
USE Naves
```

![Foto 1](./img/ejemplo2/Captura1.PNG)

Y, como en el ejemplo 1, empezamos a crear las tablas. Creamos Servizo.

```
CREATE TABLE Servizo (
Clave_Servizo CHAR(5),
Nome_Servizo VARCHAR(40),
PRIMARY KEY (Clave_Servizo, Nome_Servizo)
);
```

![Foto 2](./img/ejemplo2/Captura2.PNG)

Serguimos con Dependencia.

```
CREATE TABLE Dependencia (
  Codigo_Dependencia CHAR(5),
  Nome_Dependencia   VARCHAR(40) NOT NULL,
  Función            VARCHAR(20),
  Localización       VARCHAR(20),
  Clave_Servizo      CHAR(5) NOT NULL,
  Nome_Servizo       VARCHAR(40) NOT NULL,
  UNIQUE (Nome_Dependencia),
  PRIMARY KEY (Codigo_Dependencia)
);
```

![Foto 3](./img/ejemplo2/Captura3.PNG)

Seguimos con Camara.

```
CREATE TABLE Camara (
  Código_Dependencia CHAR(5),
  Categoría          VARCHAR(40) NOT NULL,
  Capacidade         INTEGER     NOT NULL,
  PRIMARY KEY (Codigo_Dependencia)
);
```

![Foto 4](./img/ejemplo2/Captura4.PNG)

Serguimos con Tripulacion.

```
CREATE TABLE Tripulación (
  Código_Tripulación CHAR(5),
  Nome_Tripulación   VARCHAR(40),
  Categoría          CHAR(20)    NOT NULL,
  Antigüidade        INTEGER     DEFAULT 0,
  Procedencia        CHAR(20),
  Adm                CHAR(20)    NOT NULL,
  Código_Dependencia CHAR(5) NOT NULL,
  Código_Cámara      CHAR(5) NOT NULL,
PRIMARY KEY (Codigo_Tripulacion)
);
```

![Foto 5](./img/ejemplo2/Captura5.PNG)

Seguimos con Planeta.

```
CREATE TABLE Planeta (
  Código_Planeta CHAR(5),
  Nome_Planeta   VARCHAR(40) NOT NULL,
  Galaxia        CHAR(15)    NOT NULL,
  Coordenadas    CHAR(15)    NOT NULL,
  UNIQUE (Coordenadas, Nome_Planeta),
PRIMARY KEY (Codigo_Planeta)
 );
```

![Foto 6](./img/ejemplo2/Captura6.PNG)

Seguimos con Visita.

```
CREATE TABLE Visita (
  Código_Tripulación CHAR(5),
  Código_Planeta     CHAR(5),
  Data_Visita        DATE,
  Tempo              INTEGER      NOT NULL,
  PRIMARY KEY (Código_Tripulación, Código_Planeta, Data_Visita)
);
```

![Foto 7](./img/ejemplo2/Captura7.PNG)

Seguimos con Habita.

```
CREATE TABLE Habita (
  Código_Planeta    CHAR(5),
  Nome_Raza         VARCHAR(40),
  Poboación_Parcial INTEGER     NOT NULL,
  PRIMARY KEY (Código_Planeta, Nome_Raza)
);
```

![Foto 8](./img/ejemplo2/Captura8.PNG)

Y terminamos con Raza.

```
CREATE TABLE Raza (
  Nome_Raza       VARCHAR(40),
  Altura          INTEGER      NOT NULL,
  Anchura         INTEGER      NOT NULL,
  Peso            INTEGER      NOT NULL,
  Poboación_Total INTEGER      NOT NULL,
PRIMARY KEY (Nome_Raza)
);
```

![Foto 9](./img/ejemplo2/Captura9.PNG)

Una vez que hemos creado las tablas tenemos que hacer como el primer ejemplo, empezar a usar ALTER para modificar las tablas y añadir las claves ajenas.

Empezamos por la tabla Dependencia.

```
ALTER TABLE Dependencia
ADD CONSTRAINT FK_Servizo_Dependencia
FOREIGN KEY (Clave_Servizo, Nome_Servizo)
REFERENCES Servizo (Clave_Servizo, Nome_Servizo)
ON DELETE CASCADE
ON UPDATE CASCADE
;
```

![Foto A](./img/ejemplo2/CapturaA.PNG)

Seguimos con la tabla Camara.

```
ALTER TABLE Camara
ADD CONSTRAINT FK_Dependencia_Camara
 FOREIGN KEY (Codigo_Dependencia)
    REFERENCES Dependencia (Codigo_Dependencia)
    ON DELETE CASCADE
    ON UPDATE CASCADE
;
```

![Foto B](./img/ejemplo2/CapturaB.PNG)

Seguimos con la tabla Tripulacion.

```
ALTER TABLE Tripulacion
ADD CONSTRAINT FK_Camara_Tripulacion
 FOREIGN KEY (Codigo_Camara)
    REFERENCES Cámara (Codigo_Dependencia)
    ON UPDATE CASCADE
    ON DELETE CASCADE
;
```

![Foto C](./img/ejemplo2/CapturaC.PNG)

Seguimos con la tabla Tripulacion.

```
ALTER TABLE Tripulacion
ADD CONSTRAINT FK_Dependencia_Tripulacion 
FOREIGN KEY (Codigo_Dependencia)
REFERENCES Dependencia (Codigo_Dependencia)
ON UPDATE CASCADE
ON DELETE CASCADE
;
```

![Foto D](./img/ejemplo2/CapturaD.PNG)

Seguimos con la tabla Visita.

```
ALTER TABLE Visita
ADD CONSTRAINT FK_Tripulacion_Visita
FOREIGN KEY (Codigo_Tripulacion)
    REFERENCES Tripulación (Codigo_Tripulacion)
    ON UPDATE CASCADE
    ON DELETE CASCADE,
ADD CONSTRAINT FK_Planeta_Visita
  FOREIGN KEY (Codigo_Planeta)
    REFERENCES Planeta (Codigo_Planeta)
    ON UPDATE CASCADE
    ON DELETE CASCADE 
);
```

![Foto E](./img/ejemplo2/CapturaE.PNG)

Seguimos con la tabla Habita.

```
ALTER TABLE Habita
ADD CONSTRAINT FK_Planeta_Habita
 FOREIGN KEY (Código_Planeta)
    REFERENCES Planeta (Código_Planeta)
    ON UPDATE CASCADE
    ON DELETE CASCADE
;
```

![Foto F](./img/ejemplo2/CapturaF.PNG)

Y terminamos otra vez con la tabla Habita.

```
ALTER TABLE Habita
  ADD CONSTRAINT FK_Raza
    FOREIGN KEY (Nome_Raza)
      REFERENCES Raza
      ON UPDATE CASCADE
      ON DELETE CASCADE
;
```

Finalmente, añadimos un CHECK con ALTER.

```
ALTER TABLE Camara
ADD CONSTRAINT Capacidade_maior_de_cero
CHECK (capacidade > 0);
```

![Check1](./img/ejemplo2/Check1.PNG)


> ----------------------------------------


## Meta uso MariaDB

### SHOW DATABASES

```
SHOW {DATABASES | SCHEMAS}
    [LIKE 'pattern' | WHERE expr]
```

El comando más básico para usar la consola de comandos para visualizar nuestras bases de datos de forma gráfica es el comando SHOW DATABASES, el cual nos permite ver las bases de datos que tenemos creadas.

![Foto 0](./img/metauso/Captura0.PNG)

### SHOW TABLES

```
SHOW [FULL] TABLES [FROM db_name]
    [LIKE 'pattern' | WHERE expr]
```

Una vez que accedamos a una base de datos, podemos usar SHOW para que nos enseñe las tablas de la base de datos, usando SHOW TABLES.
También podemos usar  SHOW TABLES FROM para ver las tablas de una base de datos sin tener que acceder a ella primero.

![Foto 1](./img/metauso/Captura1.PNG)

### SHOW COLUMNS

```
SHOW [FULL] {COLUMNS | FIELDS} FROM tbl_name [FROM db_name]
    [LIKE 'pattern' | WHERE expr]
```

Y el mismo comando SHOW nos permite ver las columnas de una tabla, con la informacion extra que nos proporciona.

![Foto 2](./img/metauso/Captura2.PNG)

### SHOW CREATE TABLE

```
SHOW CREATE TABLE tbl_name
```

Usando SHOW CREATE TABLE también nos permite ver como se ha creado la tabla. (También existe SHOW CREATE DATABASE).

![Foto 3](./img/metauso/Captura3.PNG)

### WHERE y LIKE

Con SHOW COLUMNS también se puede usar WHERE y LIKE para especificar los datos que queremos sacar.
Por ejemplo, si usamos LIKE 'A%' sacamos solo las columnas que empiezan por A. Esto también se puede usar con SHOW DATABASES y con SHOW TABLES.


Usando LIKE con columnas:

![Foto 4](./img/metauso/Captura4.PNG)

Usando WHERE y LIKE para sacar cada columna que sea de un tipo concreto:

![Foto 5](./img/metauso/Captura5.PNG)

Usando LIKE con tablas:

![Foto 6](./img/metauso/Captura6.PNG)

Usando LIKE con bases de datos:

![Foto 7](./img/metauso/Captura7.PNG)


> ----------------------------------------










