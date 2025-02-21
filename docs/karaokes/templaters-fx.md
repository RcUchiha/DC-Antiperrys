---
layout: default
title: Templaters de karaoke
parent: Karaokes
nav_order: 1
---

# Creadores de templates de karaoke

Haz clic [aquí](https://github.com/TypesettingTools/arch1t3cht-Aegisub-Scripts/blob/main/doc/templaters.md#first-steps) para ir a la parte importante.

Esta no es una guía completa sobre cómo hacer KFX, sino más bien una explicación rápida de las ideas principales que deberían ayudarte lo suficiente para comenzar a leer la documentación de tu creador de templates favorito. Principalmente contiene una versión mejorada de una larga explicación que escribí en Discord en algún momento, así como algunas tablas que explican cómo convertir entre los tres creadores de templates establecidos. Básicamente, es la guía que hubiera deseado tener al aprender esto.

Si estás buscando guías reales sobre KFX, aquí tienes algunos enlaces:

- [Guía KFX de Zahuczky](https://zahuczky.com/01-kfx-guide-introduction/): Todavía en proceso de elaboración al momento que escribo esto.
- [Publicaciones de Jocko](https://jockotan.wordpress.com/2015/08/12/karaoke-templater/): También muy concisas y basadas en ejemplos, pero usa el creador de templates estándar.
- La documentación real de los diversos creadores de templates, enlazada abajo.

Además, tengo [aquí](https://github.com/TypesettingTools/arch1t3cht-Aegisub-Scripts/blob/main/doc/misc_kara.md) una colección de templates diversas que hice para la edición de carteles u otros fines que no son estilización de canciones.

---

## Creadores de Templates Existentes

Al momento de escribir esto (2022), hay tres principales creadores de templates de karaoke:

1. El creador de templates estándar que viene con Aegisub, documentado [aquí](https://aegisub.org/docs/latest/automation/karaoke_templater/) como parte de la documentación de Aegisub. La documentación es muy detallada, pero bastante técnica.
1. [KaraOK](https://github.com/logarrhythmic/karaOK), una versión modificada del creador de templates estándar junto con una biblioteca de utilidades. También se incluye por defecto con algunas versiones más nuevas de Aegisub, como AegisubDC.
1. [El Creador de Templates de The0x539](https://github.com/The0x539/Aegisub-Scripts/blob/trunk/src/0x.KaraTemplater.moon), documentado [aquí](https://github.com/The0x539/Aegisub-Scripts/blob/trunk/doc/0x.KaraTemplater.md) en su repositorio. Este es un creador de templates completamente reescrito con una lógica de ejecución más potente (mixins, condicionales más fuertes, bucles anidados, etc) y variables organizadas de manera más sensata.

Excepto por furigana y otros usos de `furi` o templates `multi`, realmente no hay nada que el creador de templates estándar o KaraOK puedan hacer que no pueda ser fácilmente portado al creador de templates de The0x. Siempre que esté instalado, el creador de templates de The0x también contiene soporte directo para la biblioteca de utilidades de KaraOK. Es por eso que será el creador de templates predeterminado para esta guía, junto con notas sobre cómo los conceptos equivalentes funcionan en otros creadores de templates. Incluso si nunca escribirás templates en otros creadores de templates, estos últimos aún pueden ser útiles para entender las templates existentes.

### Ver también

Existen otras herramientas destacadas para hacer KFX. No se explican aquí porque funcionan de manera muy diferente, pero aún así vale la pena mencionarlas.

- [KaraEffector](https://github.com/KaraEffect0r/Kara_Effector), una gran colección de plantillas existentes y formas de modificarlas.
- [PyonFX](https://github.com/CoffeeStraw/PyonFX), una biblioteca de Python para generar subtítulos `.ass`, adaptada para KFX.
- [aegsc](https://github.com/butterfansubs/aegsc), un compilador que produce scripts `.ass` (especialmente templates de karaoke) que permite escribir templates con un formato más conveniente.

---

## Empezando

Esta guía utiliza el [Creador de Templates de The0x539](https://github.com/The0x539/Aegisub-Scripts/blob/trunk/src/0x.KaraTemplater.moon), el cual necesitarás instalar primero (también te recomiendo encarecidamente que asignes su función a un atajo de teclado. Yo uso Ctrl+Alt+S). La guía intercala explicaciones semitécnicas (formateadas normalmente) con ejemplos muy simples pero concretos (formateados en cursiva). También puedes seguir solo los ejemplos en cursiva si prefieres un enfoque puramente práctico.

### Primeros Pasos

Lo siguiente es el archivo de karaoke más simple que puedas imaginar:
```
[Script Info]
ScriptType: v4.00+
WrapStyle: 0
ScaledBorderAndShadow: yes
YCbCr Matrix: TV.709
PlayResX: 1920
PlayResY: 1080

[V4+ Styles]
Format: Name, Fontname, Fontsize, PrimaryColour, SecondaryColour, OutlineColour, BackColour, Bold, Italic, Underline, StrikeOut, ScaleX, ScaleY, Spacing, Angle, BorderStyle, Outline, Shadow, Alignment, MarginL, MarginR, MarginV, Encoding
Style: Karaoke,Georgia,72,&H002A0A00,&H000019FF,&H00FFFFFF,&H00000000,0,0,0,0,100,100,0,0,1,2.5,0,8,30,30,25,1

[Events]
Format: Layer, Start, End, Style, Name, MarginL, MarginR, MarginV, Effect, Text
Comment: 0,0:00:00.00,0:00:00.00,Karaoke,,0,0,0,template line,
Comment: 0,0:00:00.00,0:00:00.00,Karaoke,,0,0,0,,
Comment: 0,0:00:20.88,0:00:26.80,Karaoke,,0,0,0,kara,{\k0}{\k39}A{\k22}to {\k54}i{\k44}chi{\k40}do {\k18}da{\k60}ke {\k12}ki{\k23}se{\k63}ki {\k39}wa {\k20}o{\k20}ko{\k39}ru {\k36}da{\k63}rou
Comment: 0,0:00:26.80,0:00:32.96,Karaoke,,0,0,0,kara,{\k36}{\k20}Ya{\k30}sa{\k70}shi{\k38}i {\k39}ko{\k21}e {\k58}de {\k21}e{\k18}ga{\k57}ku {\k23}yu{\k37}gan{\k20}da {\k41}mi{\k35}ra{\k52}i
```
(Está sincronizado con [este OP](https://animethemes.moe/anime/fatezero_2nd_season/OP-NCBD1080), por si deseas probarlo.)

Contiene:

- Un estilo.
- Algunas líneas k-timeadas comentadas que tienen este estilo, marcadas con `kara` en su campo de Efecto.
- Una única línea (en blanco, por ahora) comentada que tiene este estilo, marcada con `template line` en el campo Efecto.

*Al ejecutar la macro `The0x539's Templater`, el creador de templates verá la `template line`. Para cada línea marcada como `kara` con el mismo estilo que esta línea, generará una línea con el texto de la línea kara, sin las etiquetas de tiempo k. Estas líneas generadas serán marcadas como `fx` para que puedan ser eliminadas o reemplazadas más tarde.*

Ten en cuenta que diferentes creadores de templates utilizan palabras clave ligeramente diferentes para algunos de estos marcadores:

| Concepto                                      | En el Templater común  | En KaraOK       | En The0x's Templater |
| --------------------------------------------- | ---------------------- | --------------- | -------------------- |
| Marcador de línea de karaoke                  | `karaoke`              | `karaoke`       | `kara` or `karaoke`  |
| Template que aplica tags una vez a cada línea | `template pre-line`    | `template line` | `template line`      |

Este es el principio de una lista más larga de diferencias entre los templaters. Las [tablas](#comparación) al final de esta guía muestran la lista completa de diferencias.

*A continuación, intenta escribir `{\fad(150,150)}` en el texto de la línea `template line`, y aplica el template.
Esto dará a cada línea generada `fx` estos tags de desvanecimiento. Así que ya hemos creado una versión peor de HYDRA usando templates de karaoke.*

Lo que ocurre aquí es que antes de convertir una línea `kara` en una línea `fx` usando una `template line`, el creador de templates añadirá lo que la línea `template line` contenga delante del texto (eliminado) de la línea `kara`.
Ahora, la parte interesante es que esto no sólo puede contener texto estático, sino también expresiones Lua. Éstas van envueltas en signos de exclamación.

*Por ejemplo, si cambias el texto de la línea de la template por `{\fad(!2*50!, 150)}`, obtendrás un montón de líneas con tags `\fad(100,150)`, porque lo que había en los signos de exclamación se evaluó a 100 usando Lua.*

Además, en este entorno Lua, el creador de templates te da acceso a toda la información necesaria sobre tu línea `kara`.
Esto se almacena en la tabla `orgline`, que tiene el formato de la tabla estándar que describe una línea `.ass`, pero también contiene un montón de campos adicionales añadidos por karaskel o por el creador de templates de The0x.
Los campos de karaskel se documentan [aquí](https://aegisub.org/docs/latest/automation/lua/modules/karaskel.lua/#dialogue-line-table), mientras que los campos de The0x se documentan en la documentación del creador de templates.

*Por ejemplo, `orgline.duration` es la duración de la línea `kara`.
Así que si pones `{\t(0,!orgline.duration/2!,\fscx150\fscy150)\t(!orgline.duration/2!,!orgline.duration!,\fscx100\fscy100)}` en tu `template line`, esto crearía un efecto que hace que cada línea se haga más grande y más pequeña a lo largo de su duración.
Si la línea dura 1000 ms, se convertirá en `{\t(0,500,\fscx150\fscy150)\t(500,1000,\fscx100\fscy100)}`.*

Lua eval es una de las dos características adicionales disponibles en las templates.
La otra consiste en variables de línea prefijadas por `$`, que serán sustituidas directamente por su valor.
Esto ocurre antes de evaluar las expresiones de Lua.

*Por ejemplo, la variable de línea `$ldur` también se evalúa a la duración de la línea.
Así que la plantilla anterior también podría escribirse como `{\t(0,!$ldur/2!,\fscx150\fscy150)\t(!$ldur/2!,$ldur,\fscx100\fscy100)}`. 
Nota que la última aparición de `$ldur` no va entre signos de exclamación, ya que su valor puede sustituirse directamente en el texto del comando.
Sin embargo, las dos instancias anteriores todavía necesitan ocurrir dentro de un bloque Lua eval para realizar operaciones aritméticas con ellas después.*

Las variables de línea para el creador de templates común están documentadas [aquí](https://aegisub.org/docs/latest/automation/karaoke_templater/inline_variables/).
KaraOK añade una variable de índice de caracteres `$ci`.
Sin embargo, el creador de templates de The0x elimina la mayoría de estas variables de línea.
Puedes ver cuáles están todavía disponibles [aquí](https://github.com/The0x539/Aegisub-Scripts/blob/4d00d789d4897a04687fa7f66d3dce29dae64b64/src/0x.KaraTemplater.moon#L750-L775).
Sin embargo, esto no es realmente una restricción, ya que todos sus valores (y más) son accesibles a través de `orgline` y amigos.

### Sílabas

Hasta ahora, solo hemos añadido algunos tags a cada línea k-timeada `kara`, sin usar nunca el k-timeo real.
Pero ahora que hemos introducido Lua eval y variables de línea, estamos listos para hacer efectos sensatos basados en sílabas.

*Si reemplazamos el efecto de nuestra línea `template line` por `template syl` y aplicamos el template de nuevo, el creador de templates generará de repente múltiples líneas `fx` para cada línea `kara`; una línea `fx` por cada sílaba k-timeada de la línea `kara`.
Si no eliminaste el efecto de escalado que hicimos antes, todas estarán apiladas unas encima de otras en la parte superior de la pantalla.
Si lo eliminaste, se moverán sucesivamente hacia abajo hasta cubrir toda la pantalla.*

Esto se debe a que, por defecto, las líneas de sílabas `fx` generadas no tienen ningún tipo de formato, por lo que naturalmente seguirán la alineación dictada por su estilo.
Para posicionarlas correctamente, necesitamos usar los tags `\an` y `\pos` junto con algunas de las variables que nos da el creador de templates.
Ya hemos usado antes `orgline`, y `orgline.left` nos da la posición X del borde izquierdo de la línea `kara`, al formatearla con el estilo que tenga.
Del mismo modo, `orgline.top` nos da la coordenada Y del borde superior.

Para colocar cada sílaba donde debe ir, podemos utilizar la tabla análoga `syl`, que (obviamente) contiene información similar sobre la sílaba.
Lo más importante es que `syl.left` contiene la posición X del borde izquierdo de la sílaba, *respecto a la línea en la que está*.

*Con esto en mente, podemos escribir lo siguiente en nuestro `template syl`: `{\an7\pos(!orgline.left+syl.left!,!orgline.top!)}`.
Si ahora volvemos a aplicar nuestro template, todas las sílabas estarán en la posición correcta.*

*Ahora, mientras que `\an7` fue el ejemplo más fácil, pero rara vez es conveniente para cualquier efecto real.
Así que vamos a utilizar en su lugar, `{\an5\pos(!orgline.left+syl.center!,!orgline.middle!)}` que utiliza las variables análogas para las posiciones X e Y de la mitad de la línea o sílaba.
Esto no cambia el posicionamiento, pero utiliza `\an5` en su lugar, que es más útil en casi todos los casos.*

En el creador de templates común o KaraOK también puedes encontrar variables de línea como `$scenter` y `$lmiddle` usadas para esto[^2].
No necesitamos `syl` para la posición Y, ya que la posición Y de la sílaba no depende de la línea con formato normal.
De hecho, `syl.middle` ni siquiera existe.
[^2]: Soy consciente de que `$smiddle` también existe, pero estoy intentando resaltar el patrón `$s[var]` y `$l[var]`.

*Con todo lo que sabemos ahora, ya podemos hacer un simple template.
Las únicas piezas que nos faltan son las variables `syl.duration`, `syl.start_time` y `syl.end_time`, que son la duración de la sílaba y los tiempos de inicio y fin en milisegundos respectivamente, siendo estos dos últimos relativos al tiempo de inicio de la línea actual.
Con esto, podemos hacer un simple template...*
```
{\an5\pos(!orgline.left+syl.center!,!orgline.middle!)
\t(!syl.start_time!,!syl.start_time+syl.duration/2!,\fscx130\fscy130)
\t(!syl.start_time+syl.duration/2!,!syl.end_time!,\fscx100\fscy100)}
```
*... que resalta cada sílaba haciéndola más grande y más pequeña de nuevo.*

*Nota: Esto es solo un ejemplo de estilo, diseñado para ser lo más simple posible. No es un estilo perfecto, ni es un estilo que debas copiar en absoluto. Su mayor problema es que el escalado de las sílabas resaltadas sigue una simple curva triangular: crece durante la mitad de la duración de la sílaba y se reduce durante la otra mitad. Esto hará que las sílabas cortas crezcan y se reduzcan muy rápidamente, mientras que las sílabas más largas crecen y se reducen muy lentamente, alcanzando su tamaño máximo demasiado tarde. Para el estilo real de la canción, debería hacer que el realce siguiera algo parecido a una [curva ADSR](https://en.wikipedia.org/wiki/Envelope_(music)#ADSR), es decir, dejar los tiempos de crecimiento y encogimiento algo constantes y mantener el efecto de realce durante algún tiempo para las sílabas más largas.*

**Ya expliqué la mayoría de los conceptos básicos de templates, y espero que ahora puedas leer la documentación de los distintos creadores de templates o diseccionar los templates existentes ([estos](misc_kara.md), por ejemplo) para profundizar. Sigue leyendo si quieres una introducción a algunos de los conceptos más específicos.**

### Efectos multilínea y mixins
Nuestra `template syl` genera una línea `fx` por cada sílaba de entrada.
Si queremos hacer un efecto que genere múltiples líneas para cada sílaba podemos añadir más líneas `template syl`. (Para efectos más complejos, también podemos usar bucles).

*Por ejemplo, vamos a añadir un efecto de brillo azulado a nuestras líneas de karaoke.
Toma la línea `template syl` que hicimos antes, y duplícala.
Aumenta la capa de la segunda en uno, y añade `\3c&HFFCCCC&\bord5\blur5` a la primera.
Si ahora aplicas el template, se generarán dos líneas `fx` para cada sílaba, una para cada `template syl`.
Las líneas `fx` también tendrán las mismas capas que sus respectivas líneas `template syl`.*

Pero si no tenemos cuidado (como ocurrió en este ejemplo), esto duplica ahora gran parte del código de nuestro template. Si queremos cambiar algún elemento del efecto de resaltado, necesitaríamos cambiar ambas líneas `template syl`. Aquí es donde los mixins son muy útiles.

Los mixins son una forma de aplicar tags adicionales (o cualquier texto, en realidad) a algún subconjunto de las líneas `fx` *generadas*. Por ejemplo, por defecto una línea `mixin syl` añadirá su contenido a cada sílaba de cada línea generada, sin importar si es de una `template line` o de una `template syl`. Usando modificadores como `layer`, `t_actor`, o `if` y `unless`, podemos restringir a qué líneas se aplica un mixin. Esto es muy útil tanto para limpiar templates y eliminar código duplicado, como para el formateo condicional.

*En nuestro caso, podemos reescribir nuestro template de la siguiente manera:*
```
[layer 0] template syl: {\3c&HFFCCCC&\bord5\blur5}
[layer 1] template syl:
             mixin syl: {\an5\pos(!orgline.left+syl.center!,!orgline.middle!)
                         \t(!syl.start_time!,!syl.start_time+syl.duration/2!,\fscx130\fscy130)
                         \t(!syl.start_time+syl.duration/2!,!syl.end_time!,\fscx100\fscy100)}
```
*De hecho, al dividir el `mixin` en múltiples partes, podemos separar las diferentes partes de este template por su propósito, y hacerlo más fácil de leer:*
```
[layer 0] template syl: {\3c&HFFCCCC&\bord5\blur5}
[layer 1] template syl:
             mixin syl: {\an5\pos(!orgline.left+syl.center!,!orgline.middle!)}
             mixin syl: {\t(!syl.start_time!,!syl.start_time+syl.duration/2!,\fscx130\fscy130)
                         \t(!syl.start_time+syl.duration/2!,!syl.end_time!,\fscx100\fscy100)}
```
Los mixins (al menos en esta capacidad) son una característica exclusiva del creador de templates de The0x. Lea su documentación para encontrar todos los modificadores posibles para la ejecución condicional.

### Líneas de código
Ahora que tenemos toda la funcionalidad básica, podemos hablar de líneas de código. Estas te permiten escribir código Lua que se ejecuta una vez, o una vez por cada línea, sílaba, palabra o carácter.
Los usos simples incluyen la definición de constantes o funciones que calculan valores que necesitas en tus templates. Son bastante autoexplicativos, y todo lo demás importante se explica en la documentación, así que aquí solo hay un ejemplo de uso rápido para completar nuestra plantilla de ejemplo:

*En este momento, nuestro template hace que las sílabas se agranden en la primera mitad de su duración, y se achiquen en la segunda mitad. Si queremos que crezcan más rápido, podemos usar algo como `syl.start_time + 0.3 * syl.duration` en los tags `\t`. Pero ahora, tenemos que escribir el `0.3` dos veces. Si queremos cambiar el valor más tarde, podríamos olvidar uno de los dos y romper el efecto. Así que pongamos esto en una variable constante:*
```
             code once: ticreci = 0.3
[layer 0] template syl: {\3c&HFFCCCC&\bord5\blur5}
[layer 1] template syl:
             mixin syl: {\an5\pos(!orgline.left+syl.center!,!orgline.middle!)}
             mixin syl: {\t(!syl.start_time!,!syl.start_time+ticreci*syl.duration!,\fscx130\fscy130)
                         \t(!syl.start_time+ticreci*syl.duration!,!syl.end_time!,\fscx100\fscy100)}
```
*Ya que estamos en ello, demos también nombres a algunas de las otras constantes:*
```
             code once: ticreci = 0.3; escalacreci = 130; colorbrillo = "&HFFCCCC";
[layer 0] template syl: {\3c!colorbrillo!\bord5\blur5}
[layer 1] template syl:
             mixin syl: {\an5\pos(!orgline.left+syl.center!,!orgline.middle!)}
             mixin syl: {\t(!syl.start_time!,!syl.start_time+ticreci*syl.duration!,\fscx!escalacreci!\fscy!escalacreci!)
                         \t(!syl.start_time+ticreci*syl.duration!,!syl.end_time!,\fscx100\fscy100)}
```
*También podríamos tener una línea `code once` separada para cada constante, eso es solo cuestión de gustos.*

*Para no tener que teclear `!syl.start_time+ticreci*syl.duration!` dos veces, también podríamos convertir esto en una variable. Podemos hacerlo con un `code syl`, o con la función `set`:*
```
             code once: ticreci = 0.3; escalacreci = 130; colorbrillo = "&HFFCCCC";
[layer 0] template syl: {\3c!colorbrillo!\bord5\blur5}
[layer 1] template syl:
             code  syl: ticre = syl.start_time+ticreci*syl.duration
             mixin syl: {\an5\pos(!orgline.left+syl.center!,!orgline.middle!)}
             mixin syl: {\t(!syl.start_time!,!ticre!,\fscx!escalacreci!\fscy!escalacreci!)
                         \t(!ticre!,!syl.end_time!,\fscx100\fscy100)}
```
*o*
```
             code once: ticreci = 0.3; escalacreci = 130; colorbrillo = "&HFFCCCC";
[layer 0] template syl: {\3c!colorbrillo!\bord5\blur5}
[layer 1] template syl:
             mixin syl: {\an5\pos(!orgline.left+syl.center!,!orgline.middle!)}
             mixin syl: {!set("ticre", syl.start_time+ticreci*syl.duration)!
                         \t(!syl.start_time!,!ticre!,\fscx!escalacreci!\fscy!escalacreci!)
                         \t(!ticre!,!syl.end_time!,\fscx100\fscy100)}
```
*La segunda es probablemente más fácil de leer, pero la primera puede ser más limpia si necesitas esta variable en múltiples templates o mixins.*

### Funciones
Los creadores de templates proporcionan varias funciones útiles para modificar aún más la línea actual.
Todas están documentadas en la documentación del respectivo creador de templates.
Solo quiero destacar la más importante, llamada `retime`.

La función `retime` te permite cambiar el tiempo de salida de la línea.
No hay ninguna magia involucrada aquí; la tabla `line` siempre contiene los campos de la línea que se está generando actualmente y puede ser cambiada por cualquiera, así que nadie te impide escribir `!(function() line.start_time = 1234 end)()!` para establecer el tiempo de inicio de la línea `fx` generada a `1:23`.
La función `retime` hace esto mucho más cómodo.

*Por ejemplo, incluso con `template syl`, la línea generada utiliza por defecto el tiempo de la línea original `kara`.
Si en cambio quieres que solo aparezca cuando la sílaba está siendo resaltada, puedes añadir `!retime("syl")!` a tu template para establecer su tiempo de inicio y fin al tiempo (absoluto) de inicio y fin de la sílaba, respectivamente.
Si quieres un lead-in y lead-out de 150 y 300 milisegundos respectivamente, puedes escribir `!retime("syl", -150, 300)!`.
Si por el contrario quieres que la sílaba desaparezca justo cuando debería empezar a resaltarse, puedes usar `!retime("start2syl")!` en su lugar.
Consulta la documentación para conocer todos los modos posibles.*

### Bucles
Por último, hablemos de los bucles.
Puedes usar bucles en templates para crear efectos más complejos, como generar múltiples líneas a partir de cada aplicación de cualquier línea de `template`.
También puedes establecer el número de iteraciones durante el tiempo de ejecución.
Con el creador de templates de The0x, puedes incluso utilizar múltiples bucles anidados.
Usando la función `util.fbf`, también puedes configurar fácilmente un bucle que genere una línea de salida para cada fotograma.
Los mixins también soportan bucles en el creador de templates de The0x.

*Por ejemplo, considera una línea con el efecto `template line loop myloop 5` y el texto...*
```
{!relayer($maxloop_myloop - $loop_myloop)!
\an5\pos(!line.center - $loop_myloop - 1!,!line.middle + $loop_myloop - 1!)}
```
*... para hacer algo como una sombra sólida. O podrías tener una simple `template line` con el texto...*
```
{!util.fbf("line")!\an5
\pos(!line.center + 100 * math.sin(2 * (line.start_time - orgline.start_time) / 1000)!, !line.middle!)}
```
*... para hacer que la línea se mueva suavemente de lado a lado.*

Para los efectos frame a frame que no utilizan posicionamiento, también podrían interesarte las [funciones de onda de KaraOK](https://github.com/logarrhythmic/karaOK#wave-table---pushing-the-limits-of-ass), que también están disponibles en el creador de templates de The0x.

### Otros
Algunas cosas que no he mencionado aquí son:
- Actores: Hacer que los efectos se comporten de manera diferente (digamos, que tengan diferentes colores) dependiendo de los campos Actor de las líneas `kara`.
- Efectos en línea(Inline FX): Usar marcadores en las líneas `kara` para tener diferentes efectos para sílabas individuales.
- Las otras poderosas características de ejecución condicional de los mixins, particularmente `if` y `unless`.
- Gradientes usando la librería de utilidades del creador de templates de The0x, así como la librería color.
- Estilo Furigana.

De nuevo, lee la documentación para ver qué es posible allí.

## Comparación

Las principales diferencias entre los creadores de templates, ya sea el común o KaraOK y el de The0x son:

- El componente `mixin` y la clase `word`.
- Falta de las clases/modificadores `multi`/`furi`.
- La mayoría de las variables de dólar necesitan ser accedidas a través de las tablas `tenv` en lua en línea (in inline lua).
- Bucles nombrados y anidables.
- Ejecución condicional (aplicable a todos los componentes, no solo a `mixin`).

A continuación se ofrece una lista más completa.

**Ten en cuenta que todos los enlaces a las secciones de código de los distintos creadores de templates son enlaces permanentes y pueden estar obsoletos.**

### Marcadores de línea
Esta no es una lista completa. Consulta la documentación para ver todos los modificadores posibles.

| Templater común                               | KaraOK                  | Templater de The0x             |
| --------------------------------------------- | ----------------------- | ------------------------------ |
| `karaoke`                                     | `karaoke`               | `kara` o `karaoke`             |
| `fx`                                          | `fx`                    | `fx`                           |
| `template pre-line`                           | `template line`         | `template line`                |
| `template syl`                                | `template syl`          | `template syl`                 |
| `template char` o `template syl char`         | `template char`         | `template char`                |
| no presente                                   | `template word`         | `template word`                |
| `template line`                               | `template lsyl`         | `template line` + `mixin syl`  |
| `template furi`                               | `template furi`         | no presente                    |
| no presente                                   | `template furichar`     | no presente                    |
| no presente                                   | `template lchar`        | `template line` + `mixin char` |
| no presente                                   | `template lword`        | `template line` + `mixin word` |
|                                               |                         |                                |
| `all` (para template y componentes de código) | `all`                   | `anystyle`                     |
| no presente                                   | `all style <stylename>` | `style <stylename>`            |
|                                               |                         |                                |
| no presente                                   | no presente             | general `mixin line`           |
| no presente                                   | no presente             | general `mixin syl`            |
| no presente                                   | no presente             | general `mixin char`           |
| no presente                                   | no presente             | general `mixin word`           |
|                                               |                         |                                |
| `code once`                                   | `code once`             | `code once`                    |
| `code line`                                   | `code line`             | `code line`                    |
| `code syl`                                    | `code syl`              | `code syl`                     |
| no presente                                   | `code char`             | `code char`                    |
| no presente                                   | `code word`             | `code word`                    |
| `code furi`                                   | `code furi`             | no presente                    |
|                                               |                         |                                |
| `loop n`                                      | `loop n`                | `loop <loopname> n`            |

### Tablas en tenv
- En todos los creadores de templates, `orgline`, `syl`, `char` y `word` (donde estén presentes y sean aplicables) se refieren a objetos *originales*, mientras que `line` se refiere a la línea `fx` que será generada.
- En el creador de templates común y en KaraOK, `syl` es la sílaba actual para las líneas `template syl`, pero es el *carácter* actual para las líneas `template char`. De hecho, `template char` solo se traduce a `template syl` con una marca por carácter (per-char) internamente.
- Los creadores de templates más recientes añaden tablas que describen la palabra o sílaba actual para las líneas `template char`:
    - En el creador de templates común, `word` y `char` no existen.
    - KaraOK añade las tablas `char`, `word` y `syll` (las dos últimas también son accesibles a través de `char.word` y `char.syll` en `template char`) referenciando el carácter, palabra y sílaba actuales en `template char`.
    - En el creador de templates de The0x, sin embargo, `syl`, `char` y `word` solo existen para `template syl`, `template char` y `template word` respectivamente, y hacen referencia al objeto respectivo. Se puede acceder a la sílaba o palabra actual con `char.syl` y `char.word` respectivamente.

### Otros miembros de tenv
Aparte de las diferentes tablas de líneas, funciones de utilidad y variables de bucle, los creadores de templates exponen diferentes librerías y datos en `tenv`, a los que se puede acceder directamente desde los campos Lua eval:

- Los tres creadores de templates proporcionan el entorno global `_G` del template(templater) que llama a `tenv`, a través del cual se puede acceder a todas las demás variables globales. Por ejemplo, en el creador de templates común o en KaraOK, se puede acceder a la librería `unicode` de Aegisub a través de `_G.unicode`.
- El creador de templates común solo exponía las librerías `string` y `math`, como se ve [aquí](https://github.com/Aegisub/Aegisub/blob/6f546951b4f004da16ce19ba638bf3eedefb9f31/automation/autoload/kara-templater.lua#L294-L300). El miembro final `meta` es el mapa de campos de metadatos de script en la sección `[Script Info]` del archivo de subtítulos, como [recogido por karaskel](https://aegisub.org/docs/latest/automation/lua/modules/karaskel.lua/#karaskelcollect_head).
- Además de esto, KaraOK expone la librería `math`, así como el objeto de subtítulos `subs` y varias funciones de utilidad como `ipairs` y `tostring`. La lista completa está [aquí](https://github.com/logarrhythmic/karaOK/blob/dc26cf017809b3d6ee2fabf01b454b09a6d1413a/autoload/ln.kara-templater-mod.lua#L336-L349).
- El creador de templates de The0x expone todas las librerías que hace KaraOK, junto con las librerías de Aegisub `unicode` y `karaskel`. Cuando está disponible, también expone la librería de KaraOK como `ln` (que se inicializa automáticamente), y la librería color de The0x como `colorlib`.
El creador de templates de The0x no expone todas las funciones de utilidad de KaraOK (`ipairs` es la más notable), pero expone algunas otras funciones como `require`. Ver [aquí](https://github.com/The0x539/Aegisub-Scripts/blob/3cd439b9d2bef2307a1c5725deb03206383b8ad3/src/0x.KaraTemplater.moon#L181) para una lista completa.
El creador de templates también expone los objetos `subs`, `meta` y `styles`, y su propia librería de utilidades [aquí](https://github.com/The0x539/Aegisub-Scripts/blob/3cd439b9d2bef2307a1c5725deb03206383b8ad3/src/0x.KaraTemplater.moon#L240).

### Variables de línea
- La siguiente lista es completa con respecto a las variables de línea en el creador de templates común y KaraOK. Está en el mismo orden que la [documentación para variables de línea en el creador de templates común](https://aegisub.org/docs/latest/automation/karaoke_templater/inline_variables/), así que refiérase a ella para las explicaciones de estas variables.
    - El creador de templates de The0x contiene variables de línea adicionales para trabajar con bucles, así como las variables `$env_*` (ver el código fuente para estas).
- Las expresiones de la siguiente tabla son análogas entre sí, pero pueden no ser exactamente iguales. En particular, algunos valores se redondean en el creador de templates común.
- Muchas de las expresiones listadas a continuación para el creador de templates de The0x también funcionan para el creador de templates común y KaraOK.


|Templater común & KaraOK|Templater de The0x                            |
|------------------------|----------------------------------------------|
|`$layer`                |`orgline.layer`                               |
|`$lstart`               |`orgline.start_time`                          |
|`$lend`                 |`orgline.end_time`                            |
|`$ldur`                 |`$ldur` o `orgline.duration`                  |
|`$lmid`                 |`orgline.start_time + 0.5 * orgline.duration` |
|`$style`                |`orgline.style`                               |
|`$actor`                |`orgline.actor`                               |
|`$margin_l`             |`orgline.eff_margin_l`                        |
|`$margin_r`             |`orgline.eff_margin_r`                        |
|`$margin_t`             |`orgline.eff_margin_t`                        |
|`$margin_b`             |`orgline.eff_margin_b`                        |
|`$margin_v`             |`orgline.eff_margin_v`                        |
|`$syln`                 |`#orgline.syls`                               |
|`$li`                   |`$li` o `orgline.li`                          |
|`$lleft`                |`orgline.left`                                |
|`$lcenter`              |`orgline.center`                              |
|`$lright`               |`orgline.right`                               |
|`$ltop`                 |`orgline.top`                                 |
|`$lmiddle`              |`orgline.middle`                              |
|`$lbottom`              |`orgline.bottom`                              |
|`$lx`                   |no presente                                   |
|`$ly`                   |no presente                                   |
|`$lwidth`               |`orgline.width`                               |
|`$lheight`              |`orgline.height`                              |
|                        |                                              |
|`$sstart`               |`$sylstart` o `syl.start_time`                |
|`$send`                 |`$sylend` o `syl.end_time`                    |
|`$smid`                 |`syl.start + 0.5 * syl.duration`              |
|`$sdur`                 |`$syldur` o `syl.duration`                    |
|`$si`                   |`$si` o `(syl o char u orgline).si`           |
|`$ci` (solo KaraOK)     |`$ci` o `(char o syl o word u orgline).ci`    |
|no presente             |`$wi` o `(word o char u orgline).wi`          |
|no presente             |`$cxf`                                        |
|no presente             |`$sxf`                                        |
|no presente             |`$wxf`                                        |
|`$skdur`                |`$kdur` o `syl.kdur` o `syl.duration / 2`     |
|`$sleft`                |`orgline.left + syl.left`                     |
|`$scenter`              |`orgline.left + syl.center`                   |
|`$sright`               |`orgline.left + syl.right`                    |
|`$sbottom`              |igual que `$lbottom`                          |
|`$smiddle`              |igual que `$lmiddle`                          |
|`$stop`                 |igual que `$ltop`                             |
|`$sx`                   |no presente                                   |
|`$sy`                   |no presente                                   |
|`$swidth`               |`syl.width`                                   |
|`$sheight`              |`syl.height`                                  |
|                        |                                              |
|Automatic variants      |no presente                                   |