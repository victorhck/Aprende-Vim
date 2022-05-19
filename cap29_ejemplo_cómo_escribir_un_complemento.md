# Artículo todavía en fase de traducción
# Capítulo 29: Cómo escribir tu propio complemento en Vim

Cuando comienzas a dominar Vim, quizás quieras empezar a escribir tus propios complementos. Recientemente, el autor de la guía en inglés escribió su primer complemento para Vim [totitle-vim](https://github.com/iggredible/totitle-vim). Es un complemento operador para los títulos de secciones o capítulos de texto, similar a los operadores de Vim para convertir a mayúsculas `gU`, minúsculas `gu` o intercambiarlos`g~`.

En este capítulo verás detallado el proceso de creación del complemento `totitle-vim`. ¡Espero arrojar algo de luz sobre el proceso y tal vez inspirarte a crear tu propio complemento único!

## El problema

**Nota del traductor:** *En la cultura anglosajona, los títulos de artículos escritos o secciones suelen escribirse con la primera letra de las palabras en mayúsculas. Algo que en español no ocurre, ya que solo se escriben en mayúsculas las palabras que hacen referencia a nombres propios, después de un punto y alguna otra ocasión tal como establece la [RAE](https://www.rae.es/dpd/may%C3%BAsculas).

Por tanto el complemento tiene sentido únicamente en textos en inglés. Pero independientemente de esa circunstancia, será igual de útil ver el proceso de creación para crear nuestros propios complementos para nuestras necesidades de edición personales.*

Utilizo Vim para escribir mis artículos, incluyendo esta guía que estás leyendo.

Uno de los problemas pincipales fue crear un adecuado uso de mayúsculas y minúsculas para los títulos. Una manera de automatizar esto es convertir a mayúsculas cada letra de cada palabra mediante `g/^#/ s/\<./\u\0/g`. Para un uso básico, este comando puede ser suficiente, pero no es lo suficientemente potente como el tener una forma de convertir a mayúscula la primera letra de un título. En inglés las palabras "The" o "Of" en "Capitalize The First Letter Of Each Word" deberían escribirse en mayúsculas. Sin una regla adecuada de capitalización de palabra, la frase tendría un aspecto extraño.

Al inicio, no planeaba escribir un complemento. Además resulta que ya existe un complemento para eso llamado: [vim-titlecase](https://github.com/christoomey/vim-titlecase). Sin embargo, hay unas cuantas cosas que no funcionaban en la manera en la que yo quería. La principal era el comportamiento en el modo de selección visual de bloque. Si tienes las siguientes frases:

```
test title one
test title two
test title three
```

Si utilizo el reslatado de bloque visual en las letras "tle" en las tres líneas, quedando seleccionado la parte que está entre corchetes, así:

```
test ti[tle] one
test ti[tle] two
test ti[tle] three
```

Si pulso `gt`, el complemento no convertirá en mayúsculas la selección. Este comportamiento no sigue el comportamiento que tienen `gu`, `gU` y `g~`. Así que decidí trabajar a partir del repositorio del complemento y utilizarlo para solucionar ese comportamiento y que fuera igual a `gu`, `gU` y `g~`! Una vez más, el complemento vim-titlecase en sí mismo es un complemento excelente y digno de ser utilizado por sí solo (la verdad es que tal vez en el fondo solo quería escribir mi propio complemento de Vim. Realmente no puedo imaginar en qué casos la función de título en bloques pueda ser usado a menudo en la vida real que no sean casos extremos).

### Planificando el complemento

Antes de escribir la primera línea de código, necesito decidir cuales serán las reglas para poner en mayúsculas las líneas de los títulos. Encontré una tabla en la que se especifican las diferentes reglas a la hora de escribir mayúsculas en el sitio web [titlecaseconverter](https://titlecaseconverter.com/rules/). ¿Sabías que hay al menos 8 reglas diferentes en inglés relativas a la escritura con letras mayúsculas? *¡Casi nada!*

Al final, utilicé los denominadores comunes de esa lista para formar unas cuantas reglas básicas válidas para el complemento. Además también dudé de que la gesnte pudiera quejarse diciendo "Oye, estás utilizando la regla AMA, por qué no utilizas APA?" Aquí están las reglas básicas:

- La primera palabra siempre empieza por mayúscula.
- Algunos adverbios, conjunciones y preposiciones son con minúsculas.
- Si la palabra a tratar está completamente en mayúsculas, entonces no hagas ada (podría ser una abreviatura).

Y sobre cuales palabra van en minúsculas, en diferentes reglas tienen diferentes listas. Decidí que estas irían en minúsculas `a an and at but by en for in nor of off on or out per so the to up yet vs via`.

### Planificando la interfaz de usuario

Quiero que el complemento sea un operador que complemente los operadores ya existentes de Vim: `gu`, `gU`, and `g~`. Al ser un operador, podrá aceptar tanto movimientos como objetos de texto (`gtw` debería poner en mayúscula la palabra siguiente, `gtiw` debería poner en mayúsculas la palabra donde se encuentra el cursor, `gt$` debería poner en mayúsculas las primeras letras de las palabras desde la ubicación actual del cursor hasta el final de la línea, `gtt` debería poner en mayúsculas la línea actual, `gti(` debería convertir a mayúsculas el texto dentro de los paréntesis, etc.) También quiero que tenga la combinación de teclas `gt` para recordarlo más fácilmente. Además, también debería funcionar en todos los modos visuales: `v`, `V` y `Ctrl-V`. Debería ser capaz de resaltar el texto en *cualquier* modo visual, pulsar `gt`, y después el complemento convertir a mayúsculas las primeras letras de todo el texto resaltado.

## Ejecutables en Vim

La primera cosa que ves cuando echas un vistazo al repositorio es que tiene dos directorios: `plugin/` y `doc/`. Cuando arrancas Vim, este busca archivos especiales y directorios dentro del directorio `~/.vim` y ejecuta todos los archivos de scripts dentro del directorio. Para más información, revisa el capítulo 24 de esta guía que trata sobre este tema.

El complemento utiliza dos directorios de ejecutables: `doc/` y `plugin/`. `doc/` es el lugar donde poner la ayuda de la documentación (para así poder buscar palabras claves más adelante, como `:h totitle`). Trataré sobre cómo crear la página de ayuda más adelante. Por ahora, vamos a enfocarnos en la carpeta `plugin/`. La carpeta `plugin/` es ejecutada una sola vez al inicio de Vim. Hay un solo archivo dentro de este directorio: `totitle.vim`. El nombre no importa (podría haber llamado el nombre `loquesea.vim` y seguiría funcionando). Todo el código responsable del funcionamiento del complemento está dentro de este archivo.

## Asignación de la combinación de teclas (mapeado)

¡Vayamos ya a ver el código! 

Al comienzo del archivo, te encuentras lo siguiente:

```
if !exists('g:totitle_default_keys')
  let g:totitle_default_keys = 1
endif
```

Cuando arrancas Vim, `g:totitle_default_keys` todavía no existirá, así que `!exists(...)` devuelve un valor verdadero. En ese caso, define `g:totitle_default_keys` igual a 1. En Vim, 0 es análogo a falso y cualquier valor distinto de cero es verdadero (en este caso utilizo el 1 para indicar verdadero).

Saltemos al final del archivo. Veremos lo siguiente:

```
if g:totitle_default_keys
  nnoremap <expr> gt ToTitle()
  xnoremap <expr> gt ToTitle()
  nnoremap <expr> gtt ToTitle() .. '_'
endif
```

Aquí es donde el _mapeado_ principal de `gt` es definido. En este caso, al llegar a los `if` condicionales al final del archivo, `if g:totitle_default_keys` devolverá un 1 (verdadero), así que Vim realizará los siguientes _mapeados_:
- `nnoremap <expr> gt ToTitle()` _mapea_ el *operador* del modo normal. Esto te permite ejecutar el operado + un movimiento u objeto de texto como `gtw` para convertir a mayúsculas la primera letra del título de la siguiente palabra, o `gtiw` para hacerlo de la palabra en la que se encuentra el cursor. Trataremos más en detalle los _mapeados_ del operador más adelante.
- `xnoremap <expr> gt ToTitle()` _mapea_ los operadores del modo visual. Esto te permite convertir a mayúsculas la primera letra del texto que está resaltado en el modo visual.
- `nnoremap <expr> gtt ToTitle() .. '_'` _mapea_ el operador en el modo de selección de línea (de manera análoga a `guu` y `gUU`). Quizás te preguntes qué hace `.. '_'` al final de esa línea. `..` En Vim es el operador de interpolación de cadenas de Vim. `_` es utilizado como un movimiento con un operado. Si miras en `:help _`, verás que el guión bajo es utilizado para contar 1 línea hacia abajo. realiza un operador en la línea actual (pruebalo con otros operadores, intenta ejecutar `gU_` o `d_`, verás que hace lo mismo que `gUU` o `dd`).
- Finalmente, el argumento `<expr>` te permite especificar el contaje, así podrás ejecutar por ejemplo `3gtw` para ejecutar el comando en las siguientes 3 palabras.

¿Qué pasa si no quieres utilizar el _mapead_ predeterminado `gt`? Después de todo, estás sobreescribiendo el _mapeado_ predeterminado que ya existe en Vim de `gt` (pestaña siguiente). ¿Qué pasa si quieres utilizar `gz` en vez de `gt`? Recuerdas cómo anteriormente se tomó la molestia de comprobar `if !exists('g:totitle_default_keys')` o `if g:totitle_default_keys`? Si pones `let g:totitle_default_keys = 0` en tu archivo vimrc, entonces `g:totitle_default_keys` ya existiría cuando el complemente esté ejecutándose (el código en tu vimrc son ejecutados antes de los archivos ejecutables `plugin/`), así `!exists('g:totitle_default_keys')` devolvería un falso. Además, `if g:totitle_default_keys` sería falso (porque tendría el valor de 0), ¡así que tampoco se realizaría el _mapeado_ de `gt`! Esto te permitirá de una forma efectiva definir tus propios _mapeados_ en el archivo Vimrc.

Para definir tu propio _mapeado_ a este complemento con `gz`, añade estas líneas en tu vimrc:

```
let g:totitle_default_keys = 0

nnoremap <expr> gz ToTitle()
xnoremap <expr> gz ToTitle()
nnoremap <expr> gzz ToTitle() .. '_'
```

¡Muy fácil!

## La función ToTitle 

La función `ToTitle()` es seguramente la función más larga en este archivo.

```
 function! ToTitle(type = '')
  if a:type ==# ''
    set opfunc=ToTitle
    return 'g@'
  endif

  " invoke this when calling the ToTitle() function
  if a:type != 'block' && a:type != 'line' && a:type != 'char'
    let l:words = a:type
    let l:wordsArr = trim(l:words)->split('\s\+')
    call map(l:wordsArr, 's:capitalize(v:val)')
    return l:wordsArr->join(' ')
  endif

  " save the current settings
  let l:sel_save = &selection
  let l:reg_save = getreginfo('"')
  let l:cb_save = &clipboard
  let l:visual_marks_save = [getpos("'<"), getpos("'>")]

  try
    set clipboard= selection=inclusive
    let l:commands = #{line: "'[V']y", char: "`[v`]y", block: "`[\<c-v>`]y"}

    silent exe 'noautocmd keepjumps normal! ' .. get(l:commands, a:type, '')
    let l:selected_phrase = getreg('"')
    let l:WORD_PATTERN = '\<\k*\>'
    let l:UPCASE_REPLACEMENT = '\=s:capitalize(submatch(0))'

    let l:startLine = line("'<")
    let l:startCol = virtcol(".")

    " when user calls a block operation
    if a:type ==# "block"
      sil! keepj norm! gv"ad
      keepj $
      keepj pu_

      let l:lastLine = line("$")

      sil! keepj norm "ap

      let l:curLine = line(".")

      sil! keepj norm! VGg@
      exe "keepj norm! 0\<c-v>G$h\"ad"
      exe "keepj " . l:startLine
      exe "sil! keepj norm! " . l:startCol . "\<bar>\"aP"
      exe "keepj " . l:lastLine
      sil! keepj norm! "_dG
      exe "keepj " . l:startLine
      exe "sil! keepj norm! " . l:startCol . "\<bar>"

    " when user calls a char or line operation
    else
      let l:titlecased = substitute(@@, l:WORD_PATTERN, l:UPCASE_REPLACEMENT, 'g')
      let l:titlecased = s:capitalizeFirstWord(l:titlecased)
      call setreg('"', l:titlecased)
      let l:subcommands = #{line: "'[V']p", char: "`[v`]p", block: "`[\<c-v>`]p"}
      silent execute "noautocmd keepjumps normal! " .. get(l:subcommands, a:type, "")
      exe "keepj " . l:startLine
      exe "sil! keepj norm! " . l:startCol . "\<bar>"
    endif
  finally

    " restore the settings
    call setreg('"', l:reg_save)
    call setpos("'<", l:visual_marks_save[0])
    call setpos("'>", l:visual_marks_save[1])
    let &clipboard = l:cb_save
    let &selection = l:sel_save
  endtry
  return
endfunction
```

Esto es muy largo, así que vamos a verlo por partes. 

*Podría refactorizar esto en secciones más pequeñas, pero por el bien de completar este capítulo, lo dejé como está.*

## La función operador

Esta es la primera parte del código:

```
if a:type ==# ''
  set opfunc=ToTitle
  return 'g@'
endif
```

¿Qué es eso de `opfunc`? ¿Por qué está devolviendo `g@`?

Vim tiene un operador especial, la función de operador, `g@`. Este operador te permite utilizar *cualquier* función asignada a la opción `opfunc`. Si tengo la función `Foo()` asignada a `opfunc`, entonces ejecutaré `g@w`, estoy ejecutando `Foo()` en la siguiente palabra. Si ejecuto `g@i(`, entonces estoy ejecutando `Foo()` entre los paréntesis interiores. Esta función de operador es crítica para crear tus propios operadores de Vim.

La siguiente línea asigna `opfunc` a la función `ToTitle`.

```
set opfunc=ToTitle
```

La siguiente línea de manera literal devuelve `g@`:

```
return g@
```

Entonces, ¿cómo funcionan exactamente estas dos líneas y por qué devuelven `g@`?

Vamos a asumir que tienes el siguiente _mapeado_:

```
nnoremap <expr> gt ToTitle()`
```

Entonces pulsas `gtw` (para poner en mayúsculas la primera letra de la siguiente palabra). La primera vez que ejecutas `gtw`, Vim llama al método `ToTitle()`. Pero justo ahora `opfunc` todavía está en blanco. Tampoco estás pasando ningún argumento a `ToTitle()`, así que `a:type` tendrá un valor de  `''`. Esto causa que la expresión condicional compruebe el argumento `a:type`, `if a:type ==# ''`, para ser cierto. Dentro, asignas `opfunc` a la función `ToTitle` con `set opfunc=ToTitle`. Ahora `opfunc` está asignada a `ToTitle`. Finalmente, después de asignar `opfunc` a la función `ToTitle`, devolverás `g@`. Explicaré porque devuleve el valor `g@` a continuación.

Todavía no has terminado. Recuerda, pulsaste `gtw`. Al pulsar `gt` hizo todo el proceso que se ha detallado anteriormente, pero todavía tienes que procesar `w`. Al devolver `g@`, en este punto, técnicamente tienes `g@w` (por eso es que tines `return g@`). Ya que `g@` es un operador de función, le estás pasando el movimiento `w`. Así que Vim, una vez que recibe `g@w`, llama a `ToTitle` *una vez más* (no te preocupes, no acabarás en un bucle infinito como verás en breve).

Para recapitular, al pulsar `gtw`, Vim comprueba si `opfunc` está vacía o no. Si está vacía, entonces Vim la asignará a `ToTitle`. Y entonces devolverá `g@`, esencialmente llamando a `ToTitle` de nuevo una vez más para ahora poder utilizarla como un operador. Esta es la parte más enrevesada de crear un operador personalizado ¡y lo hiciste! A continuación, necesitas crear la lógica para `ToTitle()` para que haga su cometido con la entrada.

## Procesando la entrada

Ahora ya tienes `gt` funcionando como un operador que ejecuta `ToTitle()`. Pero ¿qué es lo próximo a hacer? ¿Cómo realmente conviertes a mayúsculas la primera letra de las palabras del título?

Cada vez que ejecutas un operador en Vim, hay tres diferentes tipos de acciones de movimiento: caracter, línea y bloque. `g@w` (palabra) es un ejemplo de una operación de caracter. `g@j` (una línea inferior) es un ejemplo de operación de línea. La operación de bloque es inusual, pero se produce cuando ejecutas la operación `Ctrl-V` (bloque visual). Las operaciones que tienen como objetivo unos cuantos caracteres hacia adelante/atrás son básicamente consideradas operaciones de caracteres (`b`, `e`, `w`, `ge`, etc). Las operaciones que tienen como objetivo unas cuantas líneas superiores/inferiores son consideradas generalmente operaciones de línea (`j`, `k`). Las operaciones que tienen como objetivo columnas o bloques hacia adelante/atrás arriba/abajo son consideradas operaciones de bloque (por lo general, son un modo de movimiento forzado en columna o un modo visual en bloque, para más información consultar: `:h forced-motion`).

Esto significa, que si pulsas `g@w`, `g@` pasará una cadena literal `"char"` como un argumento a `ToTitle()`. Si ejecutas `g@j`, `g@` pasará una cadena literal `"line"` como argumento a `ToTitle()`. Esta cadena es lo que se le pasará a la función `ToTitle` como argumento `type`.

## Crear tu propio operador de función personalizado

Vamos a detenernos un poco y jugar con `g@` escribiendo una función un poco tonta:

```
function! Test(some_arg)
  echom a:some_arg 
endfunction
```

Ahora asigna esa función a `opfunc` ejecutando:

```
:set opfunc=Test
```

El operador `g@` ejecutará `Test(some_arg)` y pasará tanto `"char"`, `"line"`, o `"block"` dependiendo de la operación que realices. Ejecuta diferentes operaciones como `g@iw` (dentro de una palabra), `g@j` (una línea por debajo), `g@$` (hasta el final de la línea), etc. Comprueba los diferentes valores qu se muestran. Para comprobar una operación de bloque, puedes utilizar el movimiento forzado para operaciones de bloque: `g@Ctrl-Vj` (operación de bloque de una columna por debajo).

También puedes utilizar el modo visual. Utiliza los diferentes resaltados como `v`, `V`, y `Ctrl-V` y después pulsa `g@` (te aviso que mostrará la salida muy rápido, así que tendrás que tener una vista muy rápida, pero la salida del comando definitivamente está ahí. También como hemos utilizado `echom`, puedes comprobar los mensajes mostrados con `:messages`).

Bastante bien ¿no? ¡La de cosas que puedes pogramar con Vim! ¿Por qué no enseñan esto en las escuelas? Vamos a continuar con nuestro complemento.

## ToTitle como una función

Pasando a las siguientes líneas:

```
if a:type != 'block' && a:type != 'line' && a:type != 'char'
  let l:words = a:type
  let l:wordsArr = trim(l:words)->split('\s\+')
  call map(l:wordsArr, 's:capitalize(v:val)')
  return l:wordsArr->join(' ')
endif
```

Esta línea en realidad no tiene nada que ver con el comportamiento de un operador `ToTitle()`, sino para habilitarlo en una función TitleCase invocable (sí, sé que estoy violando el Principio de Responsabilidad Única). La motivación es, que Vim tiene unas funciones nativas llamadas `toupper()` y `tolower()` que convierten a mayúsculas y minúsculas respectivamente cualquier cadena de texto dada. Por ejemplo: `:echo toupper('hola')` devolverá `'HOLA'` y `:echo tolower('HOLA')` devolverá `'hola'`. Yo quiero que este complemento tenga la posibilidad de ejecutar `ToTitle` para poder hacer lo siguiente `:echo ToTitle('érase una vez')` y obtener `'Érase Una Vez'` como valor de retorno.

Por ahora, ya sabes que cuando llamas `ToTitle(type)` con `g@`, el argumento `type` tendrá un valor de retorno como `'block'`, `'line'`, o `'char`'. Si el argumento no es ninguna de esas opciones, puedes asumir con total seguridad que `ToTitle()` está siendo llamada fuera de `g@`. En ese caso, divídelos con espacios en blanco (`\s\+`) con:

```
let l:wordsArr = trim(l:words)->split('\s\+')
```

Y después convierte a mayúscula la primera letra de cada elemento:


```
call map(l:wordsArr, 's:capitalize(v:val)')
```

Antes únelo todo junto de nuevo:

```
l:wordsArr->join(' ')
```


La función `capitalize()` la veremos más adelante.

## Variables temporales

Las siguientes líneas:

```
let l:sel_save = &selection
let l:reg_save = getreginfo('"')
let l:cb_save = &clipboard
let l:visual_marks_save = [getpos("'<"), getpos("'>")]
```

Estas líneas guardan varios estados actuales en variables temporales. Más adelante usarás modos visuales, marcas y registros. Al hacer esto se modificarán algunos estados. Ya que no quieres volver a revisar el historial, necesitarás guardarlos en variables temporales para poder restaurar más tarde esos estado.

## Poniéndo en mayúsculas las primeras letras de las selecciones

Las siguientes líneas son importantes:

```
try
  set clipboard= selection=inclusive
  let l:commands = #{line: "'[V']y", char: "`[v`]y", block: "`[\<c-v>`]y"}

  silent exe 'noautocmd keepjumps normal! ' .. get(l:commands, a:type, '')
  let l:selected_phrase = getreg('"')
  let l:WORD_PATTERN = '\<\k*\>'
  let l:UPCASE_REPLACEMENT = '\=s:capitalize(submatch(0))'

  let l:startLine = line("'<")
  let l:startCol = virtcol(".")

```
Vamos a verlas en detalle dividiéndolas en partes. Esta línea:

```
set clipboard= selection=inclusive
```

Primero activas la opción `selection` para que sea inclusiva y `clipboard` para que esté vacío. El atributo de selección es normalmente utilizado con el modo visual y tiene tres posibles valores: `old`, `inclusive` y `exclusive`. Al establecerlo como `inclusive` esto significa que el último caracter de la selección está incluido. No lo trataré aquí, pero la clave es que al escogerlo como `inclusive` esto hace que tenga un comportamiento consistente en el modo visual. De manera predeterminada Vim activa esta opción, pero también la establecemos aquí por si alguno de tus complementos lo estableces a un valor diferente. Echa un vistazo a `:h 'clipboard'` y `:h 'selection'` si tienes curiosidad que qué hacen realmente.

A continuación, tienes este _hash_ de aspecto extraño seguido de un comando de ejecución:

```
let l:commands = #{line: "'[V']y", char: "`[v`]y", block: "`[\<c-v>`]y"}
silent exe 'noautocmd keepjumps normal! ' .. get(l:commands, a:type, '')
```

Primero, la sintaxis `#{}` es el tipo de datos del diccionario de Vim. La variable local `l:commands` es un _hash_ con 'lines', 'char' y 'block' como sus teclas. El comando `silent exe '...'` ejecuta cualquier comando dentro de la cadena de manera silenciosa (de lo contrario se mostraría una notificación en la parte inferior de la pantalla). 

Segundo, los comandos ejecutados son `'noautocmd keepjumps normal! ' .. get(l:commands, a:type, '')`. El primero, `noautocmd`, ejecutará el siguiente comando cuando sin ser lanzado por cualquier comando automático. El segundo, `keepjumps`, es para no guardar el movimiento del cursor mientras se está moviendo. En Vim, ciertos movimientos son grabados de manera automática en la lista de cambios y la lista de marcas. Esto, previene ese comportamiento. La razón de tener `noautocmd` y `keepjumps` es para prevenir efectos secundarios. Finalmente, el comando `normal` ejecuta las cadenas como comandos normales. Esto `..` en Vim es una sintaxis de interpolación de cadena. `get()` es una método de obtención que acepta tanto una lista, un _blob_ o un diccionario. en este caso, se le está pasando el diccionario `l:commands`. La clave es `a:type`. Has aprendido anteriormente que `a:type` puede ser cualquiera de estos tres valores `char`, `line` o `block`. Así que si `a:type` es una `line`, ejecutarás `"noautocmd keepjumps normal! '[V']y"` (para más información, echa un vistazo a `:h silent`, `:h :exe`, `:h :noautocmd`, `:h :keepjumps`, `:h :normal` y `:h get()`).

Vamos ahora a ver qué realiza `'[V']y`. Primero vamosa asumir que tienes el siguiente texto:

```
the second breakfast
is better than the first breakfast
```
Asumamos que tu cursor está en la primera línea. A continuación pulsa `g@j` (ejecuta la función operados, `g@`, una línea por debajo, mediante `j`). `'[` mueve el cursor al comienzo del texto anteriormente cambiado o copiado. Aunque técnicamente no has cambiado o no has copiado ningún texto con `g@j`, Vim recuerda la ubicación del inicio y final de los movimientos del comando `g@` con `'[` y `']` (para más información, consulta la ayuda `:h g@`). En tu caso, al pulsar `'[` mueve el cursor a la primera línea porque ahí es donde empezaste a ejecutar `g@`. `V` es el comando del modo visual de selección de línea. Finalmente `']` mueve tu cursor al final del texto previamente cambiado o copiado, pero en este caso, mueve el cursor al final de la última operación de `g@`. Finalmente, `y` copia el texto seleccionado. 

Lo que has hecho es copiar la misma parte del texto que has realizado con `g@`.

Si echas un vistazo a los otros dos comandos:

```
let l:commands = #{line: "'[V']y", char: "`[v`]y", block: "`[\<c-v>`]y"}
```

Todos realizan acciones similares, excepto en vez de utilizar las acciones de selección de línea, utilizarías acciones de selección de bloque. Voy a sonar redundante, pero en cualquiera de los tres casos estás copiando la misma parte del texto que realizaste con `g@`.

Vamos a ver la siguiente línea:

```
let l:selected_phrase = getreg('"')
```

Esta línea obtiene el contenido del registro sin nombre (`"`) y lo almacena dentro de la variable `l:selected_phrase`.  Espera un minuto... ¿no habías copiado ya el cuerpo del texto? El registro sin nombre contiene el texto que habías copiado. Así es como el complemento es capaz de obtener la copia del texto.

La línea siguiente es un patrón de expresión regular:

```
let l:WORD_PATTERN = '\<\k*\>'
```

`\<` y `\>` son patrones de límites de palabras. El caracter que sigue a `\<` marca el comienzo de una palabra y el caracter precedente `\>` marca el final de una palabra. `\k` es el patrón de palabra clave. Puedes comprobar qué caracteres acepta Vim como palabras clave mediante `:set iskeyword?`. Recuerda que el movimiento `w` en Vim mueve tu cursor una palabra hacia adelante. Vim viene con una noción preconcebida de lo que es una "palabra clave" (incluso puede editarla modificando la opción `iskeyword`). Echa un vistazo a `:h /\<`, `:h /\>`, y `:h /\k`, y `:h 'iskeyword'` para saber más al respecto. Finalmente `*` significa cero o más del patrón siguiente.

A grandes rasgos, `'\<\k*\>'` encuentra una palabra. Si tienes la siguiente cadena de texto:

```
uno dos tres
```

Si lo combina con el patrón, obtendrá tres coincidencias: "uno", "dos" y "tres".

Finalmente, tienes otro patrón:

```
let l:UPCASE_REPLACEMENT = '\=s:capitalize(submatch(0))'
```

Recuerda que el comando de sustitución de Vim puede ser utilizado con una expresión mediante `\={tu-expresión}`. Por ejemplo, si quieres poner en mayúsculas la cadena de texto "donut" en la línea actual, puedes utilizar la función de Vim `toupper()`. Puedes realizar esto ejecutando `:%s/donut/\=toupper(submatch(0))/g`. `submatch(0)` es una expresión especial utilizada en el comando de sustitución. Devuelve todo el texto encontrado.

Las siguientes dos líneas:

```
let l:startLine = line("'<")
let l:startCol = virtcol(".")
```

La expresión `line()` devuelve un número de línea. Aquí se lo pasas con la marca `'<`, que representa la primera línea de la última área visual seleccionada. Recuerda que utilizaste el modo visual para copiar el texto. `'<` devuelve el número de línea del principio de esa área de selección visual. La extpresión `virtcol()` devuelve el número de columna de la posición actual del cursor. Dentro de un rato moverá el cursor por todas partes, por lo que debe almacenar la ubicación del cursor para poder volver aquí más tarde.

Tómate tu tiempo para revisar todos los conceptos y asimilarlo. Asegúrate de entenderlo y cuando te sientas preparado continuemos.

## Gestión de una operación de bloque

Vamos ahora a ver la siguiente sección:

```
if a:type ==# "block"
  sil! keepj norm! gv"ad
  keepj $
  keepj pu_

  let l:lastLine = line("$")

  sil! keepj norm "ap

  let l:curLine = line(".")

  sil! keepj norm! VGg@
  exe "keepj norm! 0\<c-v>G$h\"ad" 
  exe "keepj " . l:startLine
  exe "sil! keepj norm! " . l:startCol . "\<bar>\"aP"
  exe "keepj " . l:lastLine
  sil! keepj norm! "_dG
  exe "keepj " . l:startLine
  exe "sil! keepj norm! " . l:startCol . "\<bar>"
```

Ya es hora de convertir en mayúsculas la primera letra de tu texto. Recuerda que tenía `a:type` que puede ser tanto 'char', 'line', o 'block'. En la mayoría de los casos, lo más probable es que tengas 'char' o 'line'. Pero quizás alguna vez también pueda ser un bloque. No es habitual, pero puede ocurrir. Desafortunadamente, la gestión de un bloque no es tan sencillo como es para un caracter o una líne. Llevará un poco más de esfuerzo extra el hacerlo, pero es factible.

Antes de comenzar, vamos a ver un ejemplo de cómo obtener un bloque. Imaginemos que tenemos el siguiente texto:

```
pancake for breakfast
pancake for lunch
pancake for dinner
```

Vamos a imaginar que tenemos el cursor sobre la "c" de "pancake" en la primera línea. Ahora utilizas la selección de bloque en el modo visual (`Ctrl-V`) para seleccionar hacia abajo y la derecha la palabra "cake" en las tres líneas. Aquí está representada la selección visual con corchetes:

```
pan[cake] for breakfast
pan[cake] for lunch
pan[cake] for dinner
```

Cuando pulses `gt`, esto es lo que obtendrás. Convierte en mayúscula la primera letra de la palabra seleccionada:

```
panCake for breakfast
panCake for lunch
panCake for dinner

```
Estas son tu suposiciones básicas al utilizarlo: cuando reslatas las tres palabras "cakes" en "pancakes", le estás diciendo a Vim que tienes tres líneas de palabras que quieres resaltar. Esas palabras son "cake", "cake", y "cake". Y esperas obtener "Cake", "Cake", y "Cake".

Pasemos a los detalles de implementación. Las siguientes líneas tienen:

```
sil! keepj norm! gv"ad
keepj $
keepj pu_
let l:lastLine = line("$")
sil! keepj norm "ap
let l:curLine = line(".")
```

La primera línea:

```
sil! keepj norm! gv"ad
```

Recuerda que `sil!` sirve para ejecutar de manera silenciosa (silently) y `keepj` guarda el historial de saltos mientras te estás moviendo. Después ejecutas el comando normal `gv"ad`. `gv` selecciona el último texto resaltado visualmente (en el ejemplo, los tres 'cakes'). `"ad` borra el texto resaltado visual y lo almacena en el registro a. Como resultado de todo eso, ahora tienes:

```
pan for breakfast
pan for lunch
pan for dinner
```

Ahora tienes tres *bloques* (no líneas) de 'cakes' almacenados en el registro a. Esta distinción es importante. Cuando copias un texto con la selección de línea en el modo visual es diferente a copiar un texto seleccionando un bloque en el modo visual. Ten esto en mente porque verás esto mismo más adelante.

A continuación tienes:

```
keepj $
keepj pu_
```

`$` mueve el cursor a la última línea del fichero. `pu_` inserta una línea debajo de donde está el cursor. Quieres ejecutar todo esto con `keepj` ya que no quieres alterar el historial de saltos que guarda Vim.

A continuación almacenas el número de línea de tu última línea (`line("$")`) en la variable local `lastLine`.

```
let l:lastLine = line("$")
```

Después pegas el contenido del registro con `norm "ap`.

```
sil! keepj norm "ap
```

Ten en mente que esto está ocurriendo en la nueva línea que has creado debajo de la última línea del fichero, actualmente estás en la parte inferior de tu archivo. Al pegar ese contenido, tienen estos *bloques* de textos:

```
cake
cake
cake
```

A continuación, almacenas la ubicación de la línea actual donde se encuentra el cursor.

```
let l:curLine = line(".")
```

Ahora pasemos a las siguientes líneas del código:

```
sil! keepj norm! VGg@
exe "keepj norm! 0\<c-v>G$h\"ad"
exe "keepj " . l:startLine
exe "sil! keepj norm! " . l:startCol . "\<bar>\"aP"
exe "keepj " . l:lastLine
sil! keepj norm! "_dG
exe "keepj " . l:startLine
exe "sil! keepj norm! " . l:startCol . "\<bar>"
```

Esta línea:

```
sil! keepj norm! VGg@
```

`VG` las resalta en el modo visual de selección de línea, desde la línea actual hasta el final del archivo. Así que aquí estás resaltando los tres bloques del texto 'cake' con la selección de línea (recuerda que es distinto el modo de selección de bloque y de selección de línea). Ten en cuenta que la primera vez que pegaste los tres textos "cake", los pegaste como bloques. Ahora estás resaltando el texto, pero como líneas. Visto desde fuera puede parecer lo mismo, pero internamente, Vim sabe la diferencia entre uno y otro procedimiento a la hora de pegar texto.

```
cake
cake
cake
```

`g@` es la función operador, así que esencialmente estás haciendo una llamada recursiva a ella misma. ¿Por qué? ¿Qué logra esto?

Estás haciendo una llamada recursiva a `g@` y pasándole las tres líneas (después de ejecutar lo anterior con `V`, ahora tienes líneas, no bloques) del texto 'cake' así será gestionado por la otra parte del código (que veremos más adelante). El resultado de ejecutar `g@` es tener tres líneas de texto con la primera letra de la palabra en mayúsculas:

```
Cake
Cake
Cake
```

La siguiente línea:

```
exe "keepj norm! 0\<c-v>G$h\"ad"
```

Esto ejecuta el comando del modo normal de ir al inicio de la línea (`0`), utiliza el resaltado del modo visual de selección de bloque para ir a la última línea y al último caracter en esa línea (`<c-v>G$`). The `h` is to adjust the cursor (when doing `$` Vim moves one extra line to the right). Finally, you delete the highlighted text and store it in the register a (`"ad`).

The next line:

```
exe "keepj " . l:startLine
```

You move your cursor back to where the `startLine` was.

Next:

```
exe "sil! keepj norm! " . l:startCol . "\<bar>\"aP"
```

Being in the `startLine` location, you now jump to the column marked by `startCol`. `\<bar>\` is the bar `|` motion. The bar motion in Vim moves your cursor to the nth column (let's say the `startCol` was 4. Running `4|` will make your cursor jump to the column position of 4). Recall that you `startCol` was the location where you stored the column position of the text you wanted to titlecase. Finally, `"aP` pastes the texts stored in the register a. This puts the text back to where it was deleted before.

Let's look at the next 4 lines:

```
exe "keepj " . l:lastLine
sil! keepj norm! "_dG
exe "keepj " . l:startLine
exe "sil! keepj norm! " . l:startCol . "\<bar>"
```

`exe "keepj " . l:lastLine` moves your cursor back to the `lastLine` location from earlier. `sil! keepj norm! "_dG` deletes the extra space(s) that were created using the blackhole register (`"_dG`) so your unnamed register stays clean. `exe "keepj " . l:startLine` moves your cursor back to `startLine`. Finally, `exe "sil! keepj norm! " . l:startCol . "\<bar>"` moves your cursor to the `startCol` column.

These are all the actions you could've done manually in Vim. However, the benefit of turning these actions into reusable functions is that they will save you from running 30+ lines of instructions every single time you need to titlecase anything. The take home here is, anything that you can do manually in Vim, you can turn it into a reusable function, hence a plugin!

Here is what it would look like.

Given some text:

```
pancake for breakfast
pancake for lunch
pancake for dinner

... some text
```

First, you visually highlight it blockwise:

```
pan[cake] for breakfast
pan[cake] for lunch
pan[cake] for dinner

... some text
```

Then you delete it and store that text in register a:

```
pan for breakfast
pan for lunch
pan for dinner

... some text
```

Then you paste it at the bottom of the file:

```
pan for breakfast
pan for lunch
pan for dinner

... some text
cake
cake
cake
```

Then you capitalize it:

```
pan for breakfast
pan for lunch
pan for dinner

... some text
Cake
Cake
Cake
```

Finally, you put the capitalized text back:

```
panCake for breakfast
panCake for lunch
panCake for dinner

... some text
```

## Handling Line and Char Operations

You are not done yet. You've only addressed the edge case when you run `gt` on block texts. You still need to handle the 'line' and 'char' operations. Let's look at the `else` code to see how tthis is done.

Here are the codes:

```
if a:type ==# "block"
  # ... 
else
  let l:titlecased = substitute(@@, l:WORD_PATTERN, l:UPCASE_REPLACEMENT, 'g')
  let l:titlecased = s:capitalizeFirstWord(l:titlecased)
  call setreg('"', l:titlecased)
  let l:subcommands = #{line: "'[V']p", char: "`[v`]p", block: "`[\<c-v>`]p"}
  silent execute "noautocmd keepjumps normal! " .. get(l:subcommands, a:type, "")
  exe "keepj " . l:startLine
  exe "sil! keepj norm! " . l:startCol . "\<bar>"
endif
```

Let's go through them linewise. The secret sauce of this plugin is actually on this line:

```
let l:titlecased = substitute(@@, l:WORD_PATTERN, l:UPCASE_REPLACEMENT, 'g')
```

`@@` contains the text from the unnamed register to be titlecased. `l:WORD_PATTERN` is the individual keyword match. `l:UPCASE_REPLACEMENT` is the call to the `capitalize()` command (which you will see later). The `'g'` is the global flag that instructs the substitute command to substitute all given words, not just the first word.

The next line:

```
let l:titlecased = s:capitalizeFirstWord(l:titlecased)
```

This guarantees that the first word will always be capitalized. If you have a phrase like "an apple a day keeps the doctor away", since the first word, "an", is a special word, your substitute command won't capitalize it. You need a a method that always capitalizes the first character no matter what. This function does just that (you will see this function detail later). The result of these capitalization methods is stored in the local variable `l:titlecased`.

The next line:

```
call setreg('"', l:titlecased)
```

This puts the capitalized string into the unnamed register (`"`).

Next, the following two lines:

```
let l:subcommands = #{line: "'[V']p", char: "`[v`]p", block: "`[\<c-v>`]p"}
silent execute "noautocmd keepjumps normal! " .. get(l:subcommands, a:type, "")
```

Hey, that looks familiar! You have seen a similar pattern before with `l:commands`. Instead of yank, here you use paste (`p`). Check out the previous section where I went over the `l:commands` for a refresher.

Finally, these two lines:

```
exe "keepj " . l:startLine
exe "sil! keepj norm! " . l:startCol . "\<bar>"
```

You are moving your cursor back to the line and column where you started. That's it!

Let's recap. The above substitute method is smart enough to capitalize the given texts and skip the special words (more on this later). After you have a titlecased string, you store them in the unnamed register. Then you visually highlight the exact same text you operated `g@` on before, then paste from the unnamed register (this effectively replaces the non-titlecased texts with the titlecased version. Finally, you move your cursor back to where you started.

## Cleanups

You are technically done. The texts are now titlecased. All that is left to do is to restore the registers and settings.

```
call setreg('"', l:reg_save)
call setpos("'<", l:visual_marks_save[0])
call setpos("'>", l:visual_marks_save[1])
let &clipboard = l:cb_save
let &selection = l:sel_save
```

These restore:
- the unnamed register.
- the `<` and `>` marks.
- the `'clipboard'` and `'selection'` options.

Phew, you are done. That was a long function. I could have made the function shorter by breaking it apart into smaller ones, but for now, that will have to suffice. Now let's briefly go over the capitalize functions.

## The Capitalize Function

In this section, let's go over the `s:capitalize()` function. This is what the function looks like:

```
function! s:capitalize(string)
    if(toupper(a:string) ==# a:string && a:string != 'A')
        return a:string
    endif

    let l:str = tolower(a:string)
    let l:exclusions = '^\(a\|an\|and\|at\|but\|by\|en\|for\|in\|nor\|of\|off\|on\|or\|out\|per\|so\|the\|to\|up\|yet\|v\.?\|vs\.?\|via\)$'
    if (match(l:str, l:exclusions) >= 0) || (index(s:local_exclusion_list, l:str) >= 0)
      return l:str
    endif

    return toupper(l:str[0]) . l:str[1:]
endfunction
```

Recall that the argument for the `capitalize()` function, `a:string`, is the individual word passed by the `g@` operator. So if I am running `gt` on the text "pancake for breakfast", `ToTitle`  will call `capitalize(string)` *three* times, once for "pancake", once for "for", and once for "breakfast".

The first part of the function is:

```
if(toupper(a:string) ==# a:string && a:string != 'A')
  return a:string
endif
```

The first condition (`toupper(a:string) ==# a:string`) checks whether the uppercased version of the argument is the same as the string and whether the string itself is "A". If these are true, then return that string. This is based on the assumption that if a given word is already totally uppercased, then it is an abbreviation. For example, the word "CEO" would otherwise be converted into "Ceo". Hmm, your CEO won't be happy. So it's best to leave any fully uppercased word alone. The second condition, `a:string != 'A'`, addresses an edge case for a capitalized "A" character. If `a:string` is already a capitalized "A", it would have accidentally passed the `toupper(a:string) ==# a:string` test. Because "a" is an indefinite article in English, it needs to be lowercased.

The next part forces the string to be lowercased:

```
let l:str = tolower(a:string)
```


The next part is a regex of a list of all word exclusions. I got them from https://titlecaseconverter.com/rules/ :

```
let l:exclusions = '^\(a\|an\|and\|at\|but\|by\|en\|for\|in\|nor\|of\|off\|on\|or\|out\|per\|so\|the\|to\|up\|yet\|v\.?\|vs\.?\|via\)$'
```

The next part:

```
if (match(l:str, l:exclusions) >= 0) || (index(s:local_exclusion_list, l:str) >= 0)
  return l:str
endif
```

First, check if your string is a part of the excluded word list (`l:exclusions`). If it is, don't capitalize it. Then check if your string is a part of the local exclusion list (`s:local_exclusion_list`). This exclusion list is a custom list that the user can add in vimrc (in case the user has additional requirements for special words).

The last part returns the capitalized version of the word. The first character is uppercased while the rest remains as is.

```
return toupper(l:str[0]) . l:str[1:]
```

Let's go over the second capitalize function. The function looks like this:

```
function! s:capitalizeFirstWord(string)
  if (a:string =~ "\n")
    let l:lineArr = trim(a:string)->split('\n')
    let l:lineArr = map(l:lineArr, 'toupper(v:val[0]) . v:val[1:]')
    return l:lineArr->join("\n")
  endif
  return toupper(a:string[0]) . a:string[1:]
endfunction
```

This function was created to handle an edge case if you have a sentence that starts with an excluded word, like "an apple a day keeps the doctor away". Based on English language's capitalization rules, all first words in a sentence, regardless if it is a special word or not, must be capitalized. With your `substitute()` command alone, the "an" in your sentence would be lowercased. You need to force the first character to be uppercased.

In this `capitalizeFirstWord` function, the `a:string` argument is not an individual word like `a:string` inside the `capitalize` function, but instead the whole text. So if you have "pancake for breakfast", `a:string`'s value is "pancake for breakfast".it only runs `capitalizeFirstWord` once for the whole text. 

One scenario you need to watch out for is if you have a multi-line string like `"an apple a day\nkeeps the doctor away"`. You want to uppercase the first character of all lines. If you don't have newlines, then simply uppercase the first character.

```
return toupper(a:string[0]) . a:string[1:]
```

If you have newlines, you need to capitalize all the first characters in each line, so you split them into an array separated by newlines:

```
let l:lineArr = trim(a:string)->split('\n')
```

Then you map each element in the array and capitalize the first word of each element:

```
let l:lineArr = map(l:lineArr, 'toupper(v:val[0]) . v:val[1:]')
```

Finally, you put the array elements together:

```
return l:lineArr->join("\n")
```

And you are done!

## Documentación

El segundo directorio en este repositorio es el directorio `docs/` directory. Es aconsejable el ofrecer junto con el complemento la documentación de uso. En esta sección, trataremos brevemente de repasar cómo hacer la documentación para tu propio complemento.

El directorios `docs/` es una de las rutas especiales para los ejecutables. Vim lee todos los archivos dentro de `docs/` así que cuando buscas una palabra especial y esa palabra es encontrada en uno de los archivos del directorio `docs/`, lo mostrará en la página de ayuda. Aquí tienes el archivo `totitle.txt`. Lo he nombrado de esa manera porque ese es el nombre del complemento, pero puedes ponerle el nombre que quieras.

Un archivo de documentación de Vim no es más que en el fondo un archivo txt de texto. La diferencia entre un archivo normal de texto y un archivo de ayuda de Vim es que este último utiliza una sintaxis especial de ayuda. Pero primero, necesitas decirle a Vim que no trate al archivo como un archivo de texto normal, si no como un archivo de tipo de ayuda `help`. Si quieres que Vim interprete este `totitle.txt` como un fichero de *ayuda*, ejecuta `:set ft=help` (`:h 'filetype'` para más ayuda). Por cierto, si quieres decir a Vim que interprete este `totitle.txt` como un archivo de texto *normal*, ejecuta `:set ft=txt`.

### La sintaxis especial del archivo de ayuda

PAra hacer que la palabra clave de ayuda se pueda encontrar, debes rodear la palabra clave con asteriscos. Para hacer que Vim pueda encontrar la palabra clave `totitle` cuando busques `:h totitle`, escríbelo así `*totitle*` en el archivo de ayuda.

Por ejemplo, tengo estas líneas al inicio del índice de contenidos (*table of contents* o *toc* en inglés).

```
TABLE OF CONTENTS                                     *totitle*  *totitle-toc*

// more TOC stuff
```

Ten en cuneta que he utilizado dos palabras clave: `*totitle*` y `*totitle-toc*` para marcar la sección del índice de contenidos de la ayuda. Puedes utilizar tantas palabras clave como quieras. Esto significa que cada vez que busques tanto `:h totitle` como `:h totitle-toc`, Vim te llevará a esta sección. 

Aquí hay otro ejemplo, en alguna parte más abajo en el fichero:

```
2. Usage                                                       *totitle-usage*

// usage
```

Si buscas `:h totitle-usage`, Vim te llevará a esta sección.

También puedes utilizar enlaces internos a otra sección en el archivo de ayuda, rodeando la palabra clave con `|`. En la sección del índice de contenidos, ves que se usan las palabras clave rodeadas con esas barras, como `|totitle-intro|`, `|totitle-usage|`, etc.

```
TABLE OF CONTENTS                                     *totitle*  *totitle-toc*

    1. Intro ........................... |totitle-intro|
    2. Usage ........................... |totitle-usage|
    3. Words to capitalize ............. |totitle-words|
    4. Operator ........................ |totitle-operator|
    5. Key-binding ..................... |totitle-keybinding|
    6. Bugs ............................ |totitle-bug-report|
    7. Contributing .................... |totitle-contributing|
    8. Credits ......................... |totitle-credits|

```
Esto te permite saltar a la definición. Si pones el cursor sobre `|totitle-intro|` y pulsas `Ctrl-]`, Vim saltará a la definición de ese término. En este caso, saltará a donde se ubica `*totitle-intro*`. Así es como puedes enlazar a diferentes palabras clave en un documento de ayuda.

No hay una manera correcta o errónea de escribir un archivo de ayuda en Vim. Si echas un vistazo a diferentes complementos de Vim de diferentes autores, muchos de ellos utilizan formatos diferentes. Lo importante es hacer una ayuda sencilla y fácil de entender para los usuarios del complemento.

Por último, si estás escribiendo tu propio complemento de manera local, y quieres comprobar la página de documentación, simplemente añadiendo un archivo de texto (txt) dentro de la ruta `~/.vim/docs/` esto no hará que Vim pueda buscar automáticamente las palabras claves que hayas marcado. Necesitas decir a Vim que añada tu página de documentación. Ejecuta el comando: `:helptags ~/.vim/doc` para crear los archivos de etiquetas. Ahora ya sí podrás empezar a buscar por tus palabras clave.

## Conclusión

¡Llegaste hasta el final! Este capítulo es la fusión de todos los capítulos de Vimscript. Aquí finalmente estás poniendo en práctica lo que has aprendido hasta ahora. Con suerte, después de leer esto, no solo comprenderás cómo crear complementos de Vim, sino que también te animó a escribir tu propio complemento.

Cada vez que te encuentres repitiendo la misma secuencia de acciones varias veces, ¡deberías intentar crear la tuya propia! Siempre se ha dicho que no se debe reinventar la rueda. Sin embargo, creo que puede ser beneficioso reinventar la rueda por el bien del aprendizaje. Lee los complementos de otras personas. Aprende de ellos. ¡Escribe lo tuyo! Quién sabe, tal vez escribas el próximo complemento increíble y súper popular después de leer esto. Quizás seas el próximo legendario Tim Pope. Cuando eso suceda, ¡avísame!
