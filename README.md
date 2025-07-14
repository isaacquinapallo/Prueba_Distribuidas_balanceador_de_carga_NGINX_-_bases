
# Proyecto: Aplicación Web con Flask + MySQL + NGINX + Docker

Este proyecto implementa una aplicación web para registrar currículums, usando:

- **Flask** como framework web.
- **MySQL** como base de datos (con replicación master-slave).
- **Nginx** como balanceador de carga.
- **Docker y Docker Compose** para orquestar todos los contenedores.
- **phpMyAdmin** para gestión visual de las bases de datos.

---

## Funcionalidades

- Registro de currículums: nombre, correo, experiencia, formación académica.
- 
  <img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/c1a2cbba-760f-487d-8315-dbc1bee570c5" />

  
- Alta disponibilidad y balanceo con Nginx.
  
  <img width="1365" height="717" alt="image" src="https://github.com/user-attachments/assets/89dfb58f-7320-42e4-bad9-838e33689b28" />

  <img width="1365" height="767" alt="image" src="https://github.com/user-attachments/assets/bc903aad-d6cd-44d2-ab72-070a21709e61" />


- Replicación MySQL master-slave configurada.

  MASTER

  <img width="1365" height="711" alt="image" src="https://github.com/user-attachments/assets/44e3fa08-2767-424e-9d47-2c9756782627" />

  
  SLAVE
  
  <img width="1365" height="740" alt="image" src="https://github.com/user-attachments/assets/8f9a6503-7561-4028-9534-4b23abe2e63e" />


---

## Estructura del proyecto

```
  servidor1/
  ├── app.py
  ├── templates/
       └── index.html
  └── Dockerfile
  servidor2/
  ├── app.py
  ├── templates/
       └── index.html
  └── Dockerfile
  mysql/
  ├── master.cnf
  └── slave.cnf
  nginx/
  └── nginx.conf
docker-compose.yml
```

---

## Configuración

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

## Ejecución

### 1️. Clona el repositorio

```bash
git clone https://github.com/tuusuario/tu-repo.git
cd tu-repo
```

### 2️. Construye y levanta los contenedores

```bash
docker compose up --build -d
```

---

### 3️. Accede a la aplicación

- **Formulario web**: [http://localhost/](http://localhost/)
- **phpMyAdmin**: [http://localhost:8081](http://localhost:8081)
  - Usuario: `root`
  - Password: `root`

---

## Configuración de la replicación

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
## Desarrollado por Isaac Quinapallo y Alejandro Gutierrez
