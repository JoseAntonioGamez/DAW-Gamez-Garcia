---
title: UD2 — Conexiones Remotas
nav_order: 3
---

# UD2 · Conexiones remotas para la administración de servidores web

**Módulo:** Despliegue de Aplicaciones Web · CFGS DAW 2025-2026  
**Integrantes que han trabajado esta unidad:** 
- Marcos García 
- José Antonio Gámez
- Carlos Robles

---

## Contenidos principales

SSH, SCP, SFTP, gestión de claves públicas/privadas, túneles SSH y administración remota de servidores.

---

## 📂 Índice de contenidos

### 📖 Materiales del profesor (`/material-teorico`)

| Fichero | Descripción |
|---------|-------------|
| [introduccion-al-servicio-ssh.pdf](material-teorico/introduccion-al-servicio-ssh.pdf) | Introducción al servicio SSH |
| [servicio-ssh.pdf](material-teorico/servicio-ssh.pdf) | El servicio SSH: conceptos y configuración |
| [configuracion-util-servidor-ssh.md](material-teorico/configuracion-util-servidor-ssh.md) | Guía de configuración útil del servidor SSH |
| [gestion-permisos-gnu-linux.pdf](material-teorico/gestion-permisos-gnu-linux.pdf) | Gestión de permisos en GNU/Linux |

---

### 📥 Entregas del grupo (`/entregas-alumnado`)

| Carpeta | Archivo | Tarea | Descripción |
|---------|---------|-------|-------------|
| practica-1-clientes-ssh-autenticacion-clave-publica | [Clientes SSH y Autenticación con clave pública](entregas-alumnado/practica-1-clientes-ssh-autenticacion-clave-publica/clientes-ssh-y-autenticacion-con-clave-publica.pdf) | Práctica 1 | Guía práctica para configurar conexión SSH cliente-servidor usando autenticación mediante clave pública en contenedores Linux. |
| practica-2-instalacion-configuracion-servicio-ssh | [Instalación y Configuración del servicio SSH](entregas-alumnado/practica-2-instalacion-configuracion-servicio-ssh/instalacion-y-configuracion-del-servicio-ssh.pdf) | Práctica 2 | Práctica completa de instalación, configuración y prueba del servicio SSH en instancias AWS y contenedores Docker, incluyendo transferencia de archivos y syslog. |
| practica-3-hardening-ssh-en-aws-ec2-ubuntu | [Hardening SSH en AWS EC2 con Ubuntu](entregas-alumnado/practica-3-hardening-ssh-en-aws-ec2-ubuntu/hardening-ssh-en-aws-ec2-con-ubuntu.pdf) | Práctica 3 | Documento sobre securización (“hardening”) de SSH en AWS EC2 con Ubuntu aplicando buenas prácticas de seguridad, auditoría y control de accesos.

---

### 📚 Documentos adicionales (`/recursos`)

| Fichero / Enlace | Descripción |
|------------------|-------------|
| [Clasificación De Tecnologías De Acceso Remoto](./recursos/Clasificación-tecnologías-acceso-remoto.pdf) | Guía clasificadora técnica que define los fundamentos, tipos y ventajas de diferentes tecnologias de acceso remoto fomentando su uso y al teletrabajo desde el COVID-19. |
| [Guía General de uso SSH](./recursos/Guia-de-uso-ssh.pdf) | Guía de la Universidad de santiago de compostela que explica el funcionamiento y uso del servicio SSH y sus posibles usos tecnicos. |
| [Guía de instalación OpenSSH](./recursos/Open-SSH.pdf) | Guía técnica para instalar el servidor OpenSSH en Ubuntu, configurar su puerto y aplicar medidas de seguridad como la desactivación del acceso para el usuario root. |

---

## 🔗 Referencias externas de interés

- [OpenSSH Manual](https://www.openssh.com/manual.html)
- [DigitalOcean: How to Set Up SSH Keys on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04)