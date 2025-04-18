
# Tabla de contenidos

1.  [Parte 1: git rebase para mantener un historial lineal](#org5f48d9b)
    1.  [Rama inicial](#org983eb56)
    2.  [Rama new-feature](#org6305f88)
    3.  [git rebase y merge](#orgf90143a)
        1.  [Rebase](#org4240aba)
        2.  [Merge](#orgcaa07b6)
2.  [Parte 2: git cherry-pick para la integración selectiva de commit](#orgf440f23)
    1.  [Diagrama de ramas](#orgf658abf)
    2.  [Cherry-pick](#orgc4f83e7)



<a id="org5f48d9b"></a>

# Parte 1: git rebase para mantener un historial lineal


<a id="org983eb56"></a>

## Rama inicial

Después de crear el repositorio `prueba-git-rebase` y realizar los commits correspondientes, así se ve el gráfico en la rama `main`.

![Gráfico de la primera rama del primer repositorio](../resources/img/A6_P1_1.jpg)


<a id="org6305f88"></a>

## Rama new-feature

![Gráfico de la nueva rama en el primer repositorio](../resources/img/A6_P1_2.jpg)


<a id="orgf90143a"></a>

## git rebase y merge


<a id="org4240aba"></a>

### Rebase

![Gráfico después de rebase en el primer repositorio](../resources/img/A6_P1_3.jpg)


<a id="orgcaa07b6"></a>

### Merge

![Gráfico después de merge en el primer repositorio](../resources/img/A6_P1_4.jpg)


<a id="orgf440f23"></a>

# Parte 2: git cherry-pick para la integración selectiva de commit

Después de la inicialización del segundo repositorio y creación de la rama `add-base-documents`, así se ve el gráfico del repositorio:

![Gráfico inicial del segundo repositorio](../resources/img/A6_P2_1.jpg)


<a id="orgf658abf"></a>

## Diagrama de ramas

![Diagrama de ramas](../resources/img/A6_P2_2.jpg)


<a id="orgc4f83e7"></a>

## Cherry-pick

Realizando un cherry pick en el commit `db5e7b7` a la rama principal, se tiene el siguiente gráfico:

![Gráfico del segundo repositorio tras cherry-pick](../resources/img/A6_P2_3.jpg)

Este nuevo commit tiene un hash diferente al de &ldquo;Se agrega CONTRIBUTING.md&rdquo; de la rama `add-base-documents`.

