# QA Engineer Portafolio

Hola, soy Jorge Páez.

j.paez@outlook.com

# Sobre Mí
Tecnólogo en Informática y certificado QA Engineer Junior, apasionado por la calidad del software y la mejora continua. Tengo conocimientos en pruebas funcionales, diseño de casos de prueba, validación de APIs REST con Postman, SQL y herramientas como Jira y DevTools. Mi experiencia en soporte técnico me permitió desarrollar habilidades de análisis, resolución de problemas y documentación de incidencias. Actualmente estoy construyendo mi carrera en QA y buscando una oportunidad para contribuir al desarrollo de productos de alta calidad.


## Habilidades

<p>
  <a href="#proyecto-uno"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/microsoftsqlserver/microsoftsqlserver-plain.svg" width="48" height="48" alt="SQL Server" /></a>
  <a href="#proyecto-uno"><img width="48" height="48" alt="image" src="https://github.com/user-attachments/assets/86854460-13ff-45e4-b389-4dbfc416277a" /></a>
  <a href="#proyecto-dos"><img src="https://skillicons.dev/icons?i=postman,js,html,css,cs&perline=5" alt="Herramientas" /></a>
  <a href="#proyecto-dos"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/jira/jira-original.svg" width="48" height="48" alt="Jira" /></a>
  <a href="#proyecto-tres"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/chrome/chrome-original.svg" width="48" height="48" alt="DevTools" /></a>
  <a href="#proyecto-tres"><img src="https://github.com/user-attachments/assets/a7d6e786-8fa1-4549-b730-a0665d60a6ab" width="48" height=48 alt="Excel"/></a>
  <a href="#proyecto-tres"><img width="48" height="48" alt="image" src="https://github.com/user-attachments/assets/928012b0-5439-4274-bb0f-c8ca3180373f" /></a>
  <a href="#proyecto-tres"><img width="48" height="48" alt="image" src="https://github.com/user-attachments/assets/3fea424a-d7a6-433b-b35f-d851ae47de6b" /></a>

  

  <p>Manual Testing</p>
  <p>API Testing</p>
</p>

## Proyectos

<details>
<summary><b>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/microsoftsqlserver/microsoftsqlserver-plain.svg" width="25" height="25" alt="SQL Server" />
  <img width="25" height="25" alt="image" src="https://github.com/user-attachments/assets/86854460-13ff-45e4-b389-4dbfc416277a" />
  <h6 id="proyecto-uno">Análisis de logs y base de datos de una app de taxis</h6>
</b></summary>
<br>
# 

Proyecto del **Sprint 7** de mi formación en pruebas de software (QA). Trabajé sobre una aplicación de taxis en dos frentes: **logs de servidor desde la consola** y **consultas SQL** sobre una base de datos de viajes en taxi de Chicago.

**Autor:** Jorge Alberto Paez Vargas · QA Engineer
**Estado:** entregado y aprobado

---

## Qué demuestra este proyecto

- Investigar un incidente leyendo logs de un servidor remoto.
- Filtrar y ordenar información con comandos de consola.
- Consultar una base de datos relacional con SQL para responder preguntas de negocio.
- Documentar los comandos usados, las consultas y los resultados de forma que otra persona pueda reproducirlos.

## Herramientas

| Área | Herramienta |
|---|---|
| Consola | Terminal, SSH, `grep`, redirección de salida, `mkdir` |
| Logs | Registros de servidor Apache (`logs/2019/12`) |
| Base de datos | PostgreSQL |
| SQL | `COUNT`, `DISTINCT`, `GROUP BY`, `HAVING`, `CASE`, `JOIN`, `ORDER BY` |
| Documentación | Google Docs |

---

## Parte 1 · Consola y logs

Los logs estaban en un servidor remoto, en la carpeta `logs/2019/12`, y no se sabía en qué día ocurrió el error.

### Ejercicio 1 · Buscar solicitudes por dirección IP

**Objetivo:** encontrar las solicitudes que llegaron desde direcciones IP con el patrón `233.201`, revisando todos los archivos del mes.

**Comando usado:**

```bash
grep -r ".233.201" logs/2019/12
```

`-r` hace que `grep` busque dentro de todos los archivos de la carpeta, así no hace falta saber de antemano qué día falló. Cada resultado muestra el archivo y la línea del log, por ejemplo:

```
137.189.233.201 - - [11/12/2019:21:55:33 +0000] "PATCH /users HTTP/1.1" 401 3763
```

### Ejercicio 2 · Separar los logs del error por tipo

**Objetivo:** el sistema falló el 30/12/2019 con errores **400** y **500**. Había que guardar esos logs en archivos independientes.

**Pasos que seguí:**

1. Crear en el directorio de inicio del servidor la carpeta `bug1` y, dentro, la carpeta `events`.
2. Guardar en `bug1/main.txt` todas las solicitudes del periodo del error.
3. Separar de `main.txt` los registros con error 400 en `events/400.txt` y los de error 500 en `events/500.txt`.

**Estructura resultante:**

```
~/
└── bug1/
    ├── main.txt
    └── events/
        ├── 400.txt
        └── 500.txt
```

Los comandos exactos y el contenido de los archivos están en el [documento completo](#documento-completo).

---

## Parte 2 · Base de datos (SQL)

### Datos

Base de datos de viajes en taxi de Chicago:

| Tabla | Contenido |
|---|---|
| `neighborhoods` | Barrios de la ciudad (`neighborhood_id`, `name`) |
| `cabs` | Taxis y su compañía (`cab_id`, `vehicle_id`, `company_name`) |
| `trips` | Viajes: inicio, fin, duración, distancia, origen y destino |
| `weather_records` | Clima por hora: `ts`, `temperature`, `description` |

`trips` y `weather_records` no tienen una llave en común. Se relacionan por la hora: `trips.start_ts` con `weather_records.ts`.

### Ejercicio 1 · ¿Cuántos taxis hay realmente?

El plan era tener 10 550 vehículos y llegaron quejas de que no alcanzaban. Conté los automóviles de la tabla `cabs`, teniendo en cuenta que un mismo automóvil podría pertenecer a distintas compañías.

### Ejercicio 2 · Compañías con pocos automóviles

Conté los automóviles por compañía y devolví solo las de **menos de 100**, ordenadas de mayor a menor. Para filtrar sobre un resultado agregado usé `HAVING` en lugar de `WHERE`. Los campos se llaman `cnt` y `company_name`.

### Ejercicio 3 · Coeficiente del costo del viaje según el clima

El coeficiente vale 1 con buen clima y 2 con lluvia o tormenta, y se sospechaba un error en su cálculo. Con `CASE` clasifiqué cada hora de `weather_records` como `Bad` (si `description` contiene «rain» o «storm») o `Good` (el resto). Resultado: dos campos, `ts` y `weather_conditions`, para el periodo del 05/11/2017 00:00 al 06/11/2017 00:00.

### Ejercicio 4 · Número de viajes por compañía

Tras una actualización, las compañías reportaban ganancias distintas a las de la app. Para revisar si el problema estaba en el conteo de viajes, uní `cabs` con `trips` y conté los viajes de cada compañía los días **15 y 16 de noviembre de 2017**, ordenados de mayor a menor por `trips_amount`.

## Qué aprendí

- Que `grep -r` ahorra tiempo cuando no sabes en qué archivo está el problema.
- A separar un incidente en archivos por tipo de error para que el equipo de desarrollo lo analice más fácil.
- A usar `HAVING` para filtrar resultados agregados y `CASE` para clasificar datos.
- A relacionar tablas que no tienen una llave directa, usando la hora como vínculo.
</details>

<details>
<summary><b>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/jira/jira-original.svg" width="25" height="25" alt="Jira" />
  <img src="https://skillicons.dev/icons?i=postman" width="25" height="25" alt="Herramientas" />
  <h6 id="proyecto-dos">Pruebas de API · Urban Grocers</h6>
</b></summary>
<br>

# 

Proyecto del **Sprint 4** de mi formación en pruebas de software (QA). Analicé dos requisitos de la API de **Urban Grocers** (trabajo con kits y servicio de entrega «Order and Go»), diseñé casos de prueba, los ejecuté con **Postman** y reporté los errores encontrados en **Jira**.

---

## Resumen de resultados

| | Casos | Aprobados | Fallidos | Defectos en Jira |
|---|:---:|:---:|:---:|:---:|
| **Requisito 1 · Kits** | 17 | 11 | 6 | 6 |
| **Requisito 2 · Servicio de entrega** | 17 | 9 | 8 | 8 |
| **Total** | **34** | **20** | **14** | **14** |

Cada caso fallido tiene su propio informe de errores en Jira (`S4-1` a `S4-14`).

## Herramientas

| Herramienta | Uso |
|---|---|
| **Postman** | Envío de solicitudes y validación de respuestas |
| **Jira** | Informes de errores |
| **apiDoc** | Endpoints, métodos, parámetros y cuerpos de ejemplo |
| **Requisitos del backend** | Reglas de negocio y criterios de aceptación |
| **Google Sheets / Excel** | Documentación de casos de prueba y resultados |

## Técnicas de diseño de pruebas

- Casos **positivos** y **negativos**
- **Clases de equivalencia**
- **Valores límite**
- Validación de **códigos de estado** y **mensajes de error**

---

## Requisito 1 · Trabajar con los kits

**Qué se probó:** creación de kits y adición de productos a un kit.

| Endpoint | Método |
|---|---|
| `/api/v1/kits` | POST |
| `/api/v1/kits/:id/products` | POST |

**Cobertura de los 17 casos:**

- **Cantidad de productos por kit** (valores límite): 29, 30 y 31 productos. El máximo permitido es 30.
- **Nombre del kit** (valores límite): 1, 2, 3, 14, 15 y 16 caracteres. El máximo permitido es 15.
- **Caracteres no latinos** en el nombre del kit (por ejemplo, cirílico).
- **Campos obligatorios** ausentes en el cuerpo de la solicitud.
- **IDs inexistentes:** producto que no existe y kit que no existe.
- **Estructura incorrecta** del array `productsList`.
- **Datos existentes:** revisión de que ningún kit supere los 30 productos.

### Defectos encontrados (Requisito 1)

| ID | Resumen | Esperado | Obtenido |
|---|---|---|---|
| S4-1 | Se puede crear un kit con nombre de 1 carácter | 400 Bad Request | 201 Created |
| S4-2 | Se puede crear un kit con nombre de 16 caracteres | 400 Bad Request | 201 Created |
| S4-3 | Se acepta un nombre con caracteres no latinos | 400 Bad Request | 201 Created |
| S4-4 | Se crea un kit sin completar los campos obligatorios de producto | 400 Bad Request | 201 Created |
| S4-5 | Se puede agregar un producto con ID inexistente a un kit | Error | Se agrega |
| S4-6 | El kit 1 «Para pícnics» contiene 39 productos y supera el límite de 30 | Máximo 30 | 39 productos |

---

## Requisito 2 · Servicio de entrega «Order and Go»

**Qué se probó:** el cálculo de la entrega según tres campos numéricos obligatorios.

| Endpoint | Método |
|---|---|
| `/order-and-go/v1/delivery` | POST |

```json
{
  "deliveryTime": 9,
  "productsCount": 10,
  "productsWeight": 11
}
```

**Reglas del requisito:**

- Si falta un campo, tiene un tipo incorrecto o el cuerpo es inválido, la respuesta debe ser **400 Bad Request**.
- Si los valores no permiten calcular la entrega, la respuesta es **200 OK** con `"isItPossibleToDeliver": false`.

**Cobertura de los 17 casos:**

- **Campos ausentes:** sin `productsCount`, sin `productsWeight` y sin `deliveryTime`.
- **`deliveryTime`** (valores límite del horario): 7, 8, 9, 21, 22 y 23.
- **Tipo de dato incorrecto:** texto en lugar de número en los tres campos.
- **Estructura de JSON inválida.**
- **`productsCount`** (valores límite): 14, 15 y 16 productos, con el cálculo del costo de entrega.

### Defectos encontrados (Requisito 2)

| ID | Resumen | Esperado | Obtenido |
|---|---|---|---|
| S4-7 | La solicitud sin `productsCount` se procesa | 400 Bad Request | 200 OK |
| S4-8 | La solicitud sin `productsWeight` se procesa | 400 Bad Request | 200 OK |
| S4-9 | La solicitud sin `deliveryTime` se procesa | 400 Bad Request | 200 OK |
| S4-10 | `deliveryTime` = 7 (fuera de horario) permite la entrega | `isItPossibleToDeliver: false` | `true` |
| S4-11 | `deliveryTime` = 23 (fuera de horario) permite la entrega | `isItPossibleToDeliver: false` | `true` |
| S4-12 | `deliveryTime` con texto («ocho») se acepta | 400 Bad Request | 200 OK |
| S4-13 | `productsWeight` con texto («once») se acepta | 400 Bad Request | 200 OK |
| S4-14 | `productsCount` con texto («Diez») se acepta | 400 Bad Request | 200 OK |

---

## Estructura de un caso de prueba

Cada caso de la hoja de cálculo incluye:

| Campo | Contenido |
|---|---|
| Caso de prueba | Qué se quiere verificar |
| Datos de la prueba | Método, endpoint y cuerpo de la solicitud |
| Pasos | Cómo ejecutarlo en Postman |
| Resultado esperado | Código de estado y mensaje según los requisitos |
| Resultado obtenido | Lo que devolvió la API |
| Estatus | `Passed` o `Failed` |
| Enlace al defecto | Informe de errores en Jira |

## Qué aprendí

- A derivar casos de prueba a partir de requisitos, usando clases de equivalencia y valores límite.
- A distinguir un error del cliente (400) de uno del servidor (500) y a validar tanto el código como el mensaje.
- A detectar fallos de validación: la API acepta datos que debería rechazar.
- A redactar informes de errores con pasos, resultado esperado y real, y a enlazarlos con los casos que los originaron.
</details>

<details>
<summary><b>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/chrome/chrome-original.svg" width="25" height="25" alt="DevTools" />
  <img src="https://github.com/user-attachments/assets/a7d6e786-8fa1-4549-b730-a0665d60a6ab" width="25" height="25" alt="Excel"/>
  <img width="25" height="25" alt="image" src="https://github.com/user-attachments/assets/928012b0-5439-4274-bb0f-c8ca3180373f" />
  <img width="25" height="25" alt="image" src="https://github.com/user-attachments/assets/3d03fb00-9db3-4ced-b85c-644bccfa8e6c" />

  <h6 id="proyecto-tres">Proyecto final de QA · Urban Scooter</h6>
  </b></summary>
<br>

Proyecto final de mi formación en pruebas de software (QA). Reúne en un solo trabajo la **teoría de las pruebas** y las pruebas de una plataforma de alquiler de scooters en tres frentes: **aplicación web**, **aplicación móvil** y **API**.

**Autor:** Jorge Alberto Paez Vargas · QA Engineer
**Estado:** entregado y aprobado

---

## Resumen del proyecto

| Tarea | Qué hice | Entregable |
|---|---|---|
| **1 · Teoría de las pruebas** | Respondí 23 preguntas con mis propias palabras | Respuestas en la hoja |
| **2 · Aplicación web** | Mapa mental, lista de comprobación, validación de campos y pruebas funcionales | Lista de comprobación y tabla de validación de datos |
| **3 · Aplicación móvil** | Casos de prueba de funciones y de diseño, comparados con los diseños en Figma | Casos de prueba con resultados |
| **4 · API** | Lista de comprobación frente a los requisitos del back-end y la documentación | Lista de comprobación con resultados |

En las tareas 2, 3 y 4, cada error encontrado se documentó en un informe de errores.

## Herramientas y recursos

| Recurso | Uso |
|---|---|
| **Requisitos de la aplicación** (web, móvil y back-end) | Base para decidir qué probar |
| **Figma** | Diseños de referencia para comparar la interfaz web y móvil |
| **Postman** | Envío de solicitudes a la API |
| **DevTools del navegador** | Revisión de solicitudes y respuestas en la aplicación web |
| **Documentación de la API** | Endpoints, métodos, parámetros y ejemplos |
| **Google Sheets** | Listas de comprobación, casos de prueba y resultados |

## Técnicas de diseño de pruebas

- Casos **positivos** y **negativos**
- **Clases de equivalencia**
- **Valores límite**
- **Listas de comprobación** y **casos de prueba**
- **Mapas mentales** para organizar una función antes de probarla
- Validación de **códigos de estado** y **mensajes de error**
- Pruebas de **diseño** (comparar la interfaz con los diseños de Figma)

---

## Tarea 1 · Teoría de las pruebas

23 preguntas, como las que aparecen en entrevistas de trabajo, respondidas con mis propias palabras. Los temas cubiertos:

- Diseño de pruebas: clases de equivalencia, valores límite y tablas de decisión
- Listas de comprobación frente a casos de prueba
- Informes de errores
- Arquitectura cliente-servidor, HTTP/HTTPS, URL y caché
- Pruebas de aplicaciones móviles
- Bases de datos relacionales y consultas SQL con `JOIN`
- Pruebas automatizadas

## Tarea 2 · Aplicación web Urban Scooter

Probé la aplicación web a partir de sus requisitos y de los diseños en Figma (sin la página de inicio).

**Qué entregué:**

1. **Mapa mental** de la función «Hacer pedido», con su lógica de trabajo y su diseño.
2. **Lista de comprobación** de los requisitos de la pantalla «Estado del pedido».
3. **Tabla de validación de datos** de los campos de la pantalla «Hacer pedido», con casos positivos, negativos, clases de equivalencia y valores límite.
4. **Pruebas funcionales** de todas las funciones, con resultados, capturas de pantalla e informes de errores.

## Tarea 3 · Aplicación móvil Urban Scooter

Probé la aplicación móvil (APK) enfocándome en las funciones destacadas en los requisitos.

**Qué entregué:**

- Casos de prueba de **funcionalidad** y de **diseño**, comparados con los diseños en Figma.
- Resultados de cada caso y enlaces a los informes de errores de los fallos encontrados.

## Tarea 4 · API de Urban Scooter

Probé las funciones de la API frente a los requisitos del back-end y la documentación.

**Qué entregué:**

- Una **lista de comprobación** con los requisitos clave de la API.
- Resultados de cada comprobación y los informes de errores de los defectos encontrados.

---

## Cómo trabajé

1. **Analizar** los requisitos y la documentación antes de probar.
2. **Organizar** las funciones con mapas mentales y listas de comprobación.
3. **Diseñar** casos de prueba con clases de equivalencia y valores límite.
4. **Ejecutar** las pruebas y registrar el resultado obtenido junto al esperado.
5. **Reportar** cada error con pasos, resultado esperado y real, y evidencia en capturas de pantalla.


## Qué aprendí

- A conectar la teoría con la práctica: aplicar clases de equivalencia y valores límite en una aplicación real.
- A probar un mismo producto desde tres ángulos distintos (web, móvil y API) y a comparar lo implementado con los requisitos y los diseños.
- A organizar una función compleja con un mapa mental antes de escribir los casos de prueba.
- A documentar los resultados de forma clara para que otra persona pueda reproducirlos.
</details>



### Login Testing

- Casos de prueba
- Reportes de errores
- Evidencias
- Colección Postman

### API Testing

Pruebas CRUD utilizando Postman.

### SQL

Consultas básicas para validación de bases de datos.

### Proyecto 1 

## Pruebas de regresión
Urban Routes
sitio web y aplicación (alquiler de vehículos con distintas comodidades).

### 1. Urban Routes

![Dashboard Design For a Car Rental Service by Shakuro on Dribbble](https://images.openai.com/static-rsc-4/Gs6xxABldVPfXaUxkq3wocnbSuctC0MfH8y4e4xpTSIA2vEhnM8K2-rtDEcQZi4yKmb6c_U8f2yhi3YbgpB1mMLNO6vb-z_KxXgQVLwKq6hUUxMCVVf_etaiZ3GIkXsB6Kjsn9ghp2FU8gT-0EW_BqMUXpMqmFOyE9eBe81SA5f9xZ1z9ipW13Cf4658dpWi?purpose=fullsize)


### 3. Caso de prueba fallido

![Test Case Execution Report Template - Rebeccachulew.com](https://images.openai.com/static-rsc-4/9XDX2qBY80y6_eL7rljiNQWcAvAngKOPhcNGa9jujTY3rBPSq9RVyaYBqp45Ys7VJReAS63Me1mO6h5iKxjec9eM-i5kJWC-DKvdqpUQV2gTZQo_Ts095n-k9F5fdl_aULyFZLe9jogYhVc2LejqakloYe5uRcfjGI8eYu6kUCR7DulIAZhKdjEJcg1ULiJj?purpose=fullsize)

### 4. Reporte de bug

![The Art of Bug Reporting: How to Write Defects Developers Actually Want to Fix | by Lokuganhewagehashini | Mar, 2026 | Medium](https://images.openai.com/static-rsc-4/mt02QgMQRJZA7jhrDl5PzvCWZvRuHGZvbws42Pqv6iSc7-bKovvJDBx-Hmwz0kXCiGSK7YwDqPWNZKozLk8YvE-E1zj5S9wxZTcgrORXH6eVQZTJoh8GqvDMVKjOaE4LhCj187jc3RKvfA_HVXxf_YeJV4NiBwudfJ1pzLAqjWX2pemn-0FDMyBOyUO_WmbY?purpose=fullsize)


### 5. Gráfico de resultados

![Test Result Trends: How to Analyze Regression Testing Results Over Time in ReportPortal | ReportPortal](https://images.openai.com/static-rsc-4/UEKT1YzhFR6oKvWo5byBg0gXUCehrx6EyubYLRj7MrFeylAbRz9US1BVhEz3EBVSDvoYXO1Z6KWHRufCzTwHkhbWDaOuZXvKe0MiSyR-jxwAoq50lJFRk0UgcboBjumLNqklJa8bjlVfN_1ZPfiA_EBfPgb0yjlREM_rRhrpYp9eMpn0qn-5v8qzQjmXEhKB?purpose=fullsize)


### 🏆 urban-routes-pruebas de regresión

Urban Routes - Pruebas de regresión e informes de errores

Proyecto de pruebas de regresión para la aplicación web y móvil Urban Routes. Incluye ejecución de pruebas, reporte de errores, clasificación de gravedad y documentación de control de calidad.


### Resultados de ejecución

Casos aprobados 62.5%

Casos con defectos 37.5%

Distribución de defectos:

Severidad Menor 20.8%

Severidad Trivial 12.5%

Otra severidad registrada 4.2%
