# Calculadora Interactiva del Método de Rigidez para Cerchas Planas

Calculadora web interactiva (HTML + CSS + JavaScript vanilla, **sin frameworks ni
dependencias externas**) para el análisis matricial de cerchas planas (2D)
mediante el método de rigidez directo. Todo el cálculo corre en el navegador
del usuario; no requiere backend, build step ni librerías de terceros.

Autor: **Anuar González A.**

## Archivo principal

- [`calculadora.html`](./calculadora.html): aplicación completa (marcado,
  estilos y lógica) en un único archivo autocontenido. Basta con abrirlo en
  cualquier navegador moderno.

## Método implementado

Para cada barra `(i, j)` de la cercha se calcula:

1. Longitud `L` y cosenos directores `c = (x_j - x_i)/L`, `s = (y_j - y_i)/L`.
2. Matriz de rigidez local axial:

   ```
   k_local = (E·A / L) · [[ 1, 0, -1, 0],
                          [ 0, 0,  0, 0],
                          [-1, 0,  1, 0],
                          [ 0, 0,  0, 0]]
   ```

3. Matriz de transformación (rotación) Beta:

   ```
   Beta = [[ c, s, 0, 0],
           [-s, c, 0, 0],
           [ 0, 0, c, s],
           [ 0, 0,-s, c]]
   ```

4. Matriz de rigidez global del elemento: `k_global = Betaᵀ · k_local · Beta`.
5. Ensamblaje de `k_global` en la matriz de rigidez global de la estructura,
   usando los vectores de colocación (grados de libertad) de cada barra.
6. Partición de la matriz global en grados de libertad libres y restringidos,
   y solución del sistema reducido `K_ff · u_f = F_f` (eliminación gaussiana
   con pivoteo parcial, implementada en JavaScript puro).
7. Recuperación del vector de desplazamientos completo, cálculo de reacciones
   (`R = K·U - F` en los grados de libertad restringidos) y de las fuerzas
   axiales internas de cada barra a partir del desplazamiento local
   (`Beta · U_elemento`).

Este procedimiento reproduce, paso a paso, el algoritmo del notebook original
(ver sección "Fuente y validación" abajo). La calculadora es de propósito
**general**: el usuario puede editar nodos, coordenadas, apoyos, cargas,
conectividad de barras, módulo de elasticidad y área de cada elemento, o
partir de cero, no está limitada al ejemplo precargado.

## Ejemplo precargado

Al abrir `calculadora.html`, la calculadora se carga con los datos EXACTOS
de la cercha de 21 barras usada en el repositorio original
[`stiffness-method-plane-truss-jupyter`](https://github.com/anuar-science/stiffness-method-plane-truss-jupyter),
notebook `Stiffness_method_plane_truss_v1_0_0.ipynb`. Ningún valor fue
inventado, redondeado o completado: son los mismos números presentes en las
celdas de código del notebook.

| Parámetro | Valor extraído del notebook |
|---|---|
| Módulo de elasticidad E | `200 000 000` kPa (200 GPa), único para todas las barras |
| Área A1 | `0.0005` m² (barras del cordón inferior, diagonales y montantes) |
| Área A2 | `0.001` m² (barras 7 a 10, cordón superior central) |
| Nodos | 12 nodos, coordenadas en metros: `(0,0) (3,0) (6,0) (9,0) (12,0) (15,0) (18,0) (3,3) (6,3) (9,3) (12,3) (15,3)` |
| Barras | 21 barras, conectividad: `1-2, 2-3, 3-4, 4-5, 5-6, 6-7, 8-9, 9-10, 10-11, 11-12, 1-8, 2-8, 2-9, 3-9, 3-10, 4-10, 5-10, 5-11, 6-11, 6-12, 7-12` |
| Apoyos | Nodo 1: articulación fija (restringido en X e Y). Nodo 7: apoyo restringido solo en Y (rodillo horizontal) |
| Cargas nodales (kN) | Nodo 2: Fy=-10; Nodo 3: Fy=-15; Nodo 4: Fy=-20; Nodo 5: Fy=-15; Nodo 6: Fy=-10; Nodo 7: Fx=+10; resto de nodos sin carga |

Estos valores corresponden literalmente a las variables `E`, `A1`, `A2`,
`nod`, `Barras`, `GDL_Libres` y `PP` definidas en las celdas de código del
notebook fuente.

## Fuente y validación

- **Repositorio fuente (solo lectura, no modificado):**
  [anuar-science/stiffness-method-plane-truss-jupyter](https://github.com/anuar-science/stiffness-method-plane-truss-jupyter)
- **Notebook fuente:** `Stiffness_method_plane_truss_v1_0_0.ipynb`
- **Cita de la fuente original:**

  > González, A. (2026). *Computational Implementation of the Stiffness
  > Method for Matrix Analysis of a Plane Truss* (Versión 1.0.0) [Software].
  > DOI: 10.5281/zenodo.20042213. Autor: **Anuar González A.**

La lógica de esta calculadora fue validada reproduciendo en JavaScript el
mismo algoritmo del notebook (matrices `Beta` y de rigidez local/global,
ensamblaje, reducción de grados de libertad, solución del sistema y cálculo
de fuerzas axiales) y comparando los resultados numéricos con una ejecución
independiente equivalente en Python/NumPy a partir de los mismos datos de
entrada. Con el ejemplo precargado, ambas implementaciones coinciden en
desplazamientos nodales, fuerzas axiales por barra y reacciones en los
apoyos (nodo 1: Rx=-10 kN, Ry=35 kN; nodo 7: Ry=35 kN), cumpliendo el
equilibrio global de fuerzas.

## Cita de esta calculadora

> González, A. *Calculadora Interactiva del Método de Rigidez para Cerchas
> Planas* [Software]. DOI: 10.5281/zenodo.20042212. Autor: **Anuar González A.**

## Licencia

MIT License (ver [LICENSE](./LICENSE)), misma licencia que el repositorio
fuente [stiffness-method-plane-truss-jupyter](https://github.com/anuar-science/stiffness-method-plane-truss-jupyter).
