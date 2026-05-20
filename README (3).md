<div align="center">

# 🐳 Despliegue y Contenerización de un Frontend React con Docker
### Optimización de Caché de Capas en Imágenes Personalizadas

---

| | |
|---|---|
| **👨‍💻 Autor** | Christian Andrés Rojas |
| **📚 Materia** | Tendencias Tecnológicas |
| **🎓 Nivel** | Cuarto Ciclo |
| **🏫 Institución** | Instituto Tecnológico Sudamericano |
| **📅 Práctica** | Laboratorio 06 — Contenerización de Aplicaciones Web |

---

</div>

## 📋 Tabla de Contenidos

1. [Título y Descripción](#1-título-y-descripción)
2. [Tiempo de Duración](#2-tiempo-de-duración)
3. [Fundamentos Teóricos](#3-fundamentos-teóricos)
4. [Conocimientos Previos](#4-conocimientos-previos)
5. [Objetivos](#5-objetivos)
6. [Equipo Necesario](#6-equipo-necesario)
7. [Material de Apoyo](#7-material-de-apoyo)
8. [Procedimiento](#8-procedimiento)
9. [Diagrama de la Solución](#9-diagrama-de-la-solución)
10. [Resultados Esperados](#10-resultados-esperados)
11. [Conclusiones](#11-conclusiones)
12. [Bibliografía](#12-bibliografía)

---

## 1. Título y Descripción

### Contenerización de un Frontend React con Docker: Construcción de Imágenes Personalizadas y Estrategias de Optimización de Caché por Capas para el Despliegue de Aplicaciones Web Modernas

Esta práctica de laboratorio aborda el proceso completo de **contenerizar una aplicación frontend** desarrollada con React y Vite, integrándola con un backend simulado (mockAPI) a través de una red local. El enfoque central es la comprensión del sistema de capas de Docker, la escritura correcta de un `Dockerfile` y la aplicación de buenas prácticas de optimización para reducir tiempos de construcción mediante el uso eficiente de la caché de capas.

El proyecto utiliza el repositorio `suda-frontend-s6` como aplicación cliente y `mockAPI` como servicio backend local, replicando un entorno de desarrollo real donde el frontend se sirve desde un contenedor Docker mientras consume datos de una API que corre directamente en el host.

---

## 2. Tiempo de Duración

⏱️ **120 minutos**

---

## 3. Fundamentos Teóricos

### 3.1 Imágenes Propias vs. Imágenes Públicas en Docker

Docker opera bajo el concepto de **imágenes**, que son plantillas de solo lectura a partir de las cuales se crean los contenedores. Existen dos grandes categorías:

- **Imágenes públicas (base images):** Son imágenes preconstruidas disponibles en Docker Hub u otros registros públicos. Ejemplos comunes son `node:18-alpine`, `nginx:latest` o `python:3.11-slim`. Estas imágenes son mantenidas por la comunidad o por los propios proveedores del software y representan el punto de partida más conveniente para construir sobre ellas.

- **Imágenes personalizadas (custom images):** Son imágenes creadas por el propio desarrollador a partir de un `Dockerfile`. Estas imágenes heredan de una imagen base pública y le añaden capas con el código fuente, las dependencias y la configuración específica del proyecto. Son el estándar para desplegar aplicaciones propias en producción o en entornos de desarrollo controlados.

La elección de usar una imagen personalizada permite reproducibilidad total: cualquier miembro del equipo, en cualquier máquina, puede ejecutar exactamente el mismo entorno con un único comando.

### 3.2 El Sistema de Capas del Dockerfile

Un `Dockerfile` es un archivo de instrucciones secuenciales que Docker ejecuta línea por línea para construir una imagen. **Cada instrucción genera una nueva capa** en el sistema de archivos de la imagen final. Las instrucciones más comunes y su efecto en las capas son:

| Instrucción | Función |
|---|---|
| `FROM` | Define la imagen base. Es siempre la primera capa. |
| `WORKDIR` | Establece el directorio de trabajo dentro del contenedor. |
| `COPY` | Copia archivos del host al sistema de archivos de la imagen. |
| `RUN` | Ejecuta un comando durante la construcción (instala paquetes, compila, etc.). |
| `EXPOSE` | Documenta el puerto que el contenedor escucha en tiempo de ejecución. |
| `CMD` | Define el comando por defecto que se ejecuta cuando el contenedor arranca. |

Este sistema de capas tiene una implicación poderosa: **las capas son inmutables y reutilizables**. Si una capa no ha cambiado entre dos builds, Docker la reutiliza desde su caché en lugar de regenerarla.

### 3.3 Optimización de Caché: Por qué copiar `package.json` primero

El paso más lento en la construcción de una imagen Node.js es invariablemente `npm install`, ya que descarga e instala todas las dependencias del proyecto desde la red. La optimización clave consiste en **separar la copia de los archivos de dependencias de la copia del código fuente**:

```dockerfile
# ✅ Enfoque OPTIMIZADO
COPY package.json package-lock.json ./   # Capa A
RUN npm install                          # Capa B (costosa, ~2-3 min)
COPY . .                                 # Capa C (solo código fuente)
```

**¿Por qué funciona?** Docker invalida la caché de una capa solo cuando el contenido que la generó ha cambiado. Si únicamente se modifica el código fuente (archivos `.jsx`, `.css`, etc.) pero NO el `package.json`, Docker reutiliza las capas A y B desde la caché y solo regenera la capa C. El resultado: el siguiente build toma **segundos en lugar de minutos**.

```dockerfile
# ❌ Enfoque SIN optimizar
COPY . .                                 # Cualquier cambio en cualquier archivo...
RUN npm install                          # ...obliga a reinstalar TODAS las dependencias
```

Esta distinción entre datos que cambian frecuentemente (código) y datos que cambian ocasionalmente (dependencias) es un principio fundamental del diseño eficiente de `Dockerfiles` (Matthias & Kane, 2015).

---

## 4. Conocimientos Previos

Para llevar a cabo esta práctica satisfactoriamente, el estudiante debe tener dominio de los siguientes temas:

- ✅ **Comandos básicos de Linux/terminal:** navegación con `cd`, `ls`, `mkdir`, `cat`, manejo de archivos y permisos de ejecución.
- ✅ **Node.js y npm:** comprensión del archivo `package.json`, el directorio `node_modules` y el flujo de instalación de dependencias con `npm install`.
- ✅ **React y Vite:** conocimiento básico de la estructura de un proyecto frontend moderno, scripts de inicio (`npm start`) y proceso de build.
- ✅ **Git y GitHub:** clonación de repositorios con `git clone`, uso básico de ramas y commits.
- ✅ **Conceptos de redes locales:** comprensión de `localhost`, puertos TCP, y la diferencia entre el sistema host y un contenedor.
- ✅ **Docker CLI fundamental:** familiaridad con los conceptos de imagen, contenedor, y los comandos `docker build`, `docker run`, `docker ps` y `docker stop`.
- ✅ **Variables de entorno:** comprensión de `.env` files y cómo las aplicaciones consumen configuración externa.
- ✅ **Arquitectura cliente-servidor:** comprensión de cómo un frontend consume endpoints de una API REST.

---

## 5. Objetivos

### 5.1 Objetivo General

> **Implementar** la contenerización de una aplicación frontend React mediante Docker, aplicando buenas prácticas de construcción de imágenes personalizadas con optimización de caché por capas, para replicar un entorno de despliegue profesional y reproducible.

### 5.2 Objetivos Específicos

1. **Comprender** el modelo de capas de Docker y el ciclo de vida de una imagen personalizada, identificando cómo cada instrucción del `Dockerfile` contribuye a la imagen final y afecta los tiempos de construcción.

2. **Analizar** la diferencia entre copiar únicamente el `package.json` antes de ejecutar `npm install` versus copiar todo el código fuente de manera indiscriminada, evaluando el impacto en la eficiencia de la caché de Docker.

3. **Aplicar** los comandos `docker build` y `docker run` con sus respectivos parámetros de configuración (`-t`, `-d`, `-p`, `--name`) para construir una imagen y lanzar un contenedor funcional que sirva la aplicación React en el puerto 3000.

4. **Construir** un `Dockerfile` optimizado y un archivo `.dockerignore` correctamente configurado que permita desplegar el frontend `suda-frontend-s6` en un contenedor Docker, conectado a un backend local (`mockAPI`) corriendo en el puerto 3100 del host.

---

## 6. Equipo Necesario

### Software

| Herramienta | Versión recomendada | Propósito |
|---|---|---|
| **Docker Desktop** | 4.x o superior | Motor de contenedores + interfaz gráfica |
| **Node.js** | 18 LTS | Entorno de ejecución para el frontend |
| **npm** | 9.x o superior | Gestor de paquetes de Node.js |
| **Vite** | Incluido en el proyecto | Bundler y servidor de desarrollo para React |
| **Git** | 2.x o superior | Control de versiones y clonación de repositorios |
| **VS Code** *(recomendado)* | Última versión estable | Editor de código con soporte para Docker |

### Hardware mínimo

| Componente | Requisito mínimo |
|---|---|
| **RAM** | 8 GB (4 GB mínimos absolutos) |
| **Almacenamiento** | 5 GB libres para imágenes y contenedores |
| **CPU** | Procesador de 64 bits con soporte para virtualización |
| **OS** | Windows 10/11, macOS 12+, o Ubuntu 20.04+ |

---

## 7. Material de Apoyo

### Documentación Oficial

- 📖 [Documentación oficial de Docker](https://docs.docker.com/) — Referencia completa de comandos, Dockerfile y Docker Compose.
- 📖 [Referencia de Dockerfile](https://docs.docker.com/engine/reference/builder/) — Especificación de cada instrucción disponible.
- 📖 [Docker Hub — Imagen node:18-alpine](https://hub.docker.com/_/node) — Página oficial de la imagen base utilizada.
- 📖 [Documentación de Vite](https://vitejs.dev/guide/) — Guía oficial del bundler utilizado en el proyecto.
- 📖 [React Documentation](https://react.dev/) — Documentación oficial del framework de frontend.

### Repositorios del Proyecto

- 🔗 **Backend simulado (mockAPI):** `https://github.com/<organización>/mockAPI`
- 🔗 **Frontend React (suda-frontend-s6):** `https://github.com/<organización>/suda-frontend-s6`

> ⚠️ *Reemplaza `<organización>` con la URL real proporcionada por el docente.*

### Recursos Adicionales

- 📹 [Play with Docker](https://labs.play-with-docker.com/) — Entorno Docker en el navegador para practicar sin instalación local.
- 📹 [Docker — Documentación de Best Practices para Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

---

## 8. Procedimiento

### Paso 1 — Clonación de los repositorios

Abre una terminal (PowerShell en Windows o Bash en macOS/Linux) y clona ambos repositorios del proyecto:

```bash
# Clona el backend simulado (mockAPI)
git clone https://github.com/<organización>/mockAPI.git

# Clona el frontend React
git clone https://github.com/<organización>/suda-frontend-s6.git
```

> 💡 Asegúrate de que ambas carpetas queden en el mismo directorio raíz para facilitar la navegación.

---

### Paso 2 — Levantamiento del backend local (mockAPI)

El backend actúa como una API REST simulada. Debe estar corriendo en el host **antes** de levantar el contenedor del frontend para que este pueda consumirla.

```bash
# Ingresa al directorio del backend
cd mockAPI

# Instala las dependencias del backend
npm install

# Inicia el servidor en el puerto 3100
npm start
```

**Verificación:** Abre tu navegador y visita `http://localhost:3100`. Deberías ver una respuesta JSON del servidor mock. Deja esta terminal abierta y no cierres el servidor.

---

### Paso 3 — Creación del archivo `.dockerignore`

Antes de construir la imagen, crea un archivo `.dockerignore` en la raíz del proyecto `suda-frontend-s6`. Este archivo le indica a Docker qué archivos y carpetas **excluir** del contexto de construcción, reduciendo el tamaño de la imagen y evitando copiar archivos innecesarios o sensibles.

```bash
# Entra al directorio del frontend
cd ../suda-frontend-s6

# Crea el archivo .dockerignore (PowerShell en Windows)
New-Item .dockerignore -ItemType File

# O en macOS/Linux
touch .dockerignore
```

Edita el archivo `.dockerignore` con el siguiente contenido:

```
node_modules
.git
.gitignore
.env
.env.local
.env.*.local
dist
build
*.log
npm-debug.log*
README.md
.DS_Store
Thumbs.db
```

> 💡 **¿Por qué excluir `node_modules`?** Esta carpeta puede contener miles de archivos y pesar varios cientos de MB. No tiene sentido copiarla a la imagen porque el `Dockerfile` ejecutará `npm install` dentro del contenedor y generará su propia `node_modules` limpia y compatible con la arquitectura del contenedor.

---

### Paso 4 — Creación del `Dockerfile` optimizado

Crea un archivo llamado `Dockerfile` (sin extensión) en la raíz de `suda-frontend-s6`:

```bash
# PowerShell
New-Item Dockerfile -ItemType File

# macOS/Linux
touch Dockerfile
```

Agrega el siguiente contenido:

```dockerfile
# ─────────────────────────────────────────────────────────────────
# ETAPA ÚNICA — Servidor de desarrollo con Node.js
# Imagen base: node:18-alpine (Alpine Linux, ~5 MB vs ~900 MB de la
# versión full). Ideal para contenedores ligeros en desarrollo.
# ─────────────────────────────────────────────────────────────────
FROM node:18-alpine

# Define el directorio de trabajo dentro del contenedor.
# Todos los comandos posteriores se ejecutarán desde /app.
# Si la carpeta no existe, Docker la crea automáticamente.
WORKDIR /app

# ─── OPTIMIZACIÓN DE CACHÉ ────────────────────────────────────────
# Copiamos SOLO los archivos de dependencias PRIMERO.
# Esta capa solo se invalida si package.json o package-lock.json cambian.
# Mientras el código fuente cambie pero no las dependencias,
# Docker reutilizará esta capa desde caché → builds mucho más rápidos.
COPY package.json package-lock.json ./

# Instala las dependencias de Node.js dentro del contenedor.
# Esta instrucción genera la capa más costosa en tiempo.
RUN npm install

# ─── CÓDIGO FUENTE ────────────────────────────────────────────────
# Ahora copiamos el resto del código fuente.
# Esta capa cambia frecuentemente, pero no re-ejecuta npm install.
COPY . .

# Documenta que el contenedor escucha en el puerto 3000.
# EXPOSE no publica el puerto; solo sirve como documentación
# y señal para herramientas como docker-compose.
EXPOSE 3000

# Comando por defecto al iniciar el contenedor.
# Se usa formato JSON (exec form) para evitar que el proceso
# quede como hijo de un shell y reciba correctamente las señales
# del sistema operativo (como SIGTERM al detener el contenedor).
CMD ["npm", "start"]
```

---

### Paso 5 — Construcción de la imagen Docker

Con el `Dockerfile` y `.dockerignore` listos, construye la imagen personalizada:

```bash
docker build -t sudafront .
```

**Desglose del comando:**

| Parte del comando | Significado |
|---|---|
| `docker build` | Inicia el proceso de construcción de una imagen |
| `-t sudafront` | Asigna el **tag** (nombre) `sudafront` a la imagen resultante |
| `.` | Indica que el contexto de construcción es el directorio actual (donde está el `Dockerfile`) |

**Salida esperada en consola:**

```
[+] Building 45.2s (9/9) FINISHED
 => [internal] load build definition from Dockerfile
 => [internal] load .dockerignore
 => [1/5] FROM docker.io/library/node:18-alpine
 => [2/5] WORKDIR /app
 => [3/5] COPY package.json package-lock.json ./
 => [4/5] RUN npm install
 => [5/5] COPY . .
 => exporting to image
 => => naming to docker.io/library/sudafront
```

> 💡 En el **segundo build** (si solo cambias código fuente), notarás que los pasos 3 y 4 muestran `CACHED` — esa es la optimización de caché funcionando.

---

### Paso 6 — Ejecución del contenedor

```bash
docker run -d -p 3000:3000 --name sudafront-container sudafront
```

**Desglose del comando:**

| Parámetro | Significado |
|---|---|
| `docker run` | Crea y arranca un contenedor a partir de una imagen |
| `-d` | Modo **detached**: el contenedor corre en segundo plano, liberando la terminal |
| `-p 3000:3000` | Mapeo de puertos: `HOST_PORT:CONTAINER_PORT`. El puerto 3000 del host se redirige al puerto 3000 del contenedor |
| `--name sudafront-container` | Asigna un nombre legible al contenedor para gestión posterior |
| `sudafront` | Nombre de la imagen a usar para crear el contenedor |

**Verificación del contenedor activo:**

```bash
# Lista todos los contenedores en ejecución
docker ps

# Salida esperada:
# CONTAINER ID   IMAGE       COMMAND       CREATED         STATUS         PORTS                    NAMES
# a3f1c9e2b847   sudafront   "npm start"   2 seconds ago   Up 1 second    0.0.0.0:3000->3000/tcp   sudafront-container
```

**Comandos útiles de gestión:**

```bash
# Ver los logs del contenedor en tiempo real
docker logs -f sudafront-container

# Detener el contenedor
docker stop sudafront-container

# Eliminar el contenedor (debe estar detenido)
docker rm sudafront-container

# Eliminar la imagen
docker rmi sudafront
```

---

## 9. Diagrama de la Solución

```
╔══════════════════════════════════════════════════════════════════════╗
║                        MÁQUINA HOST (Tu PC)                          ║
║                                                                      ║
║   ┌─────────────────┐         ┌──────────────────────────────────┐  ║
║   │                 │         │     CONTENEDOR DOCKER             │  ║
║   │   NAVEGADOR WEB │  HTTP   │  ┌────────────────────────────┐  │  ║
║   │   localhost:3000│◄───────►│  │  React App (Vite/Node.js)  │  │  ║
║   │                 │  :3000  │  │  Puerto interno: 3000      │  │  ║
║   └─────────────────┘         │  └──────────┬─────────────────┘  │  ║
║                               │             │                     │  ║
║                               └─────────────┼─────────────────────┘  ║
║                                             │ HTTP Request            ║
║                                             │ host.docker.internal    ║
║                                             │ :3100                   ║
║                                             ▼                         ║
║                               ┌──────────────────────────────────┐  ║
║                               │       BACKEND LOCAL (Host)        │  ║
║                               │   mockAPI — Puerto 3100           │  ║
║                               │   (Node.js corriendo en el host)  │  ║
║                               └──────────────────────────────────┘  ║
╚══════════════════════════════════════════════════════════════════════╝

  Flujo de datos:
  [1] El usuario abre http://localhost:3000 en el navegador
  [2] La solicitud llega al puerto 3000 del HOST
  [3] Docker redirige la solicitud al puerto 3000 del CONTENEDOR (-p 3000:3000)
  [4] El contenedor sirve la aplicación React
  [5] React realiza peticiones a la API en http://host.docker.internal:3100
  [6] Docker resuelve host.docker.internal → IP del host
  [7] mockAPI responde con los datos JSON al contenedor
  [8] React renderiza los datos en el navegador del usuario
```

> 📌 **Nota importante:** Desde dentro del contenedor, `localhost` se refiere al propio contenedor, no al host. Para acceder a servicios del host (como el mockAPI en el puerto 3100), se debe usar `host.docker.internal` en la URL de la API dentro del código React.

---

## 10. Resultados Esperados

Al completar exitosamente todos los pasos del procedimiento, se deben obtener los siguientes resultados verificables:

### 10.1 Estructura de carpetas del proyecto

El proyecto `suda-frontend-s6` debe contener los archivos `Dockerfile` y `.dockerignore` creados durante la práctica, además de la estructura estándar de un proyecto React con Vite.

<img width="800" alt="Evidencia - Estructura de carpetas del proyecto con Dockerfile y .dockerignore" src="ARRASTRE_SU_IMAGEN_AQUI_1" />

> 📷 *Captura de pantalla del explorador de archivos o del terminal mostrando la estructura del proyecto con `Dockerfile` y `.dockerignore` visibles.*

---

### 10.2 Backend (mockAPI) corriendo en el puerto 3100

El servidor mock debe estar activo y respondiendo solicitudes HTTP en `http://localhost:3100`, mostrando datos en formato JSON al ser consultado desde el navegador o desde una herramienta como Postman.

<img width="800" alt="Evidencia - Backend mockAPI corriendo en puerto 3100" src="ARRASTRE_SU_IMAGEN_AQUI_2" />

> 📷 *Captura del navegador o terminal mostrando el servidor mockAPI activo y respondiendo en el puerto 3100.*

---

### 10.3 Build de Docker exitoso

La ejecución de `docker build -t sudafront .` debe completarse sin errores, mostrando en consola las 9 etapas de construcción finalizadas correctamente. En builds subsiguientes, los pasos de dependencias deben mostrar `CACHED`.

<img width="800" alt="Evidencia - Build de Docker completado exitosamente con todas las capas" src="ARRASTRE_SU_IMAGEN_AQUI_3" />

> 📷 *Captura de la consola mostrando el output completo del `docker build` con el mensaje `FINISHED` y todas las capas construidas. Incluir también la salida del segundo build para evidenciar el uso de caché.*

---

### 10.4 Aplicación React funcionando en el navegador desde el contenedor

Al acceder a `http://localhost:3000`, la aplicación React debe cargar correctamente y mostrar datos provenientes del backend mockAPI, demostrando la comunicación exitosa entre el contenedor y el servicio del host.

<img width="800" alt="Evidencia - Aplicación React funcionando en localhost:3000 desde el contenedor Docker" src="ARRASTRE_SU_IMAGEN_AQUI_4" />

> 📷 *Captura del navegador en `http://localhost:3000` mostrando la interfaz de la aplicación React con datos cargados. Opcionalmente, incluir también la vista de Docker Desktop mostrando el contenedor `sudafront-container` en estado `Running`.*

---

## 11. Conclusiones

**1. Docker como estándar de reproducibilidad en el desarrollo moderno**

La contenerización con Docker elimina el problema histórico de *"en mi máquina funciona"* al encapsular la aplicación junto con todo su entorno de ejecución en una unidad portable y autosuficiente. A través de esta práctica se comprobó que un `Dockerfile` bien escrito permite reproducir el mismo comportamiento en cualquier sistema operativo que tenga Docker instalado, independientemente de las versiones de Node.js o de las dependencias globales del host. Esta capacidad de reproducibilidad es uno de los pilares de los flujos de trabajo de integración y despliegue continuo (CI/CD) en la industria.

**2. La optimización de caché por capas es una práctica esencial, no opcional**

La diferencia entre un `Dockerfile` naive (que copia todo el código y luego ejecuta `npm install`) y uno optimizado (que copia primero el `package.json` y luego el código) puede representar la diferencia entre un build de 3 minutos y uno de 5 segundos en cada iteración de desarrollo. Esta práctica demuestra que el diseño del `Dockerfile` tiene un impacto directo y medible en la productividad del equipo, y que el conocimiento del modelo de capas de Docker no es simplemente teórico: se traduce en ahorro real de tiempo durante el ciclo de desarrollo (Miell & Sayers, 2019).

**3. La arquitectura contenedor-host requiere una configuración de red explícita y consciente**

Un error frecuente al contenerizar aplicaciones frontend es asumir que `localhost` dentro del contenedor se refiere al host. Esta práctica evidenció que la red del contenedor es aislada del host, y que para comunicar un servicio contenedorizado con uno que corre en el sistema anfitrión (como el mockAPI en el puerto 3100) es necesario usar el hostname especial `host.docker.internal`. Este conocimiento es fundamental para diseñar arquitecturas de microservicios donde múltiples componentes pueden correr en distintos contextos de red.

---

## 12. Bibliografía

> Las referencias siguen el formato APA 7ma edición.

Matthias, K., & Kane, S. P. (2015). *Docker: Up & running: Shipping reliable containers in production*. O'Reilly Media. https://www.oreilly.com/library/view/docker-up/9781491917565/

Miell, I., & Sayers, A. (2019). *Docker in practice* (2nd ed.). Manning Publications. https://www.manning.com/books/docker-in-practice-second-edition

Docker Inc. (2024). *Dockerfile reference*. Docker Documentation. https://docs.docker.com/engine/reference/builder/

Docker Inc. (2024). *Best practices for writing Dockerfiles*. Docker Documentation. https://docs.docker.com/develop/develop-images/dockerfile_best-practices/

OpenJS Foundation. (2024). *Node.js 18 LTS documentation*. Node.js. https://nodejs.org/docs/latest-v18.x/api/

---

<div align="center">

---

*Práctica elaborada para la materia de **Tendencias Tecnológicas** — Cuarto Ciclo*
*Instituto Tecnológico Sudamericano · Christian Andrés Rojas*

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white)

</div>
