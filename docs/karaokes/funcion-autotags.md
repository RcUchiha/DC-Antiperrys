# Introducción a la función personalizada AutoTags y sus variantes comúnmente utilizadas

La función AutoTags se utiliza para iterar entre dos (grupos de) tags de efectos dentro de un período de tiempo continuo, como cambiar el valor de `blur` hacia adelante y hacia atrás, creando un efecto de parpadeo. Es muy común en muchos scripts. Para usar esta función, primero debes declararla en la línea de código.

```lua
function AutoTags(Intervalo,Dato1,Dato2)            local RESULTADO=""     local SUERTE = 0     local CONTADOR = 0     local ARREGLO = 0                           local count = math.ceil(line.duration/Intervalo)                         ARREGLO = {Dato1,Dato2}                            for i = 1, count do               CONTADOR = i                            if Dato1 and Dato2 then                     if  CONTADOR%2 ==0 then                                    SUERTE = ARREGLO[1]                                            else                                    SUERTE = ARREGLO[2]                            end                    end                     RESULTADO = RESULTADO .."\\t(" ..(i-1)*Intervalo.. "," ..i*Intervalo.. ",\\" ..SUERTE..")"..""                      end                 return RESULTADO                                     end
```

![image](https://github.com/RcUchiha/dc-antiperrys/assets/16442041/21be6a36-dbf2-4de6-ad5e-88a1a11fd6ea)

Luego, se llama en el template.

El uso original de la función AutoTags sería: 
`AutoTags(duración del cambio, "tag1", "tag2")`
![image](https://github.com/RcUchiha/dc-antiperrys/assets/16442041/58955ca9-a8f1-4aa9-865f-83ea4131b053)

La efecto generado se vería así:
![image](https://github.com/RcUchiha/dc-antiperrys/assets/16442041/5239fe33-a8d0-416f-aa4f-e35e55943936)

Se puede observar que durante toda la duración de la palabra, se produce un cambio de ida y vuelta de `blur2` a `blur0` y de `blur0` a `blur2`.
Si deseas utilizar un cambio de ida y vuelta entre dos conjuntos de etiquetas, ten en cuenta que debes utilizar un `\` adicional, como en `AutoTags(500, "blur2\\fs50", "blur0\\fs30")`.
La introducción de la función AutoTags en su forma original termina aquí.

A continuación, se presentan distintas variantes de AutoTags generadas por diferentes necesidades.

**Variante 1**
```lua
function AutoTags1(Intervalo,Dato1,Dato2,Pause)  local RESULTADO=""  local SUERTE = 0  local CONTADOR = 0  local ARREGLO = 0  local count = math.ceil(line.duration/(Intervalo+Pause))  ARREGLO = {Dato1,Dato2}  for i = 1, count do          CONTADOR = i          if Dato1 and Dato2 then                  if  CONTADOR%2 ==0 then                  SUERTE = ARREGLO[1]                  else                  SUERTE = ARREGLO[2]                  end          end          RESULTADO = RESULTADO .."\\t(" ..(i-1)*(Intervalo+Pause).. "," ..i*Intervalo+Pause*(i-1).. ",\\" ..SUERTE..")"..""  end  return RESULTADO  end
```

![image](https://github.com/RcUchiha/dc-antiperrys/assets/16442041/a8bdf4d5-07da-45f7-add3-c15ee7aa38bd)

Se utiliza para lograr un efecto de cambio de ida y vuelta con tiempo de pausa adicional. El cuarto parámetro adicional, "Pause", representa el tiempo de pausa en milisegundos (ms).

`AutoTags1(duración del cambio, "tag1", "tag2", tiempo de pausa)`

**Variante 2**
```lua
function AutoTags2(Intervalo,Dato1,Dato2,Delay)            local RESULTADO=""     local SUERTE = 0     local CONTADOR = 0      local ARREGLO = Layer                            local count = math.ceil(line.duration/Intervalo)                                         ARREGLO = {Dato1,Dato2}                                          for i = 1, count do               CONTADOR = i                                            if Dato1 and Dato2 then                                             if  CONTADOR%2 ==0 then                                                                    SUERTE = ARREGLO[1]                                            else                                                                    SUERTE = ARREGLO[2]                                            end                            end                                                 RESULTADO = RESULTADO .."\\t(" ..(i-1)*Intervalo+Delay.. "," ..i*Intervalo+Delay.. ",\\" ..SUERTE.. ")"..""                                  end                              return RESULTADO                                             end
```

Se utiliza para lograr un efecto de cambio de ida y vuelta con retraso (en relación al tiempo de inicio de la línea).
![image](https://github.com/RcUchiha/dc-antiperrys/assets/16442041/c077de86-282c-4a8c-9183-4849f1f4e545)
Por ejemplo, se necesita que el efecto de cambio ocurra 1 segundo después del inicio de la línea:
`AutoTags2(500, "tag1", "tag2", 1000)`

**Variante3**
```lua
function AutoTags3(Intervalo1,Intervalo2,Dato1,Dato2)  local RESULTADO=""                 local SUERTE = 0                 local CONTADOR = 0                 local ARREGLO = 0                               local count = 2*math.ceil(line.duration/(Intervalo1+Intervalo2))            local d=math.ceil((Intervalo2-Intervalo1)/count)  local t={}  ARREGLO = {Dato1,Dato2}                                                      for i = 1, count do                                   CONTADOR = i  t[1]=0  t[i+1]=t[i]+Intervalo1+(i-1)*d  if Dato1 and Dato2 then            if  CONTADOR%2 ==0 then                                                                                        SUERTE = ARREGLO[1]            else                                                                                        SUERTE = ARREGLO[2]                                                        end            end                                                             RESULTADO = RESULTADO .."\\t(" ..t[i].. "," ..t[i+1].. ",\\" ..SUERTE..")"..""                                              end                                          return RESULTADO                                                         end
```

AutoTags en forma de una progresión aritmética (con el tiempo de cambio aumentando o disminuyendo).
![image](https://github.com/RcUchiha/dc-antiperrys/assets/16442041/06e0fdea-64cc-4a90-b461-db1754752350)
El efecto de decremento es similar a un temporizador de bomba, volviéndose cada vez más rápido. Mientras que el efecto de incremento indica que la velocidad de cambio se vuelve cada vez más lenta.

`AutoTags3(tiempo inicial del cambio, tiempo final del cambio, "tag1", "tag2")` 

Si `tiempo inicial del cambio` > `tiempo final del cambio`, es un efecto de decremento. Si `tiempo final del cambio` > `tiempo inicial del cambio`, es un efecto de incremento.

El archivo ASS está adjunto:
[AutoTags.zip](https://github.com/RcUchiha/dc-antiperrys/files/14785178/AutoTags.zip)


Por el momento, solo puedo pensar en estas pocas variantes: progresión geométrica, alternancia entre dos conjuntos de tiempos también es una buena idea. ¡Bienvenidos todos a contribuir! 

