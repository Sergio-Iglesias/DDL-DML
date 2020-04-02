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