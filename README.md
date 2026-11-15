# QA Engineer Portafolio

Hola, soy Jorge Páez.

j.paez@outlook.com

# Sobre Mí
Tecnólogo en Informática y certificado QA Engineer Junior, apasionado por la calidad del software y la mejora continua. Tengo conocimientos en pruebas funcionales, diseño de casos de prueba, validación de APIs REST con Postman, SQL y herramientas como Jira y DevTools. Mi experiencia en soporte técnico me permitió desarrollar habilidades de análisis, resolución de problemas y documentación de incidencias. Actualmente estoy construyendo mi carrera en QA y buscando una oportunidad para contribuir al desarrollo de productos de alta calidad.


## Habilidades

<p>
  <img src="https://skillicons.dev/icons?i=postman,js,html,css,cs&perline=5" alt="Herramientas" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/microsoftsqlserver/microsoftsqlserver-plain.svg" width="48" height="48" alt="SQL Server" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/jira/jira-original.svg" width="48" height="48" alt="Jira" />
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/chrome/chrome-original.svg" width="48" height="48" alt="DevTools" />
  <img src="https://github.com/user-attachments/assets/a7d6e786-8fa1-4549-b730-a0665d60a6ab" width="48" height=48 alt="Excel"/>
  <img width="48" height="48" alt="image" src="https://github.com/user-attachments/assets/928012b0-5439-4274-bb0f-c8ca3180373f" />
  <img width="48" height="48" alt="image" src="https://github.com/user-attachments/assets/86854460-13ff-45e4-b389-4dbfc416277a" />

  <p>Manual Testing</p>
  <p>API Testing</p>
</p>

## Proyectos

<details>
<summary><b> <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/microsoftsqlserver/microsoftsqlserver-plain.svg" width="20" height="20" alt="SQL Server" /> <h3>Análisis de logs y base de datos de una app de taxis</h6></b></summary>
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
<summary><b>🔌 API Testing</b></summary>
<br>

Pruebas CRUD utilizando Postman.

🔗 [Ver README completo](https://github.com/JorgePVDV/NOMBRE-DEL-REPO)

</details>

<details>
<summary><b>🗄️ SQL</b></summary>
<br>

Escribe aquí la descripción del proyecto.

🔗 [Ver README completo](https://github.com/JorgePVDV/NOMBRE-DEL-REPO)

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
