# Enlace de instalación para PrestaShop 16.04
[Como instalar Prestashop 16.04](https://lite.evernote.com/note/cfb72f3d-51c1-4404-91a5-dc55205fab6e)

# Práctica 3
[Instalación y securización en AWS y Docker](./instalacion-securizacion-tres-cms-aws-docker.pdf)
---

# Comandos útiles para PrestaShop / Magento y MySQL

## Copiar archivos al servidor con SCP

Si te descargas el archivo de PrestaShop o Magento en tu equipo anfitrión:

```bash
scp -i "mi_certificado.pem" archivo_a_copiar.zip ubuntu@url_mi_servidor:/home/ubuntu
```

---

## Cambiar permisos de ficheros y directorios

### Permisos para archivos

```bash
find . -type f -exec chmod 644 -- {} +
```

### Permisos para directorios

```bash
find . -type d -exec chmod 755 -- {} +
```

---

## Cambiar el usuario propietario de forma recursiva

```bash
chown usuario-apache:usuario-apache -R /www/var/html
```

---

# Crear una base de datos directamente desde MySQL

Sin necesidad de usar phpMyAdmin.

## 1. Acceder a MySQL

```bash
mysql -u root -p
```

## 2. Crear la base de datos

```sql
CREATE DATABASE base_de_datos;
```

## 3. Mostrar las bases de datos

```sql
SHOW DATABASES;
```

## 4. Crear usuario y asignar permisos

```sql
GRANT USAGE ON mi_usuario.* TO base_de_datos@localhost IDENTIFIED BY 'contraseña_mi_usuario';
```

## 5. Aplicar cambios de privilegios

```sql
FLUSH PRIVILEGES;
```