{: .no_toc }
# Sistemas de Control de Versiones (VCS) y Plataformas de Alojamiento

Este documento recopila las principales herramientas de control de versiones y plataformas de alojamiento de código utilizadas en la industria del desarrollo de software.

---

## 1. Motores de Control de Versiones (VCS)
Estos son los sistemas de software principales encargados de registrar y gestionar los cambios en los archivos de código fuente.

### Sistemas Distribuidos (DVCS)
*   **Git:** El estándar absoluto de la industria. Cada desarrollador tiene una copia completa del repositorio en su máquina local.
*   **Mercurial (Hg):** Sistema distribuido similar a Git, caracterizado por una curva de aprendizaje más suave y comandos más simples.
*   **Bazaar:** Una alternativa distribuida diseñada por Canonical para ser intuitiva y fácil de usar.
*   **Fossil:** Sistema distribuido único que incluye control de versiones, wiki, chat y seguimiento de errores en un solo ejecutable.

### Sistemas Centralizados (CVCS)
*   **Subversion (SVN):** El sistema centralizado más popular. Todo el historial se almacena en un único servidor central.
*   **Perforce Helix Core:** Extremadamente escalable. Es el estándar de oro en la industria de videojuegos y cine para manejar archivos binarios de gran tamaño.
*   **TFVC (Team Foundation Version Control):** Sistema centralizado integrado de forma nativa en entornos Microsoft Azure DevOps.
*   **CVS (Concurrent Versions System):** Uno de los pioneros históricos. Actualmente obsoleto y en desuso.

---

## 2. Plataformas de Alojamiento y Colaboración (Git-Based)
Interfaces web y servicios en la nube que toman el motor de Git y añaden herramientas de colaboración, revisión de código y automatización.

*   **GitHub:** La plataforma más grande del mundo. Destaca por su comunidad, herramientas sociales y automatización con GitHub Actions.
*   **GitLab:** Plataforma orientada a flujos DevOps completos. Ofrece pipelines integrados de CI/CD potentes tanto en la nube como autoalojados.
*   **Bitbucket:** La opción preferida de empresas que utilizan el ecosistema de Atlassian gracias a su integración nativa con Jira y Confluence.
*   **Gitea / Gogs:** Soluciones ultraligeras de código abierto ideales para servidores locales o dispositivos de bajos recursos (como una Raspberry Pi).
*   **AWS CodeCommit:** Servicio de repositorios privados gestionado de forma nativa y segura dentro de la infraestructura de Amazon Web Services.
*   **SourceHut:** Una plataforma minimalista y rápida orientada a desarrolladores que prefieren flujos de trabajo basados en correo electrónico y sin JavaScript.

---

## 3. Estado del Mercado
*   **Dominio de Git:** Git y sus plataformas asociadas concentran más del 85% de la cuota del mercado global de desarrollo de software.
*   **Migración Tecnológica:** Los sistemas centralizados como SVN se mantienen principalmente en proyectos legados o corporaciones con infraestructuras antiguas.
*   **Enfoque DevOps:** El valor actual de las plataformas ya no es solo guardar el código, sino su capacidad para probarlo, compilarlo y desplegarlo automáticamente (CI/CD).
