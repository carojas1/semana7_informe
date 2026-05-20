<div align="center">

# Instituto Tecnológico Sudamericano

**Materia:** Tendencias Tecnológicas  
**Nivel:** Cuarto Ciclo  
**Autor:** Christian Andrés Rojas  

</div>

---

## 1. Título

**Despliegue de Aplicación Web Desacoplada: Contenerización de Frontend React con Optimización de Caché y Comunicación con Backend Simulado**

---

## 2. Tiempo de duración

**120 minutos**

---

## 3. Fundamentos

Docker es una plataforma de código abierto diseñada para automatizar el despliegue de aplicaciones dentro de contenedores de software, proporcionando una capa adicional de abstracción y automatización de virtualización a nivel de sistema operativo. A diferencia de las máquinas virtuales tradicionales que requieren un sistema operativo completo, Docker utiliza contenedores que comparten el kernel del sistema operativo host, lo que los hace significativamente más ligeros y eficientes en términos de recursos.

Un contenedor Docker es una unidad ejecutable de software que empaqueta el código de la aplicación junto con todas sus dependencias, bibliotecas, archivos de configuración y entorno de ejecución necesarios para que la aplicación funcione de manera consistente en cualquier infraestructura. Los contenedores son inmutables por naturaleza, lo que significa que una vez construidos, su contenido no cambia, garantizando la reproducibilidad del entorno en desarrollo, pruebas y producción.

El archivo Dockerfile es un documento de texto que contiene todas las instrucciones necesarias para construir una imagen de Docker. Este archivo funciona bajo el principio de capas inmutables: cada instrucción en el Dockerfile crea una nueva capa en la imagen. Cuando se ejecuta un comando como `RUN npm install`, Docker crea una capa que contiene los resultados de esa operación. Si el Dockerfile no cambia, Docker reutiliza las capas en caché de construcciones anteriores, acelerando significativamente el proceso de build. Esta característica es fundamental para optimizar los tiempos de desarrollo.

La optimización de la caché de capas es un concepto crítico en la construcción de imágenes Docker eficientes. Una práctica recomendada es copiar el archivo `package.json` antes de copiar todo el código fuente de la aplicación. Esto se debe a que las dependencias de Node.js cambian con menos frecuencia que el código de la aplicación. Al copiar primero el `package.json` y ejecutar `npm install` en una capa separada, Docker puede reutilizar esta capa en caché siempre que las dependencias no cambien, incluso si el código fuente se modifica. Esto reduce el tiempo de construcción de varios minutos a segundos en iteraciones subsecuentes.

Existen dos tipos principales de imágenes Docker: las imágenes públicas oficiales disponibles en Docker Hub, como `node:18-alpine`, que son mantenidas por la comunidad y empresas, y las imágenes personalizadas que los desarrolladores crean para sus aplicaciones específicas mediante Dockerfiles. Las imágenes Alpine Linux son especialmente populares en el desarrollo de contenedores porque proporcionan un sistema operativo mínimo, reduciendo el tamaño de la imagen final a menos de 100 MB en comparación con imágenes basadas en distribuciones Linux completas que pueden superar los 500 MB.

En el contexto de aplicaciones web modernas, la contenerización de un frontend React separado del backend representa una arquitectura de microservicios donde cada componente se ejecuta en su propio contenedor aislado. Esta separación facilita el escalamiento independiente, el despliegue continuo y la posibilidad de desarrollar y mantener cada parte de la aplicación por equipos diferentes. El frontend React, al estar contenerizado, puede comunicarse con el backend mediante solicitudes HTTP a través de la red Docker o mediante puertos expuestos, manteniendo la arquitectura cliente-servidor tradicional pero con todas las ventajas de la portabilidad y consistencia que ofrece Docker.

---

## 4. Conocimientos previos

Para realizar esta práctica, el estudiante necesita tener claros los siguientes temas:

* **Comandos básicos de línea de comandos (CLI):** Uso de terminal en Windows (PowerShell/CMD) o Linux/macOS (bash), navegación entre directorios con `cd`, listado de archivos con `ls` o `dir`, y ejecución de comandos con privilegios de administrador
* **Interfaz de línea de comandos de Docker (Docker CLI):** Comprensión de comandos fundamentales como `docker build`, `docker run`, `docker ps`, `docker images`, y el uso de flags como `-d`, `-p`, `--name`, y sus respectivas funciones
* **Conceptos de redes y puertos:** Entendimiento de qué es un puerto TCP/IP, cómo funciona el mapeo de puertos entre el host y el contenedor (ejemplo: `-p 3000:3000`), y el concepto de localhost o 127.0.0.1
* **Node.js y gestión de paquetes con npm:** Conocimiento del archivo `package.json`, cómo instalar dependencias con `npm install`, ejecutar scripts con `npm start`, y la estructura básica de un proyecto Node.js
* **Arquitectura Cliente-Servidor y aplicaciones web:** Comprensión de cómo funciona una aplicación web con frontend (React) y backend (API REST), comunicación mediante peticiones HTTP (GET, POST), y el flujo de datos entre ambos componentes

---

## 5. Objetivos a alcanzar

* **Implementar** un entorno de desarrollo contenerizado utilizando Docker para ejecutar una aplicación frontend React de forma aislada y reproducible
* **Comprender** la estructura y sintaxis del archivo Dockerfile, identificando cada instrucción y su impacto en la construcción de capas inmutables de la imagen
* **Optimizar** el proceso de construcción de imágenes Docker mediante la técnica de caché de capas, separando la instalación de dependencias del código fuente
* **Configurar** correctamente el mapeo de puertos y la comunicación entre el contenedor frontend y el servidor backend simulado ejecutándose en el host

---

## 6. Equipo necesario

* Computador con sistema operativo Windows 10/11 (64 bits), Linux o macOS con mínimo 8 GB de RAM
* Docker Desktop instalado y funcionando correctamente (versión 4.0 o superior)
* Git instalado para clonar repositorios desde GitHub
* Navegador web moderno (Google Chrome, Mozilla Firefox o Microsoft Edge en su versión más reciente)
* Editor de código Visual Studio Code (recomendado) o cualquier editor de texto plano
* Conexión estable a internet para descargar imágenes de Docker Hub y repositorios de GitHub
* Node.js instalado localmente (versión 16 o superior) para ejecutar el servidor backend simulado

---

## 7. Material de apoyo

* Documentación oficial de Docker: https://docs.docker.com/
* Docker Hub - Imagen oficial de Node.js: https://hub.docker.com/_/node
* Guía de Dockerfile Best Practices: https://docs.docker.com/develop/dev-best-practices/
* Repositorio backend simulado (mockAPI): https://github.com/[usuario]/mockAPI
* Repositorio frontend React (suda-frontend-s6): https://github.com/[usuario]/suda-frontend-s6
* Documentación de Create React App: https://create-react-app.dev/docs/deployment/
* Material de la asignatura Tendencias Tecnológicas proporcionado por el docente

---

## 8. Procedimiento

### Paso 1: Clonar los repositorios necesarios

Abrir la terminal (PowerShell en Windows o terminal en Linux/macOS) y ejecutar los siguientes comandos para clonar el backend y el frontend:

```bash
# Clonar el repositorio del backend simulado
git clone https://github.com/[usuario]/mockAPI.git
cd mockAPI

# Instalar dependencias del backend
npm install

# Levantar el servidor backend en el puerto 3100
npm start
```

Verificar que el backend está corriendo accediendo a `http://localhost:3100` en el navegador. El servidor debe responder con datos JSON.

Abrir una nueva terminal y clonar el repositorio del frontend:

```bash
# Clonar el repositorio del frontend React
git clone https://github.com/[usuario]/suda-frontend-s6.git
cd suda-frontend-s6
```

---

### Paso 2: Crear el archivo .dockerignore

Dentro del directorio `suda-frontend-s6`, crear un archivo llamado `.dockerignore` para excluir archivos innecesarios de la imagen Docker:

```bash
# Crear el archivo .dockerignore
echo "node_modules" > .dockerignore
echo ".git" >> .dockerignore
echo ".gitignore" >> .dockerignore
echo "README.md" >> .dockerignore
echo ".env" >> .dockerignore
```

Este archivo le indica a Docker qué archivos y carpetas no debe incluir al copiar el contexto de construcción, reduciendo el tamaño de la imagen final.

---

### Paso 3: Crear el archivo Dockerfile

Dentro del directorio `suda-frontend-s6`, crear un archivo llamado `Dockerfile` (sin extensión) con el siguiente contenido:

```dockerfile
# Usar imagen base de Node.js 18 con Alpine Linux
FROM node:18-alpine

# Establecer el directorio de trabajo dentro del contenedor
WORKDIR /app

# Copiar SOLO el package.json y package-lock.json primero (optimización de caché)
COPY package*.json ./

# Instalar las dependencias (esta capa se reutilizará si package.json no cambia)
RUN npm install

# Copiar el resto del código fuente de la aplicación
COPY . .

# Exponer el puerto 3000 donde correrá la aplicación React
EXPOSE 3000

# Comando para iniciar la aplicación
CMD ["npm", "start"]
```

**Explicación de la optimización de caché:**

La clave está en copiar `package.json` antes que todo el código fuente. Si solo modificamos archivos `.js` o `.jsx` de la aplicación pero no cambiamos las dependencias, Docker reutilizará la capa de `npm install` desde la caché, haciendo que la construcción sea mucho más rápida.

---

### Paso 4: Construir la imagen Docker

Desde el directorio `suda-frontend-s6` donde está el Dockerfile, ejecutar el comando para construir la imagen:

```bash
# Construir la imagen con el tag "sudafront"
docker build -t sudafront .
```

Este proceso descargará la imagen base `node:18-alpine`, copiará los archivos, instalará las dependencias y creará la imagen personalizada. La primera vez tomará varios minutos, pero las construcciones posteriores serán más rápidas gracias a la caché de capas.

Verificar que la imagen se creó correctamente:

```bash
docker images
```

Debe aparecer la imagen `sudafront` en la lista.

---

### Paso 5: Ejecutar el contenedor

Correr un contenedor a partir de la imagen creada, mapeando el puerto 3000:

```bash
# Ejecutar el contenedor en modo detached (segundo plano)
docker run -d -p 3000:3000 --name sudafront-container sudafront
```

**Explicación de los parámetros:**

| Parámetro | Función |
|---|---|
| `-d` | Ejecuta el contenedor en segundo plano (detached mode) |
| `-p 3000:3000` | Mapea el puerto 3000 del host al puerto 3000 del contenedor |
| `--name sudafront-container` | Asigna un nombre identificable al contenedor |
| `sudafront` | Nombre de la imagen a utilizar |

---

### Paso 6: Verificar que el contenedor está corriendo

Ejecutar el siguiente comando para listar los contenedores activos:

```bash
docker ps
```

Debe aparecer el contenedor `sudafront-container` con estado `Up` y el puerto `0.0.0.0:3000->3000/tcp`.

---

### Paso 7: Acceder a la aplicación desde el navegador

Abrir el navegador web e ir a la dirección:
http://localhost:3000

La aplicación React debe cargarse correctamente y mostrar la interfaz del frontend. Si el backend está corriendo en el puerto 3100, la aplicación podrá consumir datos de la API simulada.

---

### Paso 8: Detener y limpiar (opcional)

Una vez finalizada la práctica, se puede detener y eliminar el contenedor:

```bash
# Detener el contenedor
docker stop sudafront-container

# Eliminar el contenedor
docker rm sudafront-container

# (Opcional) Eliminar la imagen
docker rmi sudafront
```

---

## 9. Resultados esperados

Descripción gráfica de los resultados obtenidos durante la práctica:

### Resultado 1: Construcción exitosa de la imagen Docker

**Figura 9-1. Construcción de la imagen Docker (Build).**
<img width="1537" height="1110" alt="resultado1" src="https://github.com/user-attachments/assets/6af41899-7a43-424a-841c-4047ce332572" />

*Figura 9-1. Proceso de construcción de la imagen Docker con todas las capas ejecutándose correctamente.*

---

### Resultado 2: Backend simulado ejecutándose

**Figura 9-2. Levantamiento del servidor backend local en el puerto 3100.**

<img width="927" height="687" alt="resultado2" src="https://github.com/user-attachments/assets/be37bc12-2e0e-4e8a-a462-964bee529628" />


*Figura 9-2. Servidor backend activo y escuchando peticiones en http://localhost:3100.*

---

### Resultado 3: Contenedor en ejecución

**Figura 9-3. Contenedor ejecutándose en Docker Desktop.**

<img width="1147" height="51" alt="resultado3" src="https://github.com/user-attachments/assets/2254d0d3-23a4-45a9-b29a-74b1d7ca80d8" />


*Figura 9-3. Contenedor activo visible en la interfaz de Docker Desktop con el puerto 3000 mapeado correctamente.*

---

### Resultado 4: Aplicación funcionando en el navegador

**Figura 9-4. Aplicación React funcionando en el navegador (Resultado Final).**

<img width="1907" height="952" alt="resultado_final" src="https://github.com/user-attachments/assets/e759c3e4-ba7a-40b3-a762-2a7e69e83aa6" />


*Figura 9-4. Resultado final de la práctica: aplicación frontend React contenerizada y funcional, comunicándose con el backend simulado.*

---

## 10. Bibliografía

Matthias, K., & Kane, S. P. (2015). *Docker: Up & Running: Shipping Reliable Containers in Production*. O'Reilly Media, Inc. https://www.oreilly.com/library/view/docker-up/9781491917565/

Miell, I., & Sayers, A. (2019). *Docker in Practice* (2nd ed.). Manning Publications. https://www.manning.com/books/docker-in-practice-second-edition

Docker Inc. (2024). *Dockerfile reference*. Docker Documentation. https://docs.docker.com/engine/reference/builder/

Docker Inc. (2024). *Best practices for writing Dockerfiles*. Docker Documentation. https://docs.docker.com/develop/dev-best-practices/

Turnbull, J. (2016). *The Docker Book: Containerization is the new virtualization*. James Turnbull. https://dockerbook.com/

---

<div align="center">

*Christian Andrés Rojas · Tendencias Tecnológicas · Cuarto Ciclo*  
*Instituto Tecnológico Sudamericano*

</div>
