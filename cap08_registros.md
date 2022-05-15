# Capítulo 8: Registros

Aprender los registros de Vim es como aprender álgebra por primera vez. No piensas que los necesitas hasta que los necesitas.

Probablemente has usado los registros de Vim al copiar o borrar un texto y luego lo pegaste con `p` o `P`. Sin embargo, ¿sabías que Vim tiene 10 tipos diferentes de registros? Usados correctamente, los registros de Vim te pueden ahorrar el tener que escribir lo mismo de manera repetitiva.

En este capítulo repasaré todos los tipos de registros de Vim y cómo usarlos eficientemente.

## Los diez tipos de registros

Estos son los 10 tipos de registros que tiene Vim:

1. El registro sin nombre \(`""`\).
2. Los registros numerados \(`"0-9`\).
3. El registro de pequeñas eliminaciones \(`"-`\).
4. Los registros nominales \(`"a-z`\).
5. El registro de solo lectura \(`":`, `".`,y `"%`\).
6. El registro de archivo alternativo \(`"#`\).
7. El registro de expresiones \(`"=`\).
8. Los registros de selección \(`"*` y `"+`\).
9. El registro de agujero negro \(`"_`\).
10. El registro del último patrón de búsqueda \(`"/`\).

## Operadores del registro

Para utilizar los registros, necesitas primero almacenarlos con operadores. Estos son algunos operadores que almacenan valores en los registros:

```text
y    Yank (copiar)
c    Borrar texto e iniciar el modo insertar
d    Borrar texto
```

Existen algunos operadores más \(como `s` o `x`\), pero estos son los más comunes. El criterio general es, si un operador elimina texto, lo más probable es que almacene el texto en los registros.

Para pegar texto desde los registros, puedes usar:

```text
p    Pega el texto después del cursor
P    Pega el texto antes del cursor
```

Ambos `p` y `P` aceptan un contador y un símbolo de registro como argumentos. Por ejemplo, para pegar diez veces, debes escribir `10p`. Para pegar el texto del registro a, utiliza `"ap`. Para pegar 10 veces el texto del registro a, ejecuta `10"ap`. Por cierto, la `p` del comando es la abreviación de la palabra "put" \(poner en inglés\), no de "paste" \(pegar en inglés\), pero creo que usar la palabra pegar es más conveniente.

La sintaxis general para obtener el contenido desde un registro en específico es `"a`, donde `a` es el símbolo del registro.

## Llamar a los registros desde el modo insertar

Todo lo que aprendas en este capítulo puede también ser ejecutado en el modo insertar. Para obtener el texto del registro a, normalmente debes escribir `"ap`. Pero si estás en el modo insertar, ejecuta `Ctrl-R a`. La sintaxis para llamar a los registros desde el modo insertar es la siguiente:

```text
Ctrl-R a
```

Donde `a` es el símbolo del registro. Ahora que sabes cómo almacenar y recuperar registros ¡vamos a profundizar!

## El registro sin nombre

Para obtener texto desde el registro sin nombre, escribe `""p`. Este almacena el último texto que copiaste, modificaste o borraste. Si haces otra copia, modificación o borrado, Vim automáticamente reemplazará el texto. El registro sin nombre es como la operación copiar/pegar estándar de una computadora.

De manera predeterminada, `p` \(o `P`\) está conectado al registro sin nombre \(desde ahora me referiré al registro sin nombre con `p` en lugar de `""p`\).

## Los registros numerados

Los registros numerados automáticamente se llenan a sí mismos en orden ascendente. Hay 2 registros numerados diferentes: el registro de copia \(`0`\) y los registros numerados \(`1-9`\). Vamos a ver primero el registro de copia.

### El registro de copia

Si copias una línea entera de texto \(`yy`\), Vim realmente guarda ese texto en dos registros:

1. El registro sin nombre \(`p`\).
2. El registro de copia \(`"0p`\).

Cuando copias un texto diferente, Vim remplaza ambos registros, el de copia y el de sin nombre. Cualquier otra operación \(como eliminar\) no será almacenada en el registro 0. Esto puede ser usado para tu provecho, porque a menos que no hagas otra copia, el texto copiado permanecerá siempre allí, no importa cuantos cambios y borrados hagas.

Por ejemplo, si haces lo siguiente:

1. Copiar una línea \(`yy`\)
2. Borrar una línea \(`dd`\)
3. Borrar otra línea \(`dd`\)

El registro de copia tendrá el texto del primer paso.

Pero si en cambio haces esto otro:

1. Copias una línea \(`yy`\)
2. Borras una línea \(`dd`\)
3. Copias otra línea \(`yy`\)

El registro de copia tendrá el texto del paso tres.

Un último consejo, mientras estás en el modo insertar, puedes pegar rápidamente el texto que acabas de copiar usando `Ctrl-R 0`.

### Los registros numerados que no son el cero

Cuando cambias o borras un texto de al menos una línea de largo, este texto se almacenará en un registro numerado del 1 al 9, ordenados por el más reciente.

Por ejemplo, si tienes estas líneas:

```text
línea tres
línea dos
línea uno
```

Con tu cursor en la "línea tres" borra una a una las líneas con `dd`. Una vez que las líneas están borradas, el registro 1 debe contener "línea uno" \(el texto más reciente\), registro 2 la "línea dos" \(el segundo texto más reciente\) y el registro 3 "línea tres" \(el más antiguo\). Para obtener el contenido del registro uno, escribe `"1p`.

Como nota complementaria, los registros numerados son incrementados automáticamente cuando usas el comando punto. Si tu registro numerado uno \(`"1`\) contiene "línea uno", registro dos \(`"2`\) "línea dos" y el registro tres \(`"3`\) "línea tres", puedes pegarlos secuencialmente con este truco:

* Escribe `"1P` para pegar el contenido del registro del número uno.
* Escribe `.` para pegar el contenido del registro numerado dos \(`"2`\).
* Escribe `.` para pegar el contenido del registro numerado tres \(`"3`\).

Este truco funciona con cualquier registro numerado. Si empiezas con `"5P`, `.` hará `"6P`, otra vez `.` hará `"7P` y así sucesivamente.

Pequeños borrados como un borrado de palabras \(`dw`\) o un cambio de palabras \(`cw`\) no se almacenan en registros numerados. Estos se almacenan en un pequeño registro de borrado \(`"-`\), el cual es el siguiente a tratar.

## El pequeño registro de borrado

Los cambios o borrados menores a una línea no se almacenan en los registros numerados del 0-9, pero si en el pequeño registro de borrado \(`"-`\).

Por ejemplo: 1. Borrar una palabra \(`diw`\) 2. Borrar una línea \(`dd`\) 3. Borrar una línea \(`dd`\)

`"-p` te da la palabra borrada en el primer paso.

Otro ejemplo: 1. Yo borro una palabra \(`diw`\) 2. Yo borro una línea \(`dd`\) 3. Yo borro una línea \(`diw`\)

`"-p` te da la palabra borrada en el paso tres. Igualmente, `"1p` te da la línea borrada del paso dos. Desafortunadamente, no hay manera de recuperar la palabra borrada del paso uno porque el registro de borrado pequeño solo guarda un elemento. Sin embargo, si deseas preservar el texto del paso uno, puedes hacerlo con los registros nominales.

## Los registros nominales

Los registros nominales son los registros más versátiles de Vim. Estos pueden almacenar textos copiados, modificados y borrados dentro de los registros a-z. A diferencia de los 3 tipos de registros anteriores que has visto, que pueden almacenar automáticamente texto dentro de los registros, aquí tienes que decirle explícitamente a Vim el nombre del registro a usar, brindándote así control total.

Para copiar una palabra dentro del registro a, puedes hacerlo con `"ayiw`.

* `"a` le dice a Vim que la siguiente acción \(borrar / modificar / copiar\) será almacenada en el registro a.
* `yiw` copia la palabra.

Para obtener el texto del registro a, ejecuta `"ap`. Puedes usar todos los veintiséis caracteres del alfabeto para almacenar veintiséis textos diferentes dentro de los registros nominales.

A veces, puedes querer aumentar un registro nominal existente. En este caso, puedes agregar tu texto al final en lugar de empezar de nuevo. Para hacer eso, puedes usar la versión mayúscula del registro. Por ejemplo, supón que tienes ya almacenada la palabra "Hola " en el registro a. Si deseas agregar "mundo" en el registro a, puedes buscar el texto "mundo" y copiarlo usando el registro A \(`"Ayiw`\).

## Los registros de solo lectura

Vim tiene tres registros de solo lectura: `.`, `:`, y `%`. Estos son muy fáciles de usar:

```text
.    Almacena el último texto insertado
:    Almacena lo último que fue ejecutado en la línea de comandos
%    Almacena el nombre del archivo actual
```

Si el último texto que escribiste fue "Hola Vim", al ejecutar `".p` se imprimirá el texto "Hola Vim". Si deseas obtener el nombre del archivo actual, escribe `"%p`. Si ejecutas el comando `:s/foo/bar/g`, al ejecutar `":p` se mostrará el texto literal "s/foo/bar/g".

## El registro de archivo alternativo

En Vim, `#` usualmente representa el archivo alternativo. Un archivo alternativo es el último archivo que abriste. Para insertar el nombre del archivo alternativo, puedes usar `"#p`.

## El registro de expresiones

Vim tiene un registro de expresiones, `"=`, para evaluar expresiones.

Puedes calcular la expresión matemática `1 + 1` con:

```text
"=1+1<Enter>p
```

En el ejemplo anterior, le dices a Vim que estás usando el registro de expresiones con `"=`. Tu expresión es \(`1 + 1`\). Luego necesitas escribir `p` para obtener el resultado. Como mencioné anteriormente, puedes acceder al registro desde el modo insertar. Para calcular una expresión matemática desde el modo insertar puedes hacer lo siguiente:

```text
Ctrl-R =1+1
```

Puedes obtener los valores de cualquier registro usando el registro de expresiones y añadiéndole `@`. Si deseas obtener el texto desde el registro a:

```text
"=@a
```

Luego pulsa `<Enter>`, después `p`. Igualmente, para obtener valores desde el registro a mientras estas en el modo insertar:

```text
Ctrl-R =@a
```

Las expresiones son un tema muy amplio en Vim, así que aquí solo se trata lo básico. Volveré al tema de las expresiones en más detalle en los capítulos finales de Vimscript.

## Los registros de selección

¿No desearías a veces que pudieras copiar un texto de programas externos a Vim y poder pegarlo en Vim y viceversa? Con los registros de selección de Vim, puedes hacerlo. Vim tiene dos registros de selección: `quotestar` (`"*`) y `quoteplus` (`"+`). Puedes utilizarlos para acceder a texto copiado desde programas externos.

Si estás en un programa externo (por ejemplo en el navegador) y copias un bloque de texto con `Ctrl-C` (o `Cmd-C`, dependiendo de tu sistema operativo), normalmente no podrías utilizar `p` para pegar el texto en Vim. Sin embargo, en Vim tanto `"+` como `"*` están conectados con tu porta papeles, así que sí podrías pegar el texto utilizando esos registros mediante `"+p` o `"*p`. En cambio, si copias una palabra desde Vim con `"+yiw` o `"*yiw`, podrás pegar ese texto en el programa externo con `Ctrl-V` (o `Cmd-V`). Ten en cuenta que esto solo funciona si tu Vim viene con la opción `+clipboard`. Para comprobarlo, ejecuta `:version`.

Te podrías preguntar que si `"*` y `"+` hacen lo mismo, ¿por qué Vim tiene dos registros diferentes? Algunos equipos utilizan un sistema de ventanas X11. Este sistema tiene 3 tipos de selecciones: primario, secundario y porta papeles. Si tu equipo utiliza X11, Vim utiliza la selección *primario* de X11 con el registro `quotestar` (`"*`) y la selección de *portapapeles* de X11 con el registro `quoteplus` (`"+`). Esto solo es aplicable si tienes la opción `+xterm_clipboard` disponible en tu versión de la compilación de Vim. Si tu Vim no tiene `xterm_clipboard`, no es un gran problema. Solo significa que tanto `quotestar` como `quoteplus` son intercambiables (los míos no lo son).

Creo que `=*p` o `=+p` (o `"*p` o `"+p`) es un poco incómodo. Para hacer que Vim pegue el texto copiado de un programa externo con solo `p`, puede añadir esto en tu archivo vimrc:

```
set clipboard=unnamed
 ```

Ahora cuando copies un texto desde un programa externo, podrás pegarlo con el registro sin nombre, `p`. También podrás copiar un texto desde Vim y pegarlo en un programa externo. Si tienes `+xterm_clipboard` activado, quizás quieras utilizar tanto las opciones del portapapeles `unnamed` como `unnamedplus`.

## El registro de agujero negro

Cada vez que eliminas o cambias un texto, ese texto es almacenado en un registro de Vim automáticamente. Habrá ocasiones que no querrás guardar nada en el registro. ¿Cómo hacer eso?

Puedes utilizar el registro de agujero negro (`"_`). Para eliminar una línea y hacer que Vim no almacene esa línea eliminada en ningún registro, utiliza `"_dd`.

El registro del agujero negro es como el `/dev/null` de los registros.

## El registro del último patrón de búsqueda

Para pegar tu última búsqueda (con `/` o `?`), puedes utilizar el registro del último patrón de búsqueda (`"/`). Para pegar el término de la última búsqueda utiliza `"/p`.

## Ver los registros

Para ver el contenido de tus registros, utiliza el comando `:register`. Para ver solo los registros "a, "1 y "-, utiliza `:register a 1 -`.

Hay un complemento llamado [vim-peekaboo](https://github.com/junegunn/vim-peekaboo) que te permite escoger entre el contenido de los registros cuando pulsas `"` o `@` en el modo normal o con `Ctrl-R` en el modo insertar. Creo que este complemento puede ser muy útil porque la mayoría de las veces quizás no recuerdes lo que almacena cada registro. ¡Pruébalo!

## Ejecutando un registro

Los registros nominales no son solo para almacenar textos. También pueden ejecutar macros mediante `@`. Trataremos el tema de las macros en el siguiente capítulo.

Ten en mente que como las macros están almacenadas en registros de Vim, puedes de manera accidental sobre escribir el texto almacenado con macros. Si almacenas el texto "Hola Vim" en el registro a y después grabas una macro en el mismo registro con (`qa{secuecia_de_macro}q`), esa macro sobre escribirá tu texto "Hola Vim" almacenado previamente.

## Limpiando el contenido de un registro

Técnicamente, no hay necesidad de limpiar el contenido de un registro porque el próximo texto que almacenes bajo el mismo nombre de un registro lo sobre escribirá. Sin embargo, puedes limpiar el contenido rápidamente de cualquier registro grabando una macro vacía. Por ejemplo, si ejecutas `qaq`, Vim grabará una macro vacía en el registro a.

Otra alternativa es ejecutar el comando `:call setreg('a', 'hola registro a')` donde a hace referencia al registro a y "hola registro a" es el nuevo texto que sobre escribirá el anterior texto almacenado en el registro. También puedes simplemente guardar una espacio en blanco en vez de un texto.

Una forma más de limpiar el contenido de un registro es guardar una cadena vacía en el registro (por ejemplo el "a) con la siguiente expresión `:let @a = ''`.

## Pegando el contenido de un registro

Puedes utilizar el comando `:put` para pegar el contenido de cualquier registro. Por ejemplo, si ejecutas `:put a`, Vim mostrará el contenido a bajo la línea actual. Esto se parece mucho a `"ap`, con la diferencia que este comando pega el contenido después del cursor y el comando `:put`  pega el contenido del registro en una nueva línea.

Como `:put` es un comando de la línea de comando, puedes pasarle una dirección. `:10put a` pegará el texto del registro a debajo de la línea 10.

Un truco que se puede utilizar aprovechando esto es pegar con `:put` el contenido del registro del agujero negro (`"_`). Como este registro no almacena nada, al ejecutar `:put _` esto insertará una línea en blanco.

Puedes combinar esto con el comando global para insertar múltiples líneas en blanco donde lo necesites. Por ejemplo, para insertar líneas en blanco debajo de todas las líneas en tu texto que contengan el texto "fin", ejecuta `:g/fin/put _`. Aprenderás más sobre el comando global más adelante.

## Aprendiendo los registros de la manera más inteligente

Lo conseguiste. ¡Enhorabuena! Si sientes una sensación de agobio por la cantidad de información, no te pasa a ti únicamente. Cuando comencé a aprender sobre los registros de Vim, había mucha información que asimilar de una vez.

No creo que tengas que memorizar los registros de manera inmediata. Para ser más eficaz, puedes empezar por utilizar solo estos 3 registros:

1. El registro sin nombre \(`""`\).
2. 2. Los registro nominales \(`"a-z`\).
3. 3. Los registros numerados \(`"0-9`\).

Como el registro sin nombre de manera predeterminada usa `p` y `P`, solo tienes que aprender dos registro: los nominales y los numerados. Aprende de manera gradual más registros cuando los necesites. Tómate tu tiempo.

Una persona de media tiene una capacidad limitada de memoria a corto plazo, sobre unos 5 a 7 elementos a la vez. Por eso en mi trabajo diario con Vim, solo utilizo unos 5 o 7 registros nominales. No hay manera de que pueda recordar todos los 26. Normalmente comienzo con el registro a, después el b, ascendiendo en orden alfabético. Inténtalo y experimenta sobre ello para ver qué técnicas funcionan mejor en tu caso.

Los registros de Vim son muy potentes. Utilízalos estratégicamente, te pueden ahorrar el tener que escribir de manera repetitiva mucho texto. Ahora vamos a aprender sobre las macros.

