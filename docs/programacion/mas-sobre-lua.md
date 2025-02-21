# Más sobre programación en Lua

Sigo escuchando a la gente decir "Necesito aprender algo de Lua" o "Necesito aprender a escribir scripts de automatización", y no muchos parecen haberse adentrado realmente en ello. Esto debería ayudarte a empezar. Deberías leer la guía de Lyger primero, porque no voy a explicar las mismas cosas de nuevo, pero quiero proporcionar algunos consejos más prácticos. En lugar de explicar Lua en sí mismo, explicaré más sobre los scripts para Aegisub específicamente.

Aprender Lua no es gran cosa. Puedes aprender todo lo que necesitas de Lua en una hora. Principalmente son solo **if/then/end**, el ciclo **for**, **gsub**, y algunas otras cosas.

Una gran parte de lo que necesitas son expresiones regulares, o más bien, la coincidencia de patrones simplificada de Lua. Eso, nuevamente, es algo que puedes aprender en una hora.

Lo que más quiero hablar es cómo trabajar con el objeto Subtitles, que no es realmente un asunto de Lua, sino más bien de Aegisub y el formato ASS. Esto se explica en el manual de Aegisub, pero como eso puede ser confuso para los principiantes, proporcionaré algunos ejemplos prácticos específicos. El objetivo es explicar cómo escribir un script de automatización básica en los términos más simples posibles. Una vez que comprendas cómo funciona un script que agrega desenfoque, agregar funciones más complejas será fácil porque eso es solo matemáticas.

Aquí está lo básico:

```Lua
script_name="prueba" 
script_description="probando cosas" 
script_author="alguien" 
script_version="1"

function test(subs, sel, act) 
	-- aquí va el código 
end

aegisub.register_macro(script_name, script_description, test)
```

La última línea coloca una entrada en tu menú de automatización. Tiene 3 partes. Dos de ellas se definen al principio: script_name y script_description. El nombre aparecerá en el menú y como una entrada de deshacer cuando ejecutes el script. Puedes ver la descripción en el Administrador de Automatización. La tercera parte significa que al ejecutar este script se ejecutará una función llamada "test".

**script_author** y **script_version** no son realmente importantes, pero estoy seguro de que captas la idea.

Veamos la **función test(subs, sel, act)**. Probablemente escribí al menos 20 scripts antes de entender realmente qué es esto. Dado que esta función es referenciada por **register_macro**, es la función principal del script y, como tal, se le da por defecto el objeto Subtitles para trabajar. Las 3 partes — subtítulos, líneas seleccionadas y línea activa — te dan 3 cosas con las que puedes trabajar.

Puedes nombrarlas como quieras. Solo tienes que seguir con la nomenclatura. Tiendo a mantener todo corto, aunque estoy seguro de que no soy el único que usa subs/sel/act. Probablemente sea mejor usar estos incluso solo por el hecho de que otros también lo hacen, lo que facilita entender los scripts de los demás.

**subs** es todo el objeto de subtítulos. Siempre tienes que usar esto. En términos simples, es como una tabla de todas las líneas en el script ASS, incluyendo encabezados, estilos, etc.

**sel** son las líneas seleccionadas, y si quieres que tu función se aplique a todas las líneas, no tienes que usar esto. Puedes tener **function test(subs)**.

**act** es la línea activa, y probablemente no la necesitarás muy a menudo. Puedes usarla para funciones que se supone deben ejecutarse en solo una línea o leer alguna información de la línea activa. Si seleccionas una línea y usas **sel**, es prácticamente lo mismo que usar act.

La parte "aquí va el código" es donde se escribirá la función real.

Aquí tienes un ejemplo de una función simple que se ejecuta en todo el script:

```Lua
function test(subs)
    for i=1,#subs do
        if subs[i].class=="dialogue" then
            line=subs[i]
            text=subs[i].text
	    
	    line.effect="test"
	    
	    line.text=text
            subs[i]=line
	end
    end
    aegisub.set_undo_point(script_name)
end
```

La parte verde es lo que normalmente tendrás en cada script que se ejecuta en todas las líneas. La parte morada es la función específica real.

**#subs** es cuántas líneas hay en **subs** (incluyendo encabezados y todo). Si el archivo ASS tiene 200 líneas, el ciclo **for** se ejecutará 200 veces. Solo quieres aplicar esto a las líneas de diálogo, no a los estilos o encabezados, así que debes especificar esta condición: **if subs[i].class**==**"dialogue"**.

Entonces, el iterador i va de 1 a 200, así que cuando sea, digamos, 25, **subs[i]** es **subs[25]**, o la 25ª línea en el archivo ASS. **line=subs[i]** significa que creas el elemento **line** y colocas **subs[i]** en él. Ten en cuenta que un solo = no significa "igual". Podrías leerlo como "línea ahora es subs[25]" (cuando i es 25). Luego trabajas con **line**, y para que tenga algún uso, debes volver a colocar la **line** en **subs[i]** al final. **line** es algo que creaste, **subs[i]** es la línea real en los subtítulos, así que necesitas **subs[i]=line** al final.

Ves lo mismo con **text**, aunque en este caso no lo necesito, pero generalmente trabajas más con **text**. El propósito es usar algo que sea corto en lugar de escribir **subs[i].text** todo el tiempo. Además, también podría decir **text=line.text** ya que **line** ya está definida en ese punto. Puedes nombrar esas cosas como quieras, por ejemplo solo l y t, lo que puede ser bueno para un script corto, pero nuevamente, **line** y **text** son comúnmente usados por la mayoría de nosotros, así que mantiene las cosas claras.

**aegisub.set_undo_point(script_name)** establece el punto de deshacer, y debería estar al final de la función principal, aunque creo que Aegisub lo hace automáticamente de todos modos. Sin embargo, puedes crear múltiples puntos de deshacer, como para cada función en tu script, pero generalmente solo es confuso y no muy práctico.

Ahora, lo que realmente hace este script es **line.effect="test"**. **line.effect** es el campo de efecto, y aquí toma el valor "test", lo que significa que el texto del campo de efecto será "test". Entonces, lo que hace este script es poner "test" en el campo de efecto de cada línea de diálogo.

Ahora, lo que hice aquí con **text** habría tenido más sentido si lo hubiera hecho con "efecto" en su lugar (porque en realidad no hice nada con texto), es decir, **effect=line.effect**. Luego, la línea morada podría ser solo **effect="test"**. Siempre debes pensar en lo que vale la pena y lo que no. Para este script, la línea morada sería 5 caracteres más corta, pero necesitarías dos líneas adicionales, para asignar el valor al **effect** y para devolver el valor a **line.effect**, así que eso no vale la pena. Si usas algo solo una vez, igual puedes mantenerlo como está. Cuanto más uses algo, más sentido tiene asignarlo a algo con un nombre corto.

Ahora, veamos cómo trabajar con las líneas seleccionadas.

```Lua
function test(subs, sel)
    for x, i in ipairs(sel) do
        line=subs[i]
        text=line.text
	    
	if text:match("tu corbata está torcida") then line.effect="el editor es un idiota" end
	    
	line.text=text
        subs[i]=line
    end
    aegisub.set_undo_point(script_name)
    return sel
end
```

Soy demasiado perezoso para hacer html de alto esfuerzo, pero puedes pegar el código en Notepad++ para un resaltado de sintaxis adecuado. Puedes ver que el ciclo **for** está usando **ipairs**. **sel** consiste en pares de dos números. El primero es el índice de la selección, y el segundo es el índice en **subs**. Si el archivo ASS tiene 50 líneas y seleccionas las últimas 3, entonces el **x** en **ipairs** será 1, 2 y 3, e i será 48, 49 y 50. En el ejemplo anterior, **x** e **i** son lo mismo porque recorre todas las líneas.

No olvides que la función debe tener **(subs, sel)**. Por supuesto, siempre puedes incluir el **sel** incluso si no lo estás usando, solo para asegurarte de que nunca lo olvides. Casi siempre uso **(subs, sel)** y en casos raros agrego **act**.

La línea morada es un ejemplo básico de una acción dependiente de una condición. Puedes leerlo como: Si encuentras "tu corbata está torcida" en el **text**, pon "el editor es un idiota" en el **effect**.

**return sel** se asegura de que mantengas la selección con la que comenzaste (o una nueva selección si la cambiaste).

También podrías usar **for i=1,\#sel** **do** en lugar de **ipairs**, como hicimos con **subs**. Si tu script está eliminando o agregando líneas, necesitas ir hacia atrás, porque las líneas nuevas/eliminadas están cambiando el índice de las líneas seleccionadas. Si eliminas la línea 1, la línea 2 se convierte en la línea 1 y la línea 3 se convierte en la línea 2, por lo que yendo en la dirección normal, estarías omitiendo líneas o pasando por ellas dos veces.

```Lua
for i=#sel,1,-1 do
        local line=subs[sel[i]]
	
	...
	
	subs[sel[i]]=line
```

Este es el uso que le doy. Comienza en la última línea seleccionada y va hacia atrás hasta la primera. **i=a,b,c** significa que va desde a hasta b en pasos de c. **i=8,2,-2** recorrería las líneas 8, 6, 4, 2. El valor predeterminado para los pasos es 1, así que a menos que vayas hacia atrás como aquí, no necesitas escribirlo.

Una cosa importante es que si usas esto, entonces la **line** es **subs[sel[i]]**, no **subs[i]**. Aquí **i** es el número de la línea seleccionada, comenzando desde 1, así que si usas **subs[i]** cuando **i** es 1, tendrías la primera línea en el archivo ASS, no la primera línea seleccionada. **sel[3]** es el número en **subs** correspondiente a la tercera línea seleccionada.

Esta cuestión me confundió durante bastante tiempo, así que intentemos un ejemplo más específico. Digamos que **subs** tiene 50 líneas (incluyendo encabezados y estilos) y seleccionas las últimas 5 líneas. **sel** sería ahora **{46,47,48,49,50}**.   
sel[1]==46   
sel[2]==47   
sel[5]==50   
Usar el ciclo **for** irá de 1 a 5, por lo que **i** será 1-5, y **sel[i]** será 46-50. **subs[i]** serían las líneas 1-5, lo cual no es lo que quieres. **subs[sel[i]]** serán las líneas 46-50. Eso es lo que necesitas.

Entonces, eso más o menos cubre la estructura de la función principal. Con esto y un montón de líneas if/then/end puedes hacer scripts simples.

Ahora, veamos algunas formas de manipular **text**.

```Lua
text=text.." fin de línea"
```

Esto adjunta una cadena al final del **text**.

```Lua
text="Inicio de línea "..text
```

Esto adjunta una cadena al principio del **text**. De esta manera puedes agregar etiquetas:

```Lua
text="{\\blur0.6}"..text
```

Así es como funcionaba el antiguo script "Agregar desenfoque de borde". Por supuesto, esto no lo une con otras etiquetas, no reemplaza el blur existente, etc.

```Lua
text="Esto es una prueba."
text=""
```

Aquí, el primer código establece «Esto es una prueba.» como texto, eliminando lo que había antes. El segundo simplemente elimina el texto, convirtiéndolo en una cadena vacía.

## gsub

**gsub** es prácticamente el núcleo de cada script de automatización. Es lo que reemplaza una cosa por otra. Funciona así:

```Lua
text2=text:gsub("cadena A","cadena B")
```

Esto se traduce en: Si encuentras "cadena A" en **text**, reemplázala con "cadena B" y asigna el **text** modificado a **text2**. Usé **text2** para la explicación, pero normalmente usarías **text=text:gsub**, lo que simplemente mantiene el resultado en **text**.

"No podia verlo."

```Lua
text=text:gsub("No podia","No podía")
```

→ "No podía verlo."

De esta manera, por ejemplo, puedes escribir un script para hacer reemplazos.

```Lua
text=text
:gsub("aqué([lloa]s?)","aque%1")
:gsub("no lo sé","no sé")
:gsub("no ha sido","no fue")
```

Solo necesitas la parte **text=text** una vez. Luego puedes agregar tantas líneas **:gsub** como desees y crear una lista completa de contracciones. Mientras puedes simplemente agregarlas una por una, también puedes usar el patrón de coincidencia (la versión de Lua de expresiones regulares) para mantener el código corto. La primera línea gsub coincidirá con aquél, aquélla, aquéllo, aquéllas y aquéllos. También coincidirá con aquélos y aquélas, pero como esas no existen, eso no nos molesta. La parte entre paréntesis es una captura. **[lloa]** significa "**l** o **ll** o **o** o **a**", y **s?** significa "s si está ahí o nada si no lo está". En las expresiones regulares estándar podrías reemplazar esta captura con (l\|ll\|o\|a\|os\|as) para obtener el mismo resultado. Lua no tiene esta opción, así que a veces necesitas más líneas de las que necesitarías con expresiones regulares completas. El **%1** es la captura, por lo que cualquier cosa que coincida en la primera parte se pegará en la segunda.

Ahora podemos usar esto para reemplazar el valor de blur existente con nuestro nuevo valor.

```Lua
text=text:gsub("\\blur[%d%.]+","\\blur0.6")
```

El desenfoque puede tener números y un punto decimal, así que usa **[%d%.]+** para coincidir con cualquier cosa que sea un número o un punto tantas veces seguidas como sea posible, de modo que cualquier valor que tenga el desenfoque se reemplazará con 0.6.  
El mismo efecto podría lograrse de diferentes maneras:

```Lua
text=text:gsub("(\\blur)[%d%.]+","%10.6") 
text=text:gsub("\\blur[^\\}]+","\\blur0.6"))
```

El primero captura la parte **\\\\blur**, para que no tengas que volver a escribirla (puede ser útil si es algo más largo). El segundo coincide con cualquier cosa que no sea una barra invertida o } tantas veces como pueda, es decir, hasta que encuentre algo que SEA una barra invertida o }, que es donde lógicamente terminaría el valor de blur. Esto puede capturar de manera bastante eficiente todo el valor de cualquier etiqueta, ya que cualquier etiqueta tiene que terminar con \\ o }. Por supuesto, con etiquetas como **\\pos**, querrás capturar las coordenadas en lugar de incluir los ().

También puedes usar una función dentro de **gsub**:

```Lua
text=text:gsub("(\\blur)([%d%.]+)",function(a,b) return a .. 2*b end)
```

**a** y **b** son las capturas. La función las usa, devolviendo **a** (\\\\blur) tal como está, y multiplicando **b** por 2, dándote el valor de desenfoque duplicado. Así que puedes dividir tu patrón en un montón de capturas y hacer algunas operaciones con ellas.

Así es como capitalizas la primera letra de una línea:

```Lua
text=text:gsub("^(%l)([^{]-)", function (c,d) return c:upper()..d end)
```

La primera captura es una letra minúscula al principio de una línea. La segunda captura es desde después de la primera letra hasta {, lo que significa antes de encontrar un comentario o etiqueta. Se devuelve la primera captura en mayúscula y la segunda captura tal como está (lo que significa que ni siquiera tiene que estar allí en este caso, pero podrías, por ejemplo, devolver **d:lower()** para asegurarte de que el resto de la cadena sea en minúsculas).

Ahora puedes entender cómo funciona mi Teletransportador:

```Lua
text=text:gsub("\\pos%(([%d%.%-]+),([%d%.%-]+)%)",function(a,b) return "\\pos(".. a+xx.. "," ..b+yy..")" end)
```

Observa que los paréntesis literales ( y ), es decir, no capturas, deben escaparse con %. Las capturas de coordenadas son **[%d%.%-]+**. Ves que en comparación con lo que teníamos para el desenfoque, estas incluyen %- porque las coordenadas pueden ser negativas. Si no incluyes eso, el script solo funcionará cuando las coordenadas sean positivas. Por lo tanto, captura las coordenadas X e Y, y les suma la entrada del usuario, que aquí es xx e yy. Sí, así de simple.

Un ejemplo más. Este es un "movimiento inverso":

```Lua
text=text:gsub("\\move%(([%d%.%-]+),([%d%.%-]+),([%d%.%-]+),([%d%.%-]+)","\\move(%3,%4,%1,%2")
```

Eso es todo. Captura las 4 coordenadas y las devuelve en orden cambiado: 3, 4, 1, 2. Este es un buen ejemplo de cómo pueden ser útiles las capturas. Puede que notes que el ( no está escapado en la mitad derecha. Las cosas en la parte derecha de gsub no necesitan ser escapadas con %: solo se usa para capturas. Solo la parte izquierda usa expresiones regulares.

## Caracteres de escape

Cuando se utiliza expresiones regulares, estos caracteres deben ser escapados con %: **. ? \* - + ( ) [ ]** y el propio **%**.

Caracteres que deben ser escapados con \\: **" '** y el propio **\\**

Las barras invertidas y las comillas siempre deben ser escapadas, incluso en cadenas literales. (Una comilla real termina la cadena). Si deseas hacer coincidir un signo de interrogación real en una oración, debes hacer coincidir **%?**.

## Expresiones Regulares

No voy a explicar las expresiones regulares desde cero, porque hay mucha información sobre eso en Internet. Lo que voy a hacer es listar algunos patrones que son útiles para los scripts de automatización en Aegisub.

```Lua
{[^\\}]-}	-- comentario (cosas entre { y } que no tienen una barra invertida)
{\\[^}]-}	-- etiquetas (cosas entre { y } que comienzan con una barra invertida)
{[^}]-}		-- comentario o etiquetas (cosas entre { y })
```

El tercero te muestra una manera típica de hacer coincidir cosas entre dos marcadores. Haces coincidir el primer marcador, luego lo-que-no-es-el-segundo-marcador con un - o \*, y luego el segundo marcador. La diferencia entre - y \* es que {.-} coincide solo con un comentario o conjunto de etiquetas, mientras que {.}, si tienes una cadena como "abc{def}ghi{jkl}" coincidirá desde el primer { hasta el último }, así "{def}ghi{jkl}". Siempre tienes que pensar si necesitas +, -, o \*. Si eliges el incorrecto, aún puede funcionar en casos simples, como si solo hay un comentario en la línea, pero fallará en líneas más complejas. Recomiendo crear un archivo ASS de prueba y llenarlo con todo tipo de líneas diferentes, incluidos errores, etiquetas malas, comentarios rotos, etc. Ten todas las combinaciones de texto, etiquetas y comentarios, usa algunas transformaciones, algunas líneas de mocha, cualquier cosa que pueda estar en un script. Si escribes una función, necesita hacer lo que se supone que debe hacer sin importar a qué línea se aplique.

```Lua
%d+		-- secuencia de números
[%d%.]+		-- secuencia de números, puede tener punto decimal (valores de \bord, \blur, \fscx, etc.)
[%d%.%-]+	-- secuencia de números, puede tener punto decimal, puede ser negativo (\xshad, \fsp, \frz...)
&H%x+&		-- valores para colores y alfa
%([^%)]-%)	-- cosas entre ( y )

%(([%d%.%-]+),([%d%.%-]+)%)
```

Esto capturará las coordenadas de \\pos o \\org. También podría capturar el desvanecimiento de entrada y de salida en (\\fad), aunque no es necesario el '-'. Para \\move, captura 4 coordenadas y no incluyas el % final, porque \\move puede o no tener códigos de tiempo.

```Lua
[%a']+ -- palabra (secuencia de letras o apóstrofes, para coincidir con palabras como "don't") 
[^%s]+ -- palabra (secuencia de lo que no es un espacio) 
```

Es posible que necesites diferentes maneras de coincidir con una palabra. El primero de estos patrones no incluirá la puntuación, el segundo sí. A veces necesitarás uno, a veces el otro. También podrías querer reemplazar %a con %w, si deseas incluir "palabras" como AK47 o simplemente contar 20 en "I'm 20 years old." como una palabra.

\\[1234]?c& -- etiqueta de color (no coincide con el valor, solo verifica que la etiqueta esté presente) Esto coincide con \\c, \\1c, \\2c, \\3c, \\4c, pero no con \\clip (¡importante!). (También ten en cuenta que \\fs coincide con \\fsp y \\fscx, así que ten cuidado con los patrones que pueden coincidir con cosas que no deseas). Dado que la primaria puede ser \\c o \\1c, para evitar código complicado que trate con ambas, recomiendo usar esto al principio: text=text

("\\1c&","\\c&") \\c es lo que la herramienta incorporada de Aegisub crea, así que mantén eso como estándar.

Hablando de... trucos como este a menudo son muy útiles. Si tu código necesita tener en cuenta muchas cosas diferentes, ve si puedes reducir el número de estas cosas con algún truco sencillo. Un problema común es, por ejemplo, coincidir con el inicio de una palabra. Una palabra comienza ya sea después de un espacio, o al principio de una línea. Necesitas coincidir con dos patrones para eso. Sin embargo, puedes comenzar agregando un espacio al principio de una línea, luego usa solo un patrón de coincidencia, y luego elimina el espacio al final del script.

Otra cosa es lidiar con líneas con y sin etiquetas (cuando trabajas con texto). Puedes comenzar con esto: tags="" if text

("\^{\\[\^}]*}") then tags=text*

*("\^({\\[\^}]*})") end text=text

("\^{\\[\^}]\*}","")
