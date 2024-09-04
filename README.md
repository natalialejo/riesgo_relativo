## Indice:
1. [Objetivo](#Objetivo)
2. [Contexto](#Contexto)
3. [Metodología](#Metodología)   
    3.1. [Procesamiento y preparación de datos](#Procesamiento-y-preparación-de-datos)   
    3.2. [Análisis exploratorio](#Análisis-exploratorio)   
    3.3. [Análisis técnico](#Análisis-técnico)    
    3.4. [Creación de Dashboard en Looker Studio](#Creación-de-Dashboard-en-Looker-Studio)    
4. [Conclusiones y Recomendaciones](#Conclusiones-y-Recomendaciones)    
   
## Objetivo:
 Identificar y segmentar a los clientes en función de su riesgo relativo, a través de la evaluación detallada de variables clave en la base de datos proporcionada por el banco. Este análisis inicial establecerá una base sólida para futuros desarrollos, como la creación de un score crediticio y la automatización del proceso de evaluación de riesgo, contribuyendo a la solidez financiera y operativa del banco.

## Contexto: 
El banco "Super Caja" ha experimentado un notable aumento en la demanda de crédito, impulsado por clientes que buscan financiar compras importantes o consolidar deudas existentes. Este crecimiento representa una oportunidad significativa para expandir la cartera crediticia del banco, pero también conlleva un aumento en los riesgos crediticios.

Para gestionar estos riesgos de manera efectiva, el banco cuenta con una base de datos de más de 35,000 clientes, que incluye información detallada sobre diversas variables clave, como la edad, el salario, los tipos y cantidades de préstamos. Además, la base de datos contiene una clasificación preexistente denominada "default flag", que indica si un cliente es un buen pagador o si presenta riesgos de incumplimiento.

Esta información es crucial para realizar un análisis riguroso del riesgo relativo, lo que permitirá al banco segmentar a sus clientes de manera más precisa y tomar decisiones informadas sobre la concesión de crédito, minimizando así la exposición a pérdidas financieras.

Además, las respuestas obtenidas nos ayudarán a realizar la validación de las siguientes hipótesis:

- **Hi1: Los clientes más jóvenes tienen un mayor riesgo de impago.**

- **Hi2: Las personas con más cantidad de préstamos activos tienen mayor riesgo de ser malos pagadores.**

- **Hi3: Las personas que han retrasado sus pagos por más de 90 días tienen mayor riesgo de ser malos pagadores.**

## Metodología

### Procesamiento y preparación de datos:

#### 1. Herramientas:
* Google Sheets
* BigQuery
* Looker Studio
* ChatGPT 
* Canva
* Google Slides 
* Loom

#### 2. Lenguajes:
* SQL en BigQuery

#### 3. Descripción de las fuentes de datos: 
Este conjunto de tablas contiene informción sobre préstamos concedidos a un grupo de clientes del banco,los cuales se dividen en:

**User_ info**: datos del usuario/cliente.
- *user_ id*: Número de identificación del cliente (único para cada cliente).
- *age* : Edad del cliente.
- *sex* :	Sexo del cliente.
- *last_ month_ salary*: Último salario mensual que el cliente reportó al banco.
- *number_dependents*: Número de dependientes.

**loans_ outstanding**: datos del tipo de préstamo.
- *loan_ id*: Número de identificación del préstamo (único para cada préstamo).
- *user_ id* : Número de identificación del cliente.
- *loan_ type*: Tipo de préstamo (real estate = inmobiliario, others = otro).

**loans_ detail**:  el comportamiento de pago de estos préstamos, 
- *user_ id*: Número de identificación del cliente.
- *using_ lines_ not_ secured_ personal_ assets* : Cuánto está utilizando el cliente en relación con su límite de crédito, en líneas que no están garantizadas con bienes personales, como inmuebles y automóviles.
- *debt_ ratio*: Relación entre las deudas y el patrimonio del prestatario; ofrece una idea de cuán endeudada está una persona o empresa en comparación con sus activos.
- *number_ times_ delayed_ payment_ loan_ 30_ 59_ days*: Número de veces que el cliente se retrasó en el pago de un préstamo (entre 30 y 59 días).
- *number_ times_ delayed_ payment_ loan_ 60_ 89_ days*: Número de veces que el cliente retrasó el pago de un préstamo (entre 60 y 89 días).
- *more_ 90_ days_ overdue*: Número de veces que el cliente estuvo más de 90 días vencido.

**default**: la identificación de clientes ya identificados como morosos.
- *user_ id*: Número de identificación del cliente
- *default_ flag* : Clasificación de los clientes morosos (1 para clientes que pagan mal, 0 para clientes que pagan bien)

#### 4. Limpieza y transformación de datos:

* Conexión e Importación de Datos:

    Se conectaron y crearon 4 tablas en Google BigQuery utilizando archivos de datos proporcionados.
    Tablas creadas: user_info, loans_outstanding, loans_detail, y default.

* Limpieza y Preparación de Datos:

    Valores nulos:
        Identificación y tratamiento de 7,199 valores nulos en last_month_salary y 943 en number_dependents en la tabla user_info.
        Se utilizó la mediana para imputar valores nulos y se aplicó la técnica de winsorización para manejar outliers.
    Datos duplicados:
        Identificados y revisados, no afectaron significativamente el análisis.
    Datos inconsistentes en variables categóricas:
        Estandarización de categorías en loan_type utilizando SQL (UPPER, LOWER).

* Análisis de Correlación:

    Evaluación de la correlación entre variables utilizando la función CORR en SQL para identificar multicolinealidad.
    Determinación de alta correlación entre variables como more_90_days_overdue y number_times_delayed_payment_loan_30_59_days (r = 0.9829).
    Decisión de excluir variables con alta correlación para evitar multicolinealidad y simplificar el modelo.

* Tratamiento de Outliers:

    Identificación y tratamiento de outliers en variables clave como last_month_salary, age, y number_dependents utilizando percentiles (P2 y P99).
    Aplicación de winsorización para reemplazar outliers con valores límite.

* Creación de Nuevas Variables:

    Construcción de nuevas variables como total_loans, real_state_loans, y other_loans para mejorar el análisis.
    Evaluación de la correlación entre las nuevas variables y el resto de los datos.

* Integración de Datos:

    Unión de tablas limpias (user_default_view, loutstanding_view, ldetail_view) mediante comandos SQL LEFT JOIN e INNER JOIN para consolidar la base de datos.
    Construcción de una tabla final con 35,574 registros, lista para el análisis de riesgo relativo.

* Construcción de Tablas Auxiliares:

    Creación de tablas auxiliares utilizando subconsultas y comandos WITH para analizar valores nulos y comprender mejor los datos.






