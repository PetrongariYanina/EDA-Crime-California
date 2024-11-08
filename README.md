# BASE DE DATOS SOBRE CRÍMENES EN CALIFORNIA A PARTIR DE 2020

## Información sobre el Dataset

### Descripción de las Columnas

- **DR_NO** (Division Report Number): Número único de reporte policial asignado por LAPD
- **Date Rptd**: Fecha cuando el crimen fue reportado a la policía
- **DATE OCC**: Fecha cuando ocurrió el crimen
- **TIME OCC**: Hora cuando ocurrió el crimen (formato militar 24hrs)
- **AREA**: Número del área/división policial (1-21)
- **AREA NAME**: Nombre del área/división policial
- **Rpt Dist No** (Reporting District Number): Número del distrito de reporte, áreas más pequeñas dentro de cada división
- **Part 1-2**: Indica si es un crimen Parte 1 (más graves) o Parte 2 (menos graves)
- **Crm Cd** (Crime Code): Código numérico del tipo de crimen
- **Crm Cd Desc** (Crime Code Description): Descripción del tipo de crimen
- **Mocodes** (Modus Operandi Codes): Códigos que describen el método del crimen
- **Vict Age**: Edad de la víctima
- **Vict Sex**: Sexo de la víctima (F=Femenino, M=Masculino, X=Desconocido)
- **Vict Descent**: Origen étnico de la víctima utilizando códigos de una letra:
  - A: Asiático
  - B: Negro
  - H: Hispano
  - W: Blanco
  - O: Otro
  - X: Desconocido
- **Premis Cd** (Premise Code): Código numérico del tipo de lugar
- **Premis Desc** (Premise Description): Descripción del tipo de lugar donde ocurrió el crimen
- **Weapon Used Cd** (Weapon Used Code): Código del arma utilizada
- **Weapon Desc** (Weapon Description): Descripción del arma utilizada
- **Status**: Estado actual del caso
- **Status Desc** (Status Description): Descripción del estado del caso
- **Crm Cd 1** (Crime Code 1): Código de crimen adicional si aplica
- **Crm Cd 2** (Crime Code 2): Segundo código de crimen adicional si aplica
- **Crm Cd 3** (Crime Code 3): Tercer código de crimen adicional si aplica
- **Crm Cd 4** (Crime Code 4): Cuarto código de crimen adicional si aplica
- **LOCATION**: Dirección donde ocurrió el crimen (normalmente se muestra el bloque por privacidad)
- **Cross Street**: Calle que intersecta con la ubicación
- **LAT**: Latitud de la ubicación
- **LON**: Longitud de la ubicación

### Algunos Códigos Importantes Adicionales

**Estado del caso (Status):**
- IC: Investigación Continua
- AA: Arresto Adulto
- JA: Arresto Juvenil
- AO: Arresto Otro
- CLO: Cerrado/Resuelto

**Tipos de Crímenes Comunes (Crm Cd):**
- 110: Homicidio
- 121: Robo
- 122: Robo de Vehículo
- 210: Robo con Violencia
- 230: Asalto Agravado
- 624: Intento de Robo

# EDA-Crime-California

## Cosas por Mirar

1. Mirar los **CRM CD**, sobre todo el 3 y 4, porque tienen muchos valores nulos, casi la totalidad de las filas del CSV, por si vale la pena eliminarlos. ✅
2. Tomar una decisión con respecto al campo **VICT SEX**, pues faltan más de 115,000 valores, y **VICT DESCT**, que también faltan una cantidad similar.✅
3. Sobre **Premis CD** y **Premis DESC**, ver si es valorable eliminar esas filas, pues de **Premis CD** faltan 14 y de **Premis Desc** faltan 585. Su significado es:
   - **Premis Cd**: Código numérico del tipo de lugar ✅
   - **Premis Desc**: Descripción del tipo de lugar donde ocurrió el crimen ✅
4. Cambiar el tipo de las columnas **Vict Sex**, **Vict Descent** y **Status** a "category".  ✅

## Limpieza de Datos

1. Eliminamos 1 fila con dato nulo en el atributo **Status**.
2. Se cambia el tipo de datos de **Status**, **Vict Sex** y **Vict Descent** de tipo Object a tipo Category.
3. Pasamos las columnas de fechas a formato datetime.
4. Pasamos que columna de hora que teniamos en formato int64 a object
5. Eliminamos columnas de - **Crm Cd 2** (Crime Code 2), **Crm Cd 3** (Crime Code 3), **Crm Cd 4** (Crime Code 4) por tener entre el 99% y 93% de valores nulos y no ser relevante para nuestro analisis.
6. Tomamos la decisión de conservar datos nulos sobre campo **VICT SEX**, pues faltan más de 115,000 valores, y **VICT DESCT**, que también faltan una cantidad similar, ya que estos valores corresponden con casos donde la victima no es una persona, por ejemplo, caso de robo de vehiculo, malversación de fondos, etc.
7. Al analizar los valores nulos vemos que se podrìa inferir el Premis Desc por medio del Premis Cd y quedariamos solamente con 14 valores nulos pero al ver que no es relevante para nuestro analisis, por el momento quedará en nulos.
8. Unificamos datos *Vict Sex** en M, F y X, seguimos conservando datos nulos.
9. Trabajamos edades en negativo, reemplazando por la mediana en caso de que *Vict Sex** sea F o M. Y en el caso de valores donde *Vict Sex** es X reemplazamos valor negativo por 0, teniendo en cuenta que al sacar la media vamos a excluir el valor 0.
10. Hemos podido observar que las columnas de Crm Cd y Crm Cd 1 estan duplicadas pero Crm Cd 1 tiene 11 valores nulos, hemos reemplazado esos valores con los de Crm Cd ya que estan completos y eliminado Crm Cd.
11. Hemos podido observar que los valores de edad 0 coinciden en su mayoria con Crm Cd 1 donde no hay una victima sino que se ve una clara inclinacion a robo de vehiculo, hurtos y malverzacion y aunque vemos abusos infantiles, son minoria por eso decidimos omitir 0 en el recuento de las edades.
12. Hemos unido el atributo location y cross street.
13. Utilizamos la nueva columna Category, que agrupa los tipos de crímenes de CRM Cd Desc, para analizar patrones de criminalidad amplios, destacando tendencias en grupos de delitos.

## Visualizacion

1. Creamos la primera visualizacion con la libreria plotly, queremos mostrar con un histograma la cantidad de victimas en funcion de su Sexo (M, F, X) y su Raza (O, H, B, A, W, X).
2. Hicimos un pie chart para saber la distribución de las víctimas en función de su raza y sexo.
3. Realizamos un gráfico de barras en función del número de víctimas por la raza y la edad.
4. Mostramos mediante un gráfico de barras el número de víctimas por rango de edad y su sexo.
5. Gráfico de barras de número de crímenes por área
6. Pie chart con el porcentaje de crímenes por área
7. Geo mapa de los distintos distritos policiales con burbujas que indican el número de crímenes
8. Gráfico de barras con el Top 10 de los crímenes
9. Gráfico de barras relacionando los 5 distritos con más crímenes con los 10 delitos más repetidos
10. Gráfico de barras relacionando los 5 distritos con más crímenes con las 10 localizaciones más repetidas para los crímenes.
11. Creamos Line Chart donde enseñamos la tendencia temporar de ocurrencia de delitos en 2020, 2021, 2022, 2023, 2024.
12. Bar Chart con Plotly que muestra los delitos en funcion de la hora del dia que son cometidos dividido en periodos de 4 horas.
13. Gráfico de barras donde clasificamos los top 5 crímenes en función del intervalo de tiempo.
14. Top 5 de crímenes en el lugar donde ocurrieron los hechos en ese intervalo de tiempo.

