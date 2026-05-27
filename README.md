# Frontend Despacho - Evaluación DevOps EP2

## Descripción

Este repositorio contiene el frontend del sistema de despacho utilizado en la Evaluación Parcial N°2 de la asignatura Introducción a Herramientas DevOps.

El proyecto fue desarrollado con React + Vite y preparado para ejecutarse mediante Docker, permitiendo su despliegue en una instancia EC2 pública de AWS.

Dentro de la arquitectura propuesta, este frontend corresponde a la capa pública de la aplicación. Es el único componente accesible desde Internet, mientras que los servicios backend se mantienen en una capa privada.

## Tecnologías utilizadas

- React
- Vite
- JavaScript
- Tailwind CSS
- Docker
- Docker Compose
- Nginx
- AWS EC2
- GitHub Actions

## Estructura principal del proyecto

```text
front_despacho/
├── public/
├── src/
├── db.json
├── Dockerfile
├── docker-compose.yml
├── nginx.conf
├── package.json
├── package-lock.json
├── vite.config.js
├── tailwind.config.js
├── postcss.config.js
└── README.md
```

## Base de datos ficticia

El archivo `db.json` contiene datos de prueba utilizados durante el desarrollo local del frontend.

Este archivo no corresponde a una base de datos productiva. En el despliegue final, el frontend debe comunicarse con los servicios backend reales desplegados en AWS.

## Ejecución local sin Docker

Para ejecutar el proyecto en modo desarrollo, primero se deben instalar las dependencias:

```bash
npm install
```

Luego se inicia el servidor de desarrollo:

```bash
npm run dev
```

La aplicación quedará disponible normalmente en:

```text
http://localhost:5173
```

## Construcción del proyecto

Para generar la versión productiva del frontend:

```bash
npm run build
```

Vite genera los archivos estáticos en la carpeta:

```text
dist/
```

## Ejecución con Docker

Para construir y levantar el contenedor del frontend:

```bash
docker compose up -d --build
```

Para verificar que el contenedor está activo:

```bash
docker ps
```

Para revisar los logs del contenedor:

```bash
docker logs front-despacho
```

Para detener el contenedor:

```bash
docker compose down
```

## Dockerfile

El proyecto utiliza un Dockerfile con construcción multi-stage.

La primera etapa utiliza Node.js para instalar las dependencias y construir el proyecto React + Vite.

La segunda etapa utiliza Nginx para servir los archivos estáticos generados en la carpeta `dist`.

Este enfoque permite generar una imagen más liviana, separando la etapa de construcción de la etapa de ejecución.

## Docker Compose

El archivo `docker-compose.yml` permite levantar el frontend usando Docker Compose.

Configuración principal:

```yaml
services:
  front-despacho:
    build: .
    container_name: front-despacho
    restart: always
    ports:
      - "80:80"
```

El servicio expone el puerto `80`, permitiendo que el frontend sea accesible desde navegador cuando se despliega en la instancia EC2 pública.

## Configuración de Nginx

El archivo `nginx.conf` se utiliza para servir la aplicación React desde Nginx.

También puede configurarse para redirigir solicitudes hacia los servicios backend privados, manteniendo la separación entre la capa pública y la capa privada de la aplicación.

Flujo esperado:

```text
Usuario → IP pública EC2 Frontend → Nginx → Aplicación React
```

## Despliegue en AWS EC2

El frontend se despliega en una instancia EC2 ubicada en una subred pública.

La instancia debe contar con:

- Docker instalado.
- Docker Compose instalado.
- Git instalado.
- Repositorio clonado desde la rama `deploy`.
- Contenedor del frontend ejecutándose en el puerto `80`.

El acceso desde Internet se realiza mediante:

```text
http://IP_PUBLICA_EC2
```

## Automatización inicial con User Data

La instancia EC2 puede prepararse mediante User Data para automatizar la configuración inicial.

El User Data debe realizar las siguientes acciones:

```text
Actualizar sistema
Instalar Docker
Instalar Docker Compose
Instalar Git
Clonar el repositorio desde GitHub
Cambiar a la rama deploy
Ejecutar docker compose up -d --build
```

Flujo general:

```text
Crear EC2 → Ejecutar User Data → Instalar dependencias → Clonar repositorio → Levantar contenedor
```

## Pipeline CI/CD

El repositorio está preparado para incorporar un pipeline de GitHub Actions.

El pipeline debe activarse al realizar un push sobre la rama `deploy`.

Flujo esperado del pipeline:

```text
Push rama deploy → Build imagen Docker → Push imagen a Docker Hub/ECR → Deploy automático en EC2
```

Este flujo permite automatizar la entrega continua del frontend, reduciendo la intervención manual y facilitando actualizaciones controladas en la instancia EC2.

## Puertos utilizados

| Servicio | Puerto |
|---|---:|
| Frontend desarrollo Vite | 5173 |
| Frontend producción Nginx | 80 |

## Comandos útiles

Construir imagen Docker:

```bash
docker build -t front-despacho .
```

Levantar servicio:

```bash
docker compose up -d --build
```

Ver contenedores activos:

```bash
docker ps
```

Ver logs:

```bash
docker logs front-despacho
```

Detener servicio:

```bash
docker compose down
```

## Relación con la arquitectura general

Este frontend forma parte de una arquitectura de aplicación separada en capas:

```text
Internet
   ↓
EC2 Frontend pública
   ↓
EC2 Backend privada
   ↓
Base de datos privada o contenedor de base de datos con volumen
```

El objetivo de esta separación es mantener solo la capa frontend expuesta a Internet, mientras que los servicios backend y la base de datos permanecen protegidos mediante subredes privadas y reglas de Security Groups.

## Integrantes

- Genesis Manque
- Miguel Trujillo