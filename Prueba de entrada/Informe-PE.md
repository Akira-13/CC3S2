# Informe de la prueba de entrada

## Enlace del repositorio del proyecto

https://github.com/Akira-13/PE-CC3S2

## Día 1 - Configuración del entorno y estructura básica (Sprint 1 – Parte 1)

### Creación del proyecto

Después de ejecutar los comandos para la creación del ambiente virtual de Python, tengo el siguiente `pip list`

![Creación de ambiente virtual](../resources/img/PE_project_creation.jpg)

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

![Creación del repositorio](../resources/img/PE_repo_creation.jpg)

### Registro diario

No es necesario ejecutar `git diff` para el primer commit. Solo muestro el resultado de `git blame` en el primer día para los archivos más importantes.

#### Blame en compose.yml

![Blame en compose.yml](../resources/img/PE_blame_1_2.jpg)

#### Blame en Dockerfile

![Blame en Dockerfile](../resources/img/PE_blame_1_1.jpg)

## Día 2 - Implementación de la clase Question y pruebas unitarias (Sprint 1 – Parte 2)

### Clase Question

Primero, implementé la clase `Question` con la documentación necesaria. Además, agregué un chequeo en el constructor de la clase para verificar que la respuesta correcta se encuentre en la lista de opciones. Caso contrario, se lanza un error.

```python
class Question:
    """
    Class that represents a question to be displayed in the game.

    ...

    Attributes
    ----------
    description : str
        String representing the question content
    options : list[str]
        List of strings representing the answer choices for the question
    correct_answer : str
        Correct answer included in the options list

    Methods
    -------
    is_correct(answer : str)
        Compares the provided answer with the stored correct answer

    """

    def __init__(self, description, options, correct_answer):
        """
        Initializes a question with the given parameters.
        Parameters
        ----------
        description : str
            String representing the question content
        options : list[str]
            Array of strings representing the answer choices for the question
        correct_answer : int
            Correct answer included in the options list

        Raises
        -------
        Value error
            If the correct answer is not in option
        """
        self.description = description
        self.options = options
        self.correct_answer = correct_answer
        if correct_answer not in options:
            raise ValueError(
                f"Correct answer {correct_answer} is not in the options list!"
            )

    def is_correct(self, answer):
        """Compares the provided answer with the stored correct answer"""
        return self.correct_answer == answer
```

### Pruebas unitarias para Question

Documenté las pruebas unitarias provistas en las instrucciones y agregué una prueba que verifica que se lanza un error `ValueError` cuando la respuesta no se encuentra en las opciones de la pregunta.

```python
import pytest
from trivia import Question


def test_question_answer_not_in_options():
    """
    Test that Question raises ValueError if correct_answer is not in options
    list
    """
    with pytest.raises(ValueError):
        question = Question("What is 2 + 2?", ["1", "2", "3", "4"], "5")


def test_question_correct_answer():
    """
    Test that is_correct() returns True when the given answers match.
    """
    question = Question("What is 2 + 2?", ["1", "2", "3", "4"], "4")
    assert question.is_correct("4")


def test_question_incorrect_answer():
    """
    Test that is_correct() returns False when the given answers don't match.
    """
    question = Question("What is 2 + 2?", ["1", "2", "3", "4"], "4")
    assert not question.is_correct("2")
```

#### Ejecución de pruebas

![Ejecución de pruebas de la clase Question](../resources/img/PE_question_tests.jpg)

### Commit

![Commit del segundo día](../resources/img/PE_commit_d2.jpg)


### Registro diario

No modifiqué ninguno de los archivos existentes, solo creé dos nuevos archivos. Por lo tanto, solo mostraré los resultados de `git blame`.

#### Blame en trivia.py

![Blame en trivia.py](../resources/img/PE_blame_2_1.jpg)

#### Blame en test_trivia.py

![Blame en test_trivia.py](../resources/img/PE_blame_2_2.jpg)
