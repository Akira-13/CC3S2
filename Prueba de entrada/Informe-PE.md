# Informe de la prueba de entrada

## Enlace del repositorio del proyecto

https://github.com/Akira-13/PE-CC3S2

## Día 1 - Configuración del entorno y estructura básica (Sprint 1 – Parte 1)

### Creación del proyecto

Después de ejecutar los comandos para la creación del ambiente virtual de Python, tengo el siguiente `pip list`

![link text](../resources/img/PE_project_creation.jpg)

### Dockerfile y docker-compose.yml

Para este proyecto, el contenedor debe ser un ambiente para FastAPI y PostgreSQL. Para el primer servicio, se puede usar el Dockerfile provisto para [Python en dockerhub](https://hub.docker.com/_/python/), mientras que para [PostgreSQL](https://hub.docker.com/_/postgres/) se puede configurar en un `compose.yml`.

#### Dockerfile para FastAPI

``` dockerfile
FROM python:3

WORKDIR /app

COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD [ "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]

```

1. Establece el directorio de trabajo en `/app`.
2. Copia los requerimientos y los instala en el contenedor.
3. Copia los contenidos del directorio actual en el directorio de trabajo del contenedor.
4. Ejecuta una serie de comandos:
   - Ejecuta un servidor Uvicorn.
   - Busca al objeto `app` en `main.py` en el directorio `app`.
   - Expone el servidor en el puerto 8000, el puerto por defecto para Uvicorn.

#### compose.yml para el contenedor y PostgreSQL

``` yaml
services:
  web:
    build: .
    depends_on:
     - db
    ports:
     - "8000:8000"
    volumes:
     - .:/app

  db:
    image: postgres
    restart: always
    ports:
     - "5432:5432"
    environment:
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data/

volumes:
  postgres_data:
```

1. Se definen los servicios `web` y `db`
2. Se construye el contenedor en el directorio en el que se encuentra compose.yml
3. El puerto 8000 del host llevará al puerto 8000 en el contenedor.
4. El directorio actual será montado en el directorio /app del contenedor.
4. El servicio web depende de `db`, entonces `db` será levantado primero.
5. `db` importa la imagen de DockerHub de Postgres.
6. Si este sufre caídas, se reinicia automáticamente.
7. Mapea el puerto 5432 del contenedor al 5432 del host.
8. La contraseña será "password". El usuario por defecto será "postgres" y la base de datos por defecto "postgres".
9. Establece un volumen para almacenar datos persistentemente. Caso contrario, cuando el contenedor se reinicie, todos los datos se perderían.

Con estos archivos tengo listo el ambiente de desarrollo de la aplicación y puedo preparar el repositorio.

### Creación del repositorio

Como se pide en las instrucciones, creé una rama `develop` y otra `feature/estructura-inicial` con todos los archivos creados. Usé un archivo `.gitignore` para evitar cargar el ambiente virtual al repositorio.

![link text](../resources/img/PE_repo_creation.jpg)

### Registro diario

No es necesario ejecutar `git diff` para el primer commit. Solo muestro el resultado de `git blame` en el primer día para los archivos más importantes.

#### Blame en compose.yml

![link text](../resources/img/PE_blame_1_2.jpg)

#### Blame en Dockerfile

![link text](../resources/img/PE_blame_1_1.jpg)
