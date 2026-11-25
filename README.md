# UNIDAD-3-BASE-DE-DATOS-EJERCICIO-7
El diagrama muestra un **Modelo Relacional (MR)** orientado a la gestión integral de un sistema hotelero, compuesto por **7 tablas interconectadas** que administran los hoteles, sus habitaciones, los huéspedes, las reservas, las estancias efectivas y los consumos de servicios adicionales.

A continuación, se detalla la estructura de cada tabla y las relaciones representadas:

---

### 1. Tablas y Estructura

* **HOTEL**
* `ID_HOTEL` (PK)
* `NOMBRE`
* `DIRECCION`
* `CIUDAD`
* `ESTRELLAS`


* **HABITACIONES**
* `NUM_HABITACION` (PK)
* `ID_HOTEL` (FK) — Relacionado con `HOTEL`
* `TIPO`
* `PISO`
* `TARIFA`


* **DETALLES_RESERVA** *(Tabla intermedia / asociativa entre Reserva y Habitaciones)*
* `ID_RESERVA` (PK / FK)
* `NUM_HABITACION` (FK)
* `FECHA_INICIO`
* `FECHA_FIN`
* `NOMBRE`
* `ESTADO`


* **RESERVA**
* `ID_RESERVA` (PK)
* `ID_HUESPED` (FK) — Relacionado con `HUESPED`
* `ESTADO`
* `FECHA_INICIO`
* `FECHA_FIN`


* **HUESPED**
* `DNI` (PK)
* `NOMBRE`
* `NACIONALIDAD`
* `CORREO`
* `TELEFONO`


* **ESTACIA** *(Estancia)*
* `ID_ESTACIA` (PK)
* `ID_RESERVA` (FK) — Relacionado con `RESERVA`
* `NUM_HABITACION` (FK) — Relacionado con `HABITACIONES`
* `FECHA_LLEGADA`
* `FEHCA_SALIDA` *(Fecha de salida)*
* `ESTADO`
* `COSTO_TOTAL`


* **CONSUMO_SERVICIO**
* `ID_CONSUMO` (PK)
* `ID_SERVICIO` (FK)
* `ID_ESTACIA` (FK) — Relacionado con `ESTACIA`
* `FECHA`
* `CANTIDAD`
* `COSTO`





---

### 2. Relaciones Principales

* **Hotel y Habitaciones:** Un hotel dispone de múltiples habitaciones registradas en `HABITACIONES` mediante la clave foránea `ID_HOTEL` (relación de uno a muchos).
* **Huésped y Reserva:** Un huésped (identificado por su `DNI`) puede realizar múltiples reservas a lo largo del tiempo (`RESERVA`), vinculadas a través de `ID_HUESPED` (relación de uno a muchos).
* **Reserva y Habitaciones (`DETALLES_RESERVA`):** La tabla asociativa `DETALLES_RESERVA` desglosa qué habitaciones específicas están incluidas en cada reserva, junto con sus fechas y estados particulares.
* **Reserva, Habitación y Estancia (`ESTACIA`):** Registra la estadía real del huésped cuando hace efectivo el check-in, vinculando tanto la reserva origen (`ID_RESERVA`) como la habitación asignada (`NUM_HABITACION`), las fechas reales de ingreso y egreso, y el costo total.
* **Estancia y Consumos (`CONSUMO_SERVICIO`):** Permite asociar múltiples consumos de servicios adicionales (minibar, spa, restaurante, etc.) a una estancia específica mediante `ID_ESTACIA`, detallando la cantidad, fecha y costo de cada consumo.
<img width="1142" height="1032" alt="EJERCICIO 7-MR drawio" src="https://github.com/user-attachments/assets/bfac044b-6f53-41ff-a934-77d1b1482b29" />

El diagrama ilustra el proceso completo de **normalización de bases de datos** aplicado de manera secuencial —desde una tabla inicial sin normalizar hasta alcanzar la Tercera Forma Normal (3NF)— para un sistema de **gestión hotelera** (hoteles, habitaciones, huéspedes, reservas, estancias y consumos de servicios).

A continuación, se detalla lo que se realiza en cada fase representada en el esquema:

---

### 1. Fase Normal N°0 (Tabla Desnormalizada)

* **Estado inicial:** Se parte de una **única tabla grande y plana** donde cada fila representa un hotel y contiene atributos multivaluados y anidados en una misma celda (listas de habitaciones con su tipo y tarifa, reservas con fechas y estados, datos del huésped, y estancias con sus consumos de servicios asociados).
* **Problema:** Presenta grupos repetitivos anidados y una severa falta de atomicidad, lo que impide realizar consultas relacionales estándar y provoca anomalías de inserción, actualización y borrado.

### 2. Primera Forma Normal (1NF)

* **Condición:** Se exige que todos los atributos contengan **valores atómicos** (indivisibles, un solo dato por celda) y que cada tabla disponga de una clave primaria identificativa.
* **Proceso aplicado:** Se eliminan los grupos repetitivos desglosando los registros en filas individuales y separando los consumos:
* **TABLA PLANILLA:** Aplana los datos operativos de los hoteles, habitaciones, reservas, huéspedes y estancias utilizando una clave primaria compuesta (`ID_HOTEL`, `NUM_HABITACION`, `ID_RESERVA`, `ID_ESTANCIA`).
* **TABLA CONSUMO:** Extrae como una tabla independiente los consumos realizados durante cada estancia (`ID_CONSUMO` como PK y `ID_ESTANCIA` como FK), detallando el servicio, la fecha, la cantidad y el costo.



### 3. Segunda Forma Normal (2NF)

* **Condición:** La base de datos debe estar en 1NF y **todos los atributos que no forman parte de la clave deben depender por completo de toda la clave primaria** (eliminando las dependencias parciales generadas por la clave compuesta de la etapa anterior).
* **Proceso aplicado:** Se separan los atributos según la clave exacta de la que dependen:
* **TABLA HOTEL:** Aísla los datos que dependen exclusivamente de `ID_HOTEL` (nombre, dirección, ciudad y estrellas).
* **TABLA HABITACIONES:** Agrupa los atributos propios de cada habitación (`ID_HOTEL` y `NUM_HABITACION` como clave compuesta, tipo, piso y tarifa).
* **TABLA RESERVAS:** Extrae los datos que dependen de `ID_RESERVA` (fechas de inicio y fin, estado y datos del huésped).
* **TABLA ESTANCIA:** Separa la información operativa de la estadía efectiva (`ID_ESTANCIA` como PK, vinculada a la reserva y la habitación, con fechas reales de llegada y salida, estado y costo total).



### 4. Tercera Forma Normal (3NF)

* **Condición:** Debe estar en 2NF y **carecer de dependencias transitivas** (ningún atributo no clave debe depender de otro atributo que tampoco sea clave; todo atributo no clave debe depender estrictamente de la clave primaria).
* **Proceso aplicado:** Se termina de modularizar y aislar por completo las entidades independientes del sistema hotelero, conformando las **7 tablas definitivas**:
* **TABLA HOTEL y TABLA HABITACIONES:** Mantienen de forma independiente la infraestructura hotelera y sus características.
* **TABLA HUESPED:** Separa los datos personales de los clientes (`DNI` como PK, nombre, nacionalidad, correo y teléfono), eliminando la dependencia transitiva que existía dentro de las reservas.
* **TABLA RESERVA y TABLA DETALLES_RESERVA:** Desglosan la cabecera de la reserva (`ID_RESERVA` como PK vinculada al huésped) y el detalle de las habitaciones asignadas a cada reserva (`ID_RESERVA` y `NUM_HABITACION` como PK/FK compuesta).
* **TABLA ESTANCIA y TABLA CONSUMO_SERVICIO:** Registran de manera limpia las estadías efectivas y los consumos de servicios adicionales vinculados a cada estancia.
<img width="5102" height="2772" alt="EJERCICIO 7-FASES DE NORMALIZACION drawio" src="https://github.com/user-attachments/assets/f5b5e8e6-1143-4ae3-905e-f94a9e838f8e" />


