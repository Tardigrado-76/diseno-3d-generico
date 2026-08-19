# Tavilga 3D Configurator

[![Vite](https://img.shields.io/badge/Vite-Frontend-blue.svg)](https://vitejs.dev/)
[![React Three Fiber](https://img.shields.io/badge/R3F-3D%20Rendering-black.svg)](https://docs.pmnd.rs/react-three-fiber/)
[![Supabase](https://img.shields.io/badge/Supabase-BaaS-green.svg)](https://supabase.com/)

## 📌 Resumen Ejecutivo
**Tavilga 3D** es un configurador paramétrico de alta fidelidad basado en tecnologías web inmersivas (WebXR y WebGL). Diseñado para permitir a los usuarios visualizar, configurar e interactuar con modelos 3D en tiempo real desde el navegador, con persistencia segura en la nube vía Supabase.

Esta solución Enterprise-Grade optimiza los tiempos de carga mediante empaquetado optimizado (Vite + Terser) y renderizado reactivo con React Three Fiber.

---

## 🏗️ Arquitectura de la Solución (Mermaid)

```mermaid
flowchart TD
    %% Estilos
    classDef frontend fill:#3b82f6,stroke:#fff,stroke-width:2px,color:#fff;
    classDef threejs fill:#000,stroke:#fff,stroke-width:2px,color:#fff;
    classDef backend fill:#10b981,stroke:#fff,stroke-width:2px,color:#fff;
    classDef infra fill:#6366f1,stroke:#fff,stroke-width:2px,color:#fff;

    Client([Usuario - Navegador Web]) --> UI[Frontend - React + Vite]:::frontend
    
    UI --> State[Gestión de Estado - Zustand]:::frontend
    UI --> Scene[Render Engine - React Three Fiber]:::threejs
    
    Scene --> XR[Módulo WebXR]:::threejs
    Scene --> Models[Carga de Modelos 3D / Materiales]:::threejs
    
    UI --> Auth[Autenticación]:::backend
    UI --> API[Supabase Client]:::backend
    
    API --> DB[(Base de Datos PostgreSQL)]:::backend
    API --> Storage[(Almacenamiento de Diseños)]:::backend
    
    %% Despliegue
    UI -.-> Deploy[Deploy en IONOS]:::infra
```

---

## 📚 Estructura de Componentes

```text
Diseno3DGenerico/
├── src/                    # Lógica de aplicación React
├── public/                 # Assets 3D estáticos y texturas
├── supabase/               # Configuraciones y migraciones de DB
├── dist/                   # Build optimizado para producción
├── DEPLOY_IONOS.md         # Guía de integración continua y despliegue
└── TAREAS_PENDIENTES.md    # Roadmap de producto
```

---

## 🔐 Seguridad y Escalabilidad
1. **Zero-Trust Client:** El cliente de Supabase implementa RLS (Row Level Security) nativo en PostgreSQL para garantizar la privacidad de los diseños.
2. **Performance:** Bundle tree-shaking optimizado con Vite, manteniendo el tamaño gzipped por debajo de los 700 KB para tiempos de Time-To-Interactive (TTI) ultra rápidos.
