## Indice:
1. [Objetivo](#Objetivo)
2. [Contexto](#Contexto)
3. [Metodología](#Metodología)   
    3.1.[Procesamiento y preparación de datos](#Procesamiento-y-preparación-de-datos)   
    3.2.[Análisis exploratorio](#Análisis-exploratorio)   
    3.3.[Análisis técnico](#Análisis-técnico)    
    3.4.[Creación de Dashboard en Looker Studio](#Creación-de-Dashboard-en-Looker-Studio)    
4. [Conclusiones y Recomendaciones](#Conclusiones-y-Recomendaciones)    

   
## Objetivo:
 Identificar y crear segmentos de clientes basados en su riesgo relativo, mediante la evaluación de variables clave en la base de datos proporcionada por el banco. Este análisis inicial tiene como finalidad establecer una base sólida para futuros desarrollos, incluyendo la creación de un score crediticio y la automatización del proceso de evaluación del riesgo.

## Contexto: 
El banco Super Caja ha visto un aumento en la demanda de crédito, impulsado por clientes que buscan financiar compras o consolidar deudas. Este crecimiento presenta oportunidades, pero también incrementa los riesgos crediticios. El banco está enfocando sus esfuerzos en automatizar y mejorar la eficiencia en la identificación y mitigación de estos riesgos, asegurando un equilibrio entre crecimiento y seguridad financiera.

Las respuestas obtenidas nos ayudarán a realizar la validación de las siguientes hipótesis:

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

#### 3. Descripción de las variables del dataset:
Este conjunto contiene datos sobre préstamos concedidos a un grupo de clientes del banco, los datos se dividen en 4 tablas:

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
- *user_ id*: Número de identificación del cliente
- *using_ lines_ not_ secured_ personal_ assets* : Cuánto está utilizando el cliente en relación con su límite de crédito, en líneas que no están garantizadas con bienes personales, como inmuebles y automóviles
- *debt_ ratio*: Relación entre las deudas y el patrimonio del prestatario. Ratio de deuda = Deudas / Patrimonio (ofrece una idea de cuán endeudada está una persona o empresa en comparación con sus activos)
- *number_ times_ delayed_ payment_ loan_ 30_ 59_ days*: Número de veces que el cliente se retrasó en el pago de un préstamo (entre 30 y 59 días)
- *number_ times_ delayed_ payment_ loan_ 60_ 89_ days*: Número de veces que el cliente retrasó el pago de un préstamo (entre 60 y 89 días)
- *more_ 90_ days_ overdue*: Número de veces que el cliente estuvo más de 90 días vencido

**default**: la identificación de clientes ya identificados como morosos.
- *user_ id*: Número de identificación del cliente
- *default_ flag* : Clasificación de los clientes morosos (1 para clientes que pagan mal, 0 para clientes que pagan bien)

####





