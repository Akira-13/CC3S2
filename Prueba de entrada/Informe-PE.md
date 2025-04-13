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

## Día 3 - Implementación de la clase Quiz y flujo básico del juego (Sprint 1 – Parte 3)

Primero, corregí algunos errores en los docstrings de la clase Question e implementé un docstring general para el módulo. Con este nuevo commit empecé a trabajar en la clase Quiz.

### Clase Quiz

Implementé la clase Quiz junto al método `run_quiz()` para mostrar las preguntas en consola.

```python
class Quiz:
    """
    Class that manages the game flow.

    ...

    Attributes
    ----------
    questions: list[Question]
        List of Question instances to be displayed during the game.
    current_question_index: int
        The index of the current question shown in the game.

    Methods
    -------
    add_question(question : Question)
        Add a question to the Quiz question list.
    get_next_question()
        Return the question next to the current index.
    run_quiz()
        Show each question and expects player input.
    """

    def __init__(self):
        """Initialize empty Quiz instance.

        Parameters
        ----------
        questions: list[Question], default []
            An empty list of questions.
        current_question_index: int, default 0
            The index of the current question displayed in the game.
        """
        self.questions = []
        self.current_question_index = 0

    def add_question(self, question):
        """Add question to the end of the question list.

        Parameters
        ----------
        question: Question
            Question to be added at the end of the list.

        Raises
        ------
        TypeError
            If the question is not instance of Question class.
        """
        if not isinstance(question, Question):
            raise TypeError("Question is not instance of Question class!")
        self.questions.append(question)

    def get_next_question(self):
        """Get next question to the index if it exists.

        Returns
        -------
        question
            Next question in the list.
        None
            If method is called from last question in list.
        """
        if self.current_question_index < len(self.questions):
            question = self.questions[self.current_question_index]
            self.current_question_index += 1
            return question
        return None

    def run_quiz(self):
        """Run quiz game.

        Show each question and its options properly formatted
        and expects player input as their answer. If the answer
        belongs to options, shows right or wrong message and
        displays next question. Otherwise, shows the same question
        again.
        """
        welcome = (
            "Bienvenido al juego de trivia!\nResponde las siguientes "
            "preguntas seleccionando el número de la opción correcta."
        )
        print(welcome)
        question = self.questions[self.current_question_index]
        while question is not None:
            print(
                (
                    f"Pregunta {self.current_question_index + 1}: "
                    "f{question.description}"
                )
            )

            option_list = zip(question.options, range(len(question.options)))
            for option, no in option_list:
                print(f"{no+1}) {option}")
            print("Tu respuesta: ")
            answer_index = int(input())
            if answer_index not in range(len(question.options)):
                print("¡Respuesta inválida!")
                continue

            player_answer = question.options[answer_index]
            if question.is_correct(player_answer):
                print("¡Correcto!")
            else:
                print("¡Incorrecto!")

            question = self.get_next_question()
```

### Nueva rama y commit

Se creó la rama "feature/estructura-basic" y se hizo commit con la nueva clase.

![Commit en el día 3](../resources/img/PE_commit_3.jpg)

### Registro diario

#### Diff en trivia.py

![diff en el día 3](../resources/img/PE_diff_3_1.jpg)

#### Blame en trivia.py

![blame en el día 3](../resources/img/PE_blame_3_1.jpg)

## Día 4 - Sistema de puntuación, manejo de rondas y finalización del juego (Sprint 2)

### Implementación de atributos

Se implementaron los atributos `correct_answers`, `incorrect_answers` y `rounds`. Los dos primeros llevan un conteo del número de preguntas respondidas correcta e incorrectamente. El último es un valor entero que limita el número de preguntas a mostrar en el juego. 

### Implementación del método `anser_question`

```python
    def answer_question(self, question, answer):
        """Check if answer is correct to question.

        Compares the given answer to a question's correct option,
        updates correct or incorrect answer counter, and returns
        True or False.

        Parameters
        ----------
        question: Question
            A Question instance with a correct option.
        answer: str
            The answer to be compared to the correct option.

        Returns
        -------
        True
            If answer to question is correct.
        False
            If answer to question is incorrect.
        """
        if question.is_correct(answer):
            self.correct_answers += 1
            return True
        else:
            self.incorrect_answers += 1
            return False
```

### Manejo de rondas

Para el manejo de rondas, solo se agregó un bucle `for` a las preguntas mostradas, además de un mensaje final al terminar las rondas.

```python
        question = self.get_next_question()
        for _ in range(self.rounds):
            if question is None:
                break
            print(
                (
                    f"Pregunta {self.current_question_index + 1}: "
                    "f{question.description}"
                )
            )

.......

        print("Juego terminado. Aquí esta tu puntuación:")
        print(f"Preguntas contestadas: {self.rounds}")
        print(f"Respuestas correctas: {self.correct_answers}")
        print(f"Respuestas incorrectas: {self.incorrect_answers}")
```

### Pruebas unitarias

Se implementaron pruebas unitarias para los contadores de puntuación, la función `get_next_question` y el caso límite en el que se quiera agregar alguna variable que no sea instance de Question a la lista de preguntas.

```python
def test_quiz_scoring():
    """
    Test that correct and incorrect questions are properly validated.
    """
    quiz = Quiz()
    question = Question("What is 2 + 2?", ["1", "2", "3", "4"], "4")
    quiz.add_question(question)
    assert quiz.answer_question(question, "4") is True
    assert quiz.correct_answers == 1
    assert quiz.answer_question(question, "2") is False
    assert quiz.incorrect_answers == 1


def test_add_question_not_instace_of_Question():
    """
    Test that adding anything but an instance of Question raises TypeError.
    """
    quiz = Quiz()
    question = "I raise an error in Quiz!"
    with pytest.raises(TypeError):
        quiz.add_question(question)


def test_next_questions():
    """
    Test that getting the next question works up until no questions are left.
    """
    first_question = Question("What is 2 + 2?", ["1", "2", "3", "4"], "4")
    second_question = Question("What is 4 * 0?", ["4", "1", "0", "-1"], "0")
    quiz = Quiz()
    quiz.add_question(first_question)
    quiz.add_question(second_question)
    assert quiz.get_next_question() is first_question
    assert quiz.get_next_question() is second_question
    assert quiz.get_next_question() is None
```

#### Ejecución de pruebas

![Pruebas para Quiz](../resources/img/PE_test_4.jpg)

### Commit

Con la clase y pruebas hechas, hice commit en la rama feature/estructura-basic. Esto se puede ver al ejecutar `git-blame`.

### Registro diario

#### Blame en la clase Quiz

![Blame que muestra implementación de método answer_question](../resources/img/PE_blame_4_3.jpg)

![Blame que muestra documentación extendida de clase Quiz](../resources/img/PE_blame_4_2.jpg)

![Blame que muestra implementación de lógica de rondas](../resources/img/PE_blame_4_1.jpg)

## Día 5 - Mejoras en la interfaz de usuario y refinamientos (Sprint 3)

### Mejorar la lógica en run_quiz()

Mejoré la lógica en `run_quiz()` y agregué una función `load_questions()` para cargar preguntas por defecto en un archivo .json. También agregué la posibilidad de elegir la dificultad de estas preguntas.

```python
def run_quiz(self):

...

        # If no questions have been loaded, load default questions by
        # asking player difficulty.
        if not self.questions:
            diff = ""
            while True:
                diff = input(
                    "¿En qué dificultad le gustaría jugar?\n"
                    "1) Fácil\n"
                    "2) Difícil\n"
                    "Escriba 1 o 2: "
                ).lower()
                if diff == "1" or diff == "2":
                    break
                print("¡Opción inválida!")
            self.load_questions(difficulty=diff)
```

```python
    def load_questions(self, path="DEFAULT", difficulty="1"):
        """Load questions from file.

        Loads the questions from a given file path in JSON format. If
        only difficulty is given, it loads default questions from file.

        Parameters
        ----------
        path: str, default "DEFAULT"
            A file path with questions in JSON format.
        difficulty: str, default "1"
            Difficulty to distingish between easy and hard default questions.
        """
        if path == "DEFAULT" and difficulty == "1":
            with open("./default_questions.json", "r") as file:
                data = json.load(file)
                for q in data.get("easy_questions", []):
                    self.add_question(
                        Question(q["description"], q["options"], q["correct_answer"])
                    )
        elif path == "DEFAULT" and difficulty == "2":
            with open("./default_questions.json", "r") as file:
                data = json.load(file)
                for q in data.get("hard_questions", []):

                    self.add_question(
                        Question(q["description"], q["options"], q["correct_answer"])
                    )
        else:
            with open(path, "r") as file:
                data = json.load(file)
                for q in data.get("questions", []):
                    self.add_question(
                        Question(q["description"], q["options"], q["correct_answer"])
                    )
```

### Probando la interfaz de juego

![Interfaz de juego en prueba 1](../resources/img/PE_game_flow_1.jpg)

![Interfaz de juego en prueba 2](../resources/img/PE_game_flow_2.jpg)

### Blame y Diff

Después de hacer commit con estos cambios en la rama "feature/ui-improvements", puedo revisar qué cambios se tienen.

#### Diff 

![Diff en el día 5 - 1](../resources/img/PE_diff_5_1.jpg)

![Diff en el día 5 - 2](../resources/img/PE_diff_5_2.jpg)

#### Blame

![Blame en el día 5 - 1](../resources/img/PE_blame_5_1.jpg)

![Blame en el día 5 - 2](../resources/img/PE_blame_5_2.jpg)

![Blame en el día 5 - 3](../resources/img/PE_blame_5_3.jpg)

#### Fusionar en la rama develop

Con el juego principal terminado, puedo fusionar en la rama principal `develop`.

![Merge a la rama develop](../resources/img/PE_merge_5_1.jpg)
