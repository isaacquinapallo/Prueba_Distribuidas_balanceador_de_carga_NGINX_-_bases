
# 🐳 Proyecto: Aplicación Web con Flask + MySQL + NGINX + Docker

Este proyecto implementa una aplicación web para registrar currículums, usando:

- **Flask** como framework web.
- **MySQL** como base de datos (con replicación master-slave).
- **Nginx** como balanceador de carga.
- **Docker y Docker Compose** para orquestar todos los contenedores.
- **phpMyAdmin** para gestión visual de las bases de datos.

---

## 🚀 Funcionalidades

- Registro de currículums: nombre, correo, experiencia, formación académica.
- Alta disponibilidad y balanceo con Nginx.
- Replicación MySQL master-slave configurada.
- Administración fácil con phpMyAdmin.

---

## 🗂️ Estructura del proyecto

```
📁 servidor1/
  ├── app.py
  ├── templates/
       └── index.html
  └── Dockerfile
📁 servidor2/
  ├── app.py
  ├── templates/
       └── index.html
  └── Dockerfile
📁 mysql/
  ├── master.cnf
  └── slave.cnf
📁 nginx/
  └── nginx.conf
docker-compose.yml
```

---

## ⚙️ Configuración

### Variables importantes en `docker-compose.yml`

```yaml
MYSQL_ROOT_PASSWORD: root
MYSQL_DATABASE: db_informacion
```

El archivo `master.cnf` configura el servidor maestro con:

```ini
[mysqld]
server-id=1
log-bin=mysql-bin
binlog-do-db=db_informacion
```

El archivo `slave.cnf` configura el servidor esclavo con:

```ini
[mysqld]
server-id=2
relay-log=relay-log
log-bin=mysql-bin
binlog-do-db=db_informacion
```

---

## 🏃‍♂️ Ejecución

### 1️⃣ Clona el repositorio

```bash
git clone https://github.com/tuusuario/tu-repo.git
cd tu-repo
```

### 2️⃣ Construye y levanta los contenedores

```bash
docker compose up --build -d
```

---

### 3️⃣ Accede a la aplicación

- 🌐 **Formulario web**: [http://localhost/](http://localhost/)
- ⚙️ **phpMyAdmin**: [http://localhost:8081](http://localhost:8081)
  - Usuario: `root`
  - Password: `root`

---

## 🐬 Configuración de la replicación

Para configurar la replicación manualmente:

1. Conéctate al contenedor maestro:

```bash
docker exec -it Maestro1 mysql -u root -p
```

2. Crea usuario de replicación:

```sql
CREATE USER 'replica'@'%' IDENTIFIED WITH mysql_native_password BY 'replica_pass';
GRANT REPLICATION SLAVE ON *.* TO 'replica'@'%';
FLUSH PRIVILEGES;
```

3. Verifica el log file y posición:

```sql
SHOW MASTER STATUS;
```

4. Conéctate al contenedor esclavo:

```bash
docker exec -it Maestro2 mysql -u root -p
```

5. Configura la replicación:

```sql
STOP REPLICA;
RESET REPLICA ALL;
CHANGE REPLICATION SOURCE TO
  SOURCE_HOST='maestro1',
  SOURCE_USER='replica',
  SOURCE_PASSWORD='replica_pass',
  SOURCE_LOG_FILE='binlog.00000X', -- cambia X por tu archivo
  SOURCE_LOG_POS=XXX;              -- cambia XXX por tu posición
START REPLICA;
```

6. Verifica estado:

```sql
SHOW REPLICA STATUS\G
```

---

## 💬 Contacto

Cualquier duda o mejora, abre un **issue** o envía un **pull request**.  
¡Gracias por contribuir! 🚀

---

## 📄 Licencia

MIT License.
