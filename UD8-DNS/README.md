---
title: UD8 — DNS
nav_order: 8
has_children: true
---
 
# UD8 · Servicios de nombre de dominio (DNS)
 
**Módulo:** Despliegue de Aplicaciones Web · CFGS DAW 2025-2026  
**Integrantes que han trabajado esta unidad:** 
- Marcos García 
- José Antonio Gámez
- Carlos Robles

---
 
## Contenidos principales
 
DNS, registros (A, CNAME, MX...), BIND9, LDAP, OpenLDAP, resolución de nombres, TTL y DNS inverso.
 
---
 
## 📖 Material teórico (`/material-teorico`)
 
| Fichero | Descripción |
|---------|-------------|
| [daw-05.pdf](material-teorico/daw-05.pdf) | Apuntes del módulo — DAW 05 |
| [index.md](material-teorico/index.md) | Referencias del material teórico de la unidad |
| [instalacion-configuracion-ldap-openldap.pdf](material-teorico/instalacion-configuracion-ldap-openldap.pdf) | Instalación y configuración de LDAP con OpenLDAP |
| [instalacion-servidor-dns-bind9.pdf](material-teorico/instalacion-servidor-dns-bind9.pdf) | Instalación de un servidor DNS con BIND9 |
| [servicio-dns.pdf](material-teorico/servicio-dns.pdf) | El servicio DNS: conceptos y funcionamiento |
| [servicio-ldap.pdf](material-teorico/servicio-ldap.pdf) | El servicio LDAP: conceptos y arquitectura |
| [servidor-dns-sencillo-dnsmasq.pdf](material-teorico/servidor-dns-sencillo-dnsmasq.pdf) | Configuración de un servidor DNS sencillo con Dnsmasq |
| [servidor-dns.pdf](material-teorico/servidor-dns.pdf) | Administración de servidores DNS |
| [wikipedia-sistema-nombres-dominio.pdf](material-teorico/wikipedia-sistema-nombres-dominio.pdf) | Referencia Wikipedia sobre el Sistema de Nombres de Dominio |
 
---
 
## 📥 Entregas del grupo (`/entregas-alumnado`)
 
| Tarea | Archivo | Descripción |
|-------|---------|-------------|
| Práctica 1 | [Instalación BIND9](entregas-alumnado/practica-1-instalacion-bind9/instalacion-bind9.pdf) | Proceso de instalación y configuración del servidor DNS BIND9 en una instancia de AWS Ubuntu, que incluye la definición de zonas para el dominio "marcos.com", la creación de registros tipo A y CNAME, y la verificación del funcionamiento mediante el comando dig. |
 
---

### 📚 Documentos adicionales (`/recursos`)

| Fichero / Enlace | Descripción |
|------------------|-------------|
| [Guía Didáctica de DNS](./recursos/guia-dns.pdf) | Guía didáctica que explica el funcionamiento del sistema de nombres de dominio (DNS), detallando cómo se traducen los nombres de host en direcciones IP a través de una estructura jerárquica de servidores. |
| [Guía Buenas Practicas DNS](./recursos/) | Guía técnica del INCIBE detalla el funcionamiento y los pasos para la implementación de las extensiones de seguridad DNSSEC con el fin de garantizar la integridad y autenticidad de las consultas en el sistema de nombres de dominio. |

---
 
## 🔗 Referencias externas de interés
 
- [Documentación de BIND9](https://bind9.readthedocs.io/)
- [Cloudflare: ¿Qué es el DNS?](https://www.cloudflare.com/es-es/learning/dns/what-is-dns/)
- [MXToolbox — DNS Lookup](https://mxtoolbox.com/DNSLookup.aspx)