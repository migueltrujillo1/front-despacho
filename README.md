# Frontend Despacho - Evaluación Final DevOps

Este repositorio contiene el frontend React + Vite del proyecto final de Introducción a Herramientas DevOps.

El frontend se construye con Docker, se publica como imagen en Amazon ECR y se despliega en Amazon EKS mediante GitHub Actions.

## Arquitectura

El frontend se expone públicamente mediante un Service tipo LoadBalancer en Kubernetes. Internamente, Nginx redirige las solicitudes hacia los servicios backend:

- /api/despacho/ → despachos-backend:8081
- /api/ventas/ → ventas-backend:8082

## Tecnologías

- React
- Vite
- Nginx
- Docker
- Amazon ECR
- Amazon EKS
- GitHub Actions