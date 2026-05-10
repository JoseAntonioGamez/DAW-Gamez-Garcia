# Configuración del servicio SSH (`sshd_config`)

## Introducción

El archivo `sshd_config` permite configurar el comportamiento del servicio SSH en un sistema Linux.  
En esta configuración se aplican distintas medidas relacionadas con:

- Seguridad
- Autenticación
- Restricción de usuarios
- Registro de eventos
- Reenvío gráfico X11

---

# Resumen de configuración

| Parámetro | Descripción | Valor |
|---|---|---|
| `Port` | Puerto utilizado por SSH | `22` |
| `Protocol` | Versión del protocolo SSH | `2` |
| `AllowUsers` | Usuarios permitidos | `alumnado` |
| `DenyUsers` | Usuarios denegados | `root` |
| `PermitRootLogin` | Permitir acceso root | `no` |
| `PubkeyAuthentication` | Autenticación por clave pública | `yes` |
| `PermitEmptyPasswords` | Permitir contraseñas vacías | `no` |
| `X11Forwarding` | Permitir aplicaciones gráficas remotas | `yes` |
| `LoginGraceTime` | Tiempo máximo para iniciar sesión | `120` |

---

# Archivo de configuración completo

```bash
# Puerto por el que escucha ssh
Port 22

# Dirección de escucha
#ListenAddress 0.0.0.0

# Protocolo que usa ssh
Protocol 2

# Rutas de claves privadas del servidor
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

# Cuando está activa esta opción permitirá que openssh ejecute 
# una cantidad pequeña de código como root
UsePrivilegeSeparation yes

# Especifica el tiempo en segundos que el servidor debe esperar  
# antes de generar automáticamente su clave
KeyRegenerationInterval 3600

# Especifica el número de bits a utilizar en la clave del servidor
ServerKeyBits 1024

# Especifica el código de instalación que se utilizará en la 
# conexión de mensajes sshd
SyslogFacility AUTH

# Especifica el nivel que se utiliza al registrar mensajes
LogLevel INFO

# Usuarios Permitidos
AllowUsers alumnado

# Usuarios Denegados
DenyUsers root

# Autenticación

# Indica el número de segundos en que la pantalla de login estará 
# disponible para que el usuario capture su nombre de usuario 
# y contraseña
LoginGraceTime 120

# Indica si el usuario Root tiene permiso entrar al servidor
PermitRootLogin no

# Opción especificada si ssh debe comprobar los permisos de usuario 
# en su directorio personal y archivos rhost antes 
# de aceptar
StrictModes yes

# Indica si la autenticación RSA está activada
RSAAuthentication yes

# Indica si está permitida la autenticación por clave pública
PubkeyAuthentication yes

# Ubicación predeterminada del archivo authorized_keys
#AuthorizedKeysFile %h/.ssh/authorized_keys

# Especifica si rhosts o archivos shosts no se deben utilizar 
# en la autenticación
IgnoreRhosts yes

# Autenticación por Rhost
RhostsRSAAuthentication no

# Autenticación por host y no por usuario
HostbasedAuthentication no

# Opción específica si el daemon ssh debe pasar por alto 
# el archivo known_hosts del usuario
#IgnoreUserKnownHosts yes

# Permitir contraseñas vacías
PermitEmptyPasswords no

# Controla el soporte para autenticación teclado-interactivo
ChallengeResponseAuthentication no

# Autenticación por contraseña
#PasswordAuthentication yes

# Kerberos
#KerberosAuthentication no
#KerberosGetAFSToken no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes

# GSSAPI

# Permitir autenticación basada en GSSAPI
#GSSAPIAuthentication no

# Reenviar credenciales al servidor
#GSSAPICleanupCredentials yes

# Permite ejecutar aplicaciones remotas
X11Forwarding yes

# Primer número de pantalla disponible para sshd
X11DisplayOffset 10

# Mostrar contenido de /etc/motd
PrintMotd no

# Mostrar último inicio de sesión
PrintLastLog yes

# Enviar mensajes TCP keepalive
TCPKeepAlive yes

# Utilizar login para sesiones interactivas
#UseLogin no

# Número máximo de conexiones simultáneas
#MaxStartups 10:30:60

# Mensaje de bienvenida
#Banner /etc/issue.net

# Permitir variables de entorno
AcceptEnv LANG LC_*

# Permitir autenticación por contraseña
#PasswordAuthentication no
```

---

# Medidas de seguridad aplicadas

## Restricción de usuarios

Se permite únicamente el acceso del usuario:

```bash
AllowUsers alumnado
```

Y se bloquea explícitamente el acceso del usuario root:

```bash
DenyUsers root
```

---

## Deshabilitar acceso root

```bash
PermitRootLogin no
```

Esta medida evita ataques directos contra la cuenta de administrador.

---

## Uso de autenticación por clave pública

```bash
PubkeyAuthentication yes
```

Permite utilizar claves SSH en lugar de contraseñas tradicionales.

---

## Bloqueo de contraseñas vacías

```bash
PermitEmptyPasswords no
```

Impide que usuarios sin contraseña puedan autenticarse.

---

## Uso del protocolo SSH versión 2

```bash
Protocol 2
```

La versión 2 del protocolo SSH es más segura y reemplaza completamente a SSHv1.

---

# Conclusión

La configuración aplicada mejora la seguridad del servicio SSH mediante:

- Restricción de acceso
- Deshabilitación del acceso root
- Uso de autenticación segura
- Configuración de logs
- Control de sesiones remotas

Estas medidas ayudan a proteger el servidor frente a accesos no autorizados y ataques comunes.