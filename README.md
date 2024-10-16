# baronette-db
**Base de datos utilizada para la aplicación baronette-webapp.**
Este documento proporciona instrucciones para instalar y configurar el servicio PostgreSQL en un contenedor Docker, así como para crear las tablas necesarias en la base de datos.

## Instalación del servicio PostgreSQL en Docker
1. **Acceder al repositorio**: Clona este repositorio en tu máquina local o navega a su ubicación.

2. **Ejecutar el contenedor**:
    ```bash
    docker compose up -d
    ```

## Conectarse al contenedor:
1. **Buscar el id del contenedor**:
    ```bash
    docker ps
    ```
    Este comando te mostrará la lista de contenedores en ejecución. Busca el contenedor de PostgreSQL y copia su ID.
2. Entrar a la terminal interactiva del contenedor:
    ```bash
    docker exec -it {container_id} bash
    ```
    Reemplaza {container_id} con el ID del contenedor que encontraste en el paso anterior.
3. Conectarse con el superusuario postgres:
    ```bash
    psql -U postgres
    ```
    Esto abrirá la consola de PostgreSQL como el usuario superadministrador.

## Crear la base de datos:
```sql
CREATE DATABASE baronette_data_api WITH TEMPLATE = template0;
```
Este comando creará una nueva base de datos llamada baronette_data_api utilizando template0.

## Añadir las tablas al esquema public:
Ejecutar el script para crear las extensiones y tablas: El siguiente script se encuentra en init/init.sql en este repositorio. Asegúrate de ejecutarlo en la consola de PostgreSQL.
```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE public.user_list (
    id uuid DEFAULT uuid_generate_v4() NOT NULL,
    first_name varchar NULL,
    last_name_1 varchar NULL,
    last_name_2 varchar NULL,
    username varchar NOT NULL,
    user_password varchar NOT NULL,
    email varchar NOT NULL,
    verified bool DEFAULT false,
    CONSTRAINT user_pk PRIMARY KEY (id),
    CONSTRAINT user_username UNIQUE (username),
    CONSTRAINT user_email UNIQUE (email)
);

CREATE TABLE public.admin_list (
	admin_id uuid DEFAULT uuid_generate_v4() NOT NULL,
	user_id uuid NULL,
	CONSTRAINT admin_list_pk PRIMARY KEY (admin_id),
	CONSTRAINT admin_list_user_id UNIQUE (user_id),
	CONSTRAINT admin_list_user_fk FOREIGN KEY (user_id) REFERENCES public.user_list(id)
);
```

### Descripción de las tablas
**user_list**:
Esta tabla almacena información sobre los usuarios, incluyendo nombres, correos electrónicos,y si están verificados o no.
- id: UUID único generado automáticamente.
- username: Nombre de usuario único para cada usuario.
- email: Correo electrónico único de cada usuario.
- verified: Estado de verificación del usuario (por defecto es false).

**admin_list**:
Esta tabla almacena información sobre los administradores y su relación con los usuarios.
- admin_id: UUID único para cada administrador.
- user_id: Relación con el usuario correspondiente en user_list.