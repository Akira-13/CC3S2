# Práctica dirigida 1

## Ejercicio 1: Configuración inicial y clonación de repositorios

Se ejecutó el script y se verificó la inicialización del repositorio y la creación del directorio `repo_clonado` llamado `pd1-dummy-repo`.

![](../resources/img/PD1_E1_1.jpg)

![](../resources/img/PD1_E1_2.jpg)

## Ejercicio 2: Estructuración y commit inicial

Se verifica la estructura correcta del proyecto y la documentación.

![](../resources/img/PD1_E2_1.jpg)

![](../resources/img/PD1_E2_2.jpg)

Además, se revisó el historial de commits

![](../resources/img/PD1_E2_3.jpg)


## Ejercicio 3: Comparación de versiones y uso de stash

Primero se agregó un comentario a `main.py` que va a ser corregido.

![](../resources/img/PD1_E3_1.jpg)

Luego se usó la función de stash para experimentar con cambios no comiteados.

![](../resources/img/PD1_E3_2.jpg)

## Ejercicio 4: Inspección y análisis del historial

Salida de `git blame`:

![](../resources/img/PD1_E4_1.jpg)

El historial de ramas distingue claramente entre la rama principal y las ramas de arreglos o hotfixes. Esto permite distinguir entre los cambios significativos y las soluciones a errores claramente.

## Ejercicio 5: Deshacer cambios y reset

Primero, creo un commit revertible y lo revierto.

![](../resources/img/PD1_E5_1.jpg)

![](../resources/img/PD1_E5_2.jpg)

Luego, experimento con las opciones de `git reset --hard`, `--mixed` y `--soft`. Primero, con la opción de `git reset --soft`, la cual no revierte los cambios en el área de staging y los cambios en el índice de trabajo.

![](../resources/img/PD1_E5_3_1.jpg)

![](../resources/img/PD1_E5_3_2.jpg)

Luego, con `git reset --mixed`, los cambios del área de staging salen de esta.

![](../resources/img/PD1_E5_4_1.jpg)

![](../resources/img/PD1_E5_4_2.jpg)

Finalmente, con `git reset --hard`, todos los cambios, tanto en el área de staging como en el índice de trabajo, son revertidos al commit especificado.

![](../resources/img/PD1_E5_5_1.jpg)

![](../resources/img/PD1_E5_5_2.jpg)

## Ejercicio 6: Rebase interactivo y reflog

Preparando los commits y ejecutando `git rebase -i`.

![](../resources/img/PD1_E5_6_1.jpg)

![](../resources/img/PD1_E5_6_2.jpg)

![](../resources/img/PD1_E5_6_3.jpg)

Luego, ejecutando `git reflog`

![](../resources/img/PD1_E5_6_4.jpg)

## Ejercicio 7: Sincronización de repositorios

Agrego un repositorio personal al remoto del repositorio y verificando la conexión con `git ls-remote`.

![](../resources/img/PD1_E5_7_1.jpg)

## Ejercicio 8: Branching y merging avanzado

En este ejercicio, probé la funcionalidad de octopus merge con tres ramas: octopus/first, octopus/second y octopus/third.

Haciendo el octopus merge y mostrando el resultado de la fusión.

![](../resources/img/PD1_E5_8_1.jpg)

![](../resources/img/PD1_E5_8_2.jpg)

## Ejercicio 9: Localización de errores con bisect

Ejecutando `git bisect` en hotfix-2, pude delimitar el commit malo.

![](../resources/img/PD1_E5_9_1.jpg)

## Ejercicio 10: Submódulos y hooks

Primero, importo un submódulo desde el repositorio remoto de prueba que creé. Inmediatamente después, hago un commit de prueba para verificar los hooks.

![](../resources/img/PD1_E5_10_1.jpg)

## Ejercicio 11: Historial y worktrees

Pruebo la limpiza del historial usando `git filter-branch`. No pude implementar worktrees diferentes al de por defecto en este repositorio por conflictos con el worktree principal.

![](../resources/img/PD1_E5_11_1.jpg)

## Ejercicio 12: Árbol de Merkle

Ejecución de Merkle Trees y sus pruebas unitarias.

![](../resources/img/PD1_E5_12_1.jpg)
