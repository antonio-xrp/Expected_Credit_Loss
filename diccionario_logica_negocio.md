# Comprensión de la lógica del negocio

Antes de comenzar con el análisis estadístico y el modelado, es importante entender **cómo funciona el negocio crediticio y qué representa cada grupo de variables dentro del ciclo de vida de un préstamo**.

Desde una perspectiva bancaria, podemos imaginar el préstamo como un proceso que comienza con una solicitud, continúa con la evaluación y desembolso del crédito y, posteriormente, evoluciona de acuerdo con el comportamiento de pago del cliente.

Por ello, podemos organizar las variables en cuatro grandes bloques:

1. Características del préstamo.
2. Perfil del solicitante.
3. Historial o buró de crédito.
4. Comportamiento de pago.


## 1. Características del préstamo

El primer elemento que debemos imaginar es el **producto crediticio que el banco está otorgando**.

Cuando una persona solicita un préstamo, existen determinadas características que definen las condiciones económicas del crédito: cuánto dinero se presta, cuál será la tasa de interés, cuánto tiempo tendrá el cliente para devolverlo y cuál será la cuota correspondiente.

Estas variables describen principalmente **las condiciones contractuales y financieras del préstamo**.

En nuestra base de datos encontramos, entre otras:

* `funded_amnt`: monto financiado del préstamo.
* `interest_rate`: tasa de interés aplicada al préstamo.
* `monthly_payment`: cuota mensual programada.
* `loan_term_months`: plazo del préstamo en meses.
* `loan_purpose`: finalidad declarada del préstamo.
* `disbursement_method`: modalidad mediante la cual se desembolsa el crédito.
* `grade`: clasificación crediticia asignada al préstamo.

### ¿Por qué son importantes?

El monto, la tasa, el plazo y la cuota determinan en gran medida **la obligación financiera que asume el cliente**.

Por ejemplo, dos personas pueden tener ingresos similares, pero si una solicita S/10,000 a corto plazo y otra solicita S/40,000 a largo plazo, la estructura de sus obligaciones será diferente.

Desde el punto de vista de riesgo, estas características ayudan a responder preguntas como:

- ¿Qué tan grande es la obligación que está asumiendo el cliente?

- ¿Qué nivel de cuota tendrá que afrontar?

- ¿Durante cuánto tiempo estará expuesto el banco al riesgo?

# 2. Perfil del solicitante

El segundo bloque representa **quién es el cliente que está solicitando el préstamo y cuál es su situación económica y laboral**.

Aquí buscamos entender la capacidad económica y el contexto general del solicitante.

Las principales variables son:

* `emp_title`: cargo o puesto laboral declarado.
* `emp_length`: antigüedad laboral.
* `home_ownership_status`: situación de vivienda.
* `annual_income`: ingreso anual declarado.
* `verification_status`: nivel de verificación de los ingresos.
* `addr_state`: estado donde reside el solicitante.
* `region_code`: región geográfica.

### ¿Qué intenta conocer el banco?

Cuando un cliente solicita un crédito, una de las preguntas fundamentales es:

> **¿Quién es el cliente y cuál es su capacidad económica para afrontar la deuda?**

Por ejemplo, el ingreso anual puede proporcionar información sobre la capacidad de generación de recursos del solicitante, mientras que la antigüedad laboral puede aportar información sobre su estabilidad laboral.

La situación de vivienda también puede aportar contexto sobre el perfil financiero del cliente, aunque su interpretación debe realizarse de manera descriptiva y considerando posibles diferencias entre segmentos.

Por otro lado, `verification_status` es especialmente interesante porque permite distinguir entre diferentes niveles de validación de los ingresos declarados.

# 3. Historial o buró de crédito

El tercer bloque es probablemente uno de los más importantes para evaluar el riesgo crediticio.

Aquí dejamos de mirar únicamente quién es el cliente y comenzamos a observar **cómo se ha comportado financieramente antes de solicitar este préstamo**.

El buró de crédito puede entenderse como una especie de historial financiero del solicitante.

En lugar de preguntarnos solamente: ¿Cuánto gana?

también podemos preguntarnos:

- ¿Cómo ha manejado sus obligaciones financieras anteriores?

- ¿Ha presentado morosidades?

- ¿Cuántas cuentas de crédito tiene?

- ¿Cuánto crédito utiliza?

- ¿Cuántas consultas recientes ha realizado?

Entre las variables encontramos:

* `num_30+_delinq_in_2yrs`: número de morosidades de 30 días o más durante los últimos dos años.
* `num_open_credit_lines`: número de líneas de crédito abiertas.
* `total_credit_revolving_bal`: saldo total de crédito revolving.
* `used_credit_share`: proporción de utilización del crédito revolving.
* `mths_since_last_delinq`: meses desde la última morosidad.
* `num_inq`: número de consultas de crédito.
* `num_inq_in_6mths`: consultas realizadas durante los últimos seis meses.
* `tot_num_credit_lines`: número total de líneas de crédito.
* `earliest_cr_line_month` y `earliest_cr_line_year`: antigüedad del historial crediticio.

### ¿Qué intenta responder el banco?

Este bloque busca construir una imagen del **comportamiento financiero histórico del solicitante**.

Por ejemplo:

Un cliente con varias líneas de crédito abiertas no necesariamente representa mayor riesgo.

Pero si además presenta:

* múltiples morosidades,
* elevada utilización del crédito,
* numerosas consultas recientes,
* y un historial crediticio relativamente corto,

el perfil puede requerir un análisis diferente.

Por eso, estas variables no deberían analizarse de manera aislada. Lo importante es estudiar cómo se relacionan con la probabilidad de default.

# 4. Comportamiento de pago

Finalmente llegamos a una categoría completamente diferente.

Hasta este punto hemos hablado principalmente de información que podemos conocer **antes o al momento de otorgar el préstamo**.

Ahora observamos lo que ocurre **después de que el crédito fue desembolsado**.

Este bloque representa el comportamiento real del préstamo y los movimientos económicos que ocurrieron durante su vida.

Las principales variables son:

* `remaining_princ_for_tot_amnt_fund`: principal que permanece pendiente.
* `paym_rec_for_tot_amnt_fund`: pagos recibidos respecto al monto financiado.
* `princ_rec`: principal recuperado.
* `interest_rec`: intereses recuperados.
* `late_fees_rec`: penalizaciones o cargos por mora recuperados.

### ¿Qué nos permite conocer este bloque?

Estas variables permiten observar qué ocurrió realmente con el préstamo:

- ¿Cuánto pagó el cliente?

- ¿Cuánto principal recuperó el banco?

- ¿Cuánto permanece pendiente?

- ¿Cuánto se recuperó después de un evento de incumplimiento?


# **Resumen Lectura de negocio**

⚠️ Data leakage: las variables de desempeño (y el `grade` de Lending Club) NO pueden usarse como input de nuestro modelo de PD, porque no existían en el momento de conceder el préstamo. Usarlas sería hacer trampa. Sí son la base para modelizar LGD y EAD.

| Grupo                                     | Columnas (ejemplos)                                                                                                     | Momento en que se conoce          |
|---                                        |---                                                                                                                      |---                                |
| Características del préstamo              | `funded_amnt`, `interest_rate`, `monthly_payment`, `loan_term_months`, `grade`, `loan_purpose`, `disbursement_method`   | Originación                       |
| Perfil del solicitante                    | `emp_title`, `emp_length`, `home_ownership_status`, `annual_income`, `verification_status`, `addr_state`, `region_code` | Originación                       |
| Buró de crédito / historial               | `num_30+_delinq_in_2yrs`, `num_open_credit_lines`, `total_credit_revolving_bal`, `used_credit_share`, `mths_since_last_delinq`, `num_inq*`, `tot_num_credit_lines`, `earliest_cr_line_*`, etc.                                                                                                                                            | Originación (consulta al buró)    |
| **Comportamiento de pago / recuperación** | `remaining_princ_for_tot_amnt_fund`, `paym_rec_for_tot_amnt_fund`, `princ_rec`, `interest_rec`, `late_fees_rec`         | **Posterior a la originación** ⚠️ |
| Fechas                                    | `issue_date_month/year`, `earliest_cr_line_month/year`                                                                  | Originación                       |
| Target                                    | `y`                                                                                                                     | Resultado final del préstamo      |


# Diccionario de variables

| Variables                            | Descripción                                                  | Tipo       | Disponibilidad | Uso ECL          |
| ------------------------------------ | ------------------------------------------------------------ | ---------- | -------------- | ---------------- |
| `funded_amnt`                        | Monto financiado del préstamo                                | Numérica   | Originación    | PD / EAD         |
| `interest_rate`                      | Tasa de interés del préstamo                                 | Numérica   | Originación    | PD               |
| `monthly_payment`                    | Cuota mensual del préstamo                                   | Numérica   | Originación    | PD / EAD         |
| `grade`                              | Subclasificación crediticia                                  | Categórica | Originación    | ⚠️ No usar en PD |
| `emp_title`                          | Cargo o puesto laboral del solicitante                       | Categórica | Originación    | PD               |
| `emp_length`                         | Antigüedad laboral                                           | Categórica | Originación    | PD               |
| `home_ownership_status`              | Situación de propiedad de vivienda                           | Categórica | Originación    | PD               |
| `annual_income`                      | Ingreso anual declarado                                      | Numérica   | Originación    | PD               |
| `verification_status`                | Estado de verificación de ingresos                           | Categórica | Originación    | PD               |
| `loan_purpose`                       | Finalidad declarada del préstamo                             | Categórica | Originación    | PD               |
| `addr_state`                         | Estado o región de residencia                                | Categórica | Originación    | PD               |
| `dept_paym_income_ratio`             | Relación deuda-ingreso                                       | Numérica   | Originación    | PD               |
| `num_30+_delinq_in_2yrs`             | Número de morosidades de 30+ días en los últimos 2 años      | Numérica   | Originación    | PD               |
| `num_inq_in_6mths`                   | Consultas crediticias en los últimos 6 meses                 | Numérica   | Originación    | PD               |
| `mths_since_last_delinq`             | Meses desde la última morosidad                              | Numérica   | Originación    | PD               |
| `num_open_credit_lines`              | Número de líneas de crédito abiertas                         | Numérica   | Originación    | PD               |
| `num_derogatory_pub_rec`             | Registros públicos negativos                                 | Numérica   | Originación    | PD               |
| `total_credit_revolving_bal`         | Saldo total de crédito revolving                             | Numérica   | Originación    | PD               |
| `used_credit_share`                  | Porcentaje utilizado del crédito revolving                   | Numérica   | Originación    | PD               |
| `tot_num_credit_lines`               | Número total de cuentas o líneas de crédito                  | Numérica   | Originación    | PD               |
| `initial_list_status`                | Estado inicial de listado del préstamo                       | Categórica | Originación    | PD               |
| `remaining_princ_for_tot_amnt_fund`  | Principal pendiente de pago                                  | Numérica   | Performance    | LGD / EAD        |
| `paym_rec_for_tot_amnt_fund`         | Total de pagos recibidos                                     | Numérica   | Performance    | LGD              |
| `princ_rec`                          | Principal recuperado                                         | Numérica   | Performance    | LGD              |
| `interest_rec`                       | Intereses recuperados                                        | Numérica   | Performance    | LGD              |
| `late_fees_rec`                      | Penalizaciones por pagos tardíos recuperadas                 | Numérica   | Performance    | LGD              |
| `num_open_trades_in_6mths`           | Nuevas cuentas abiertas en los últimos 6 meses               | Numérica   | Originación    | PD               |
| `num_installment_acc_op_in_12mths`   | Cuentas de crédito a plazos abiertas en los últimos 12 meses | Numérica   | Originación    | PD               |
| `num_installment_acc_op_in_24mths`   | Cuentas de crédito a plazos abiertas en los últimos 24 meses | Numérica   | Originación    | PD               |
| `mths_since_last_installment_acc_op` | Meses desde la apertura de la última cuenta a plazos         | Numérica   | Originación    | PD               |
| `num_rev_trades_op_in_12mths`        | Créditos revolving abiertos en los últimos 12 meses          | Numérica   | Originación    | PD               |
| `num_rev_trades_op_in_24mths`        | Créditos revolving abiertos en los últimos 24 meses          | Numérica   | Originación    | PD               |
| `max_bal_owed`                       | Mayor saldo registrado en crédito revolving                  | Numérica   | Originación    | PD               |
| `bal_to_cred_lim`                    | Relación entre saldo y límite de crédito                     | Numérica   | Originación    | PD               |
| `num_inq`                            | Número de consultas financieras                              | Numérica   | Originación    | PD               |
| `num_inq_in_12mths`                  | Consultas crediticias en los últimos 12 meses                | Numérica   | Originación    | PD               |
| `mths_since_recent_bankcard_delinq`  | Meses desde la última morosidad de tarjeta bancaria          | Numérica   | Originación    | PD               |
| `mths_since_recent_revol_delinq`     | Meses desde la última morosidad revolving                    | Numérica   | Originación    | PD               |
| `disbursement_method`                | Método de desembolso                                         | Categórica | Originación    | PD               |
| `loan_term_months`                   | Plazo del préstamo en meses                                  | Numérica   | Originación    | PD / EAD         |
| `issue_date_month`                   | Mes de emisión del préstamo                                  | Numérica   | Originación    | PD               |
| `issue_date_year`                    | Año de emisión del préstamo                                  | Numérica   | Originación    | PD               |
| `region_code`                        | Código geográfico o regional                                 | Categórica | Originación    | PD               |
| `earliest_cr_line_month`             | Mes de apertura de la primera línea de crédito               | Numérica   | Originación    | PD               |
| `earliest_cr_line_year`              | Año de apertura de la primera línea de crédito               | Numérica   | Originación    | PD               |
