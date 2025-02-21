# Colorlib

¿Alguna vez has querido crear un degradado en KFX? ¿Se siente frustrado por los espacios de color, ya que los degradados RGB rara vez resultan como usted desea? No busque más.

Colorlib le da una multitud de útiles[^1] para manipular colores en ASS (y, oye, por qué no fuera de él también).

[^1]: no se garantiza la utilidad de dichas funciones

En esto, "cadena de color ASS" se refiere a un valor que podrías pasar a, por ejemplo, una etiqueta `\c`, es decir: `&H012345&`.

## Interpolación

Interpolación lineal entre dos colores en un espacio de color determinado. Todos los argumentos tienen el mismo formato:

`interp_xxx(t, color_1, color_2)`

-   `t`: "Distancia" a recorrer, de 0 a 1, ambos inclusive. Un valor de 0 corresponde a `color_1`, un valor de 1 a `color_2`. Un valor de 0.5 está a medio camino.
-   `color_1`: color "inicial
-   `color_2`: color "Final

Devuelve una cadena de color ASS.

Actualmente, se admiten estos cuatro espacios de color:

-   `interp_lch` - LCh (CIE L\*C\*h)
-   `interp_lab` - CIELAB (L\*a\*b\*)
-   `interp_rgb` - RGB simple
-   `interp_xyz` - XYZ (CIE 1931 XYZ)

Además, `interp_alpha` es una función con una firma equivalente para los valores alfa de ASS.

## Formateo del color

Formatea valores "crudos" en cadenas de color ASS.

`fmt_rgb(r, g, b)`

-   Todos los valores entre 0 y 255.

`fmt_xyz(x, y, z)`

-   Todos los valores entre 0 y 1.

`fmt_lab(l, a, b)`

-   `l` entre 0 y 100.
-   `a` y `b` entre -128 y 127.

`fmt_lch(l, c, h)`

-   `l` entre 0 y 100.
-   `c` y `h` entre 0 y 360.

`fmt_alpha(alpha)`

-   Espero que se explique por sí mismo, sobre todo para completar el conjunto. Toma valores entre 0 y 255.

## Ajuste del color

Ajusta un color determinado. Actualmente sólo soporta operaciones de aclarar/oscurecer. En el momento de escribir esto, la lógica de aclarado no *funciona* correctamente, por lo que algunos colores no alcanzan los extremos deseados. Si es relevante, puedes usar valores fuera del rango ±1.

`lighten(color, cantidad)`

-   Aclara el color dado (cadena de color ASS) en la cantidad especificada.
-   `cantidad` oscila entre -1 y 1.
    1.  0 deja el color sin cambios
    2.  1 lo hace lo más claro posible (cercano al blanco)
    3.  \-1 hace que sea lo más oscuro (cercano al negro) posible

`lighten_complex(x, y, z, cantidad, espacio_color)`

-   Como `lighten`, pero en su lugar toma valores de color sin procesar en lugar de una cadena de color.
-   `colorspace` puede ser `rgb`, `xyz`, `lch` o `lab` (por defecto).
