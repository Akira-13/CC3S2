# Informe de la actividad 7

## Ejercicio 1

### steps.py

Solo se debe adaptar la exprexión regular y calcular los segundos con las funciones dadas. Toda la expresión la muevo a una nueva función.

```python
def convert_description_to_hours(time_description):
    time_description = time_description.strip('"').lower()
    time_description = time_description.replace("y", " ")
    time_description = time_description.strip()

    if time_description == "media hora":
        total_time_in_hours = 0.5
    else:
        pattern = re.compile(
            r"(?:(\w+)\s*horas?)?\s*(?:(\w+)\s*minutos?)?\s*(?:(\w+)\s*segundos?)?"
        )
        match = pattern.match(time_description)

        if match:
            hours_word = match.group(1) or "0"
            minutes_word = match.group(2) or "0"
            seconds_word = match.group(3) or "0"

            hours = convertir_palabra_a_numero(hours_word)
            minutes = convertir_palabra_a_numero(minutes_word)
            seconds = convertir_palabra_a_numero(seconds_word)

            total_time_in_hours = hours + (minutes / 60) + (seconds / 3600)
        else:
            raise ValueError(
                f"No se pudo interpretar la descripción del tiempo: {time_description}"
            )
    return total_time_in_hours
```

### belly.feature

Implemento un escenario en belly.feature que use segundos.

```gherkin
  Escenario: Comer pepinos y esperar en horas, segundos y minutos
    Dado que he comido 25 pepinos
    Cuando espero "una hora, treinta minutos y veinte segundos"
    Entonces mi estómago debería gruñir
```

### Pytest

Implemento algunas pruebas básicas para verificar que se interpreten segundos adecuadamente. Las pruebas son superadas sin problema.

```python
def test_convert_seconds_to_hours():
    """Test that seconds are converted properly into hours."""
    assert convert_description_to_hours("3600 segundos") == 1
    assert convert_description_to_hours("1 hora y 3600 segundos") == 2
```

### Flujo CI

Implemento un archivo `ci.yml` para correr automáticamente pytest y behave.

```yml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'  
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
      - name: Run Behave
        run: behave
      - name: Run Pytest
        run: pytest
```

## Ejercicio 2: Manejo de cantidades fraccionarias de pepinos

### steps.py

Solo es necesario cambiar el paso given para que acepte cadenas y convertirlo a float posteriormente.

```python
@given("que he comido {cukes} pepinos")
def step_given_eaten_cukes(context, cukes):
    context.belly.comer(float(cukes))
```

### belly.features

Agrego un escenario adicional para pepinillos fraccionarios.

```gherkins
  Escenario: Comer pepinos fraccionarios
    Dado que he comido 25.5 pepinos
    Cuando espero "una hora, treinta minutos y veinte segundos"
    Entonces mi estómago no debería gruñir
```

### belly.py

Ahora se levanta un error cuando se intentan comer pepinillos "negativos".

```python
def comer(self, pepinos):
    if pepinos < 0:
        raise ValueError(f"Valor de pepinos {pepinos} negativo.")
    self.pepinos_comidos += pepinos
```

### test_belly.py

Agrego verificaciones para pepinillos fraccionarios y negativos.

```python
def test_negative_pickels():
    bell = Belly()
    with pytest.raises(ValueError):
        bell.comer(-5)


def test_fractional_pickels():
    bell = Belly()
    bell.comer(1.5)
    assert bell.pepinos_comidos == 1.5
```

### Validación de pruebas

![](../resources/img/A7_6.jpg)

![](../resources/img/A7_7.jpg)

### Entorno DevOps

En caso de que la lógica falle y se acepten pepinillos negativos en Belly, la prueba de pytest fallará y retornará una "falla de build".

## Ejercicio 3: Soporte para idiomas múltiples (Español e Inglés)

### steps.py

Modifiqué steps.py para que manejara dos expresiones regulares: una para inglés y otra para español. Dependiendo de en cuál haya un match, se procesan las palabras en inglés o en español

```python
spanish_pattern = re.compile(
    r"(?:(\w+)\s*horas?)?\s*(?:(\w+)\s*minutos?)?\s*(?:(\w+)\s*segundos?)?"
)
english_pattern = re.compile(
    r"(?:(\w+)\s*hours?)?\s*(?:(\w+)\s*minutes?)?\s*(?:(\w+)\s*seconds?)?"
)
```

### belly.features

Se agregó tres escenarios en Gherkin en inglés

```gherkin
  Escenario: Esperar usando horas en inglés 1
    Dado que he comido 20 pepinos
    Cuando espero "two hours and thirty minutes"
    Entonces mi estómago no debería gruñir

  Escenario: Esperar usando horas en inglés 2
    Dado que he comido 50 pepinos
    Cuando espero "an hour"
    Entonces mi estómago no debería gruñir

  Escenario: Esperar usando horas en inglés 3
    Dado que he comido 30 pepinos
    Cuando espero "an hour, twenty minutes and thirty seconds"
    Entonces mi estómago no debería gruñir
```

