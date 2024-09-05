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

Para gestionar estos riesgos de manera efectiva, el banco cuenta con una base de datos de 36,000 clientes, que incluye información detallada sobre diversas variables clave, como la edad, el salario, los tipos y cantidades de préstamos. Además, la base de datos contiene una clasificación preexistente denominada "default flag", que indica si un cliente es un buen pagador o si presenta riesgos de incumplimiento.

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

- Conexión e Importación de Datos:  
    * Creación de proyecto *riesgorelativop3* y conexiónd de las 4 tablas en Google BigQuery.

- Identificación de valores nulos, duplicados, fuera de rango o con caracteres extraños: 
    * Identificados y tratados 7,199 valores nulos en  variable *last_month_salary* y 943 en *number_dependents* en la tabla user_info.  
    * Datos duplicados no afectan significativamente el análisis.
    * Se identificaron valores inconsistentes en la variable categórica *loan_type*(mayúsculas y minúsculas mezcladas) de la tabla loans_outstanding.Se corrigieron estos valores utilizando `LOWER` y `CASE` para estandarizar las categorías (real estate y other).
    * Se cambiaron los formatos de *user_id* de `INTEGER` a `STRING` para evitar problemas en el proceso de unión de tablas.
    * Se utilizó la función `CORR` para evaluar la correlación entre variables como:
        1. En *more_90_days_overdue* y *number_times_delayed_payment_loan_30_59_days*,se identificó una correlación alta (r = 0.9829), por lo que se comparó la desviación estándar de cada una y se eligió tomar en cuenta para el análisis la variable con mayor desviación estándar (*more_90_days_overdue*) para representar mejor la información.
        2. Variables Independientes: Se comprobó que *debt_ratio* y *more_90_days_overdue* tenían una baja correlación, por lo que ambas se mantuvieron en el análisis, ya que proporcionaban información única.
        3. Decisión sobre variables: Solo se excluyó la variable *gender*, esto debido a que no es un factor determinante del riesgo crediticio, y su uso puede generar sesgos discriminatorios.
    * Identificación y Tratamiento de Outliers: se utilizaron gráficos como histogramas y diagramas de caja (boxplots) en Looker Studio para identificar outliers en variables clave como *last_month_salary* y *age*.
    * Seguido, se aplicó la técnica estadisticas de winsorización utilizando los percentiles P2 y P99 para reducir el impacto de los outliers sin eliminarlos.Se imputaron los valores extremos con los valores en estos percentiles, manteniendo así la representatividad de los datos cercanos a los extremos.    
        Pasos realizados:    
        1. Calcular percentiles P2 y P99 para *last_month_salary*.   
        2. Limitar el rango de edad a un máximo de 85 años para categorizar mejor a los clientes mayores, se crea la varriable *age_limited*.  
        3. Imputar valores nulos en *last_month_salary* y *number_dependents* usando la mediana.

``` slq
WITH percentiles AS (
  SELECT
    PERCENTILE_CONT(last_month_salary, 0.02) OVER() AS S_P2,
    PERCENTILE_CONT(last_month_salary, 0.99) OVER() AS S_P99
  FROM
    `riesgorelativop3.projectRR3.user_default_view`
  WHERE
    last_month_salary IS NOT NULL
),
winsorized_data AS (
  SELECT
    t1.user_id,
    CASE
      WHEN t1.last_month_salary < t2.S_P2 THEN t2.S_P2
      WHEN t1.last_month_salary > t2.S_P99 THEN t2.S_P99
      ELSE t1.last_month_salary
    END AS lms_winsorized,
    t1.age,
    t1.number_dependents
  FROM
    `riesgorelativop3.projectRR3.user_default_view` AS t1,
    (SELECT DISTINCT S_P2, S_P99 FROM percentiles) AS t2
)
SELECT
  user_id,
  CASE 
    WHEN age > 85 THEN 85 
    ELSE age 
  END AS age_clean,
  IFNULL(lms_winsorized, 5400) AS lms_clean,
  IFNULL(number_dependents, 0) AS number_dependents_clean
FROM
  winsorized_data;

```
       
    * En la variable *using_lines_not_secured_personal_assets* de la tabla *loans_detail*, se identificaron valores en notación científica (ej. 7.25e-05). Estos valores son correctos y no se alteraron, ya que representan el uso bajo de líneas de crédito.

- Creación de Nuevas Variables:  
    * Se generaron nuevas variables para agrupar los préstamos por cliente (user_id), incluyendo *total_loans*, *real_state_loans*, y *other_loans*.

- Evaluación de Colinealidad en Nuevas Variables:    
    * *total_loans* incluye *other_loans* como parte significativa, lo que generó alta correlación entre ambas variables, por lo que se optó por trabajar solo con *total_loans* para evitar redundancias y mejorar la eficiencia del modelo.

- Unión de Tablas:
    * Se unieron las tres tablas limpias (*user_default_view*, *loutstanding_view*, *ldetail_view*) mediante un`INNER JOIN`, dicha union resultó en la exclusión de aproximadamente 425 registros que presentaban valores inconsistentes.
    * El proceso de unión resultó en una tabla consolidada con un total de 35,574 registros.    

#### 5. Análisis Exploratorio:   










