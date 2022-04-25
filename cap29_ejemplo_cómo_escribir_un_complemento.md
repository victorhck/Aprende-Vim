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

## ToTitle As a Function

Moving on to the next few lines:

```
if a:type != 'block' && a:type != 'line' && a:type != 'char'
  let l:words = a:type
  let l:wordsArr = trim(l:words)->split('\s\+')
  call map(l:wordsArr, 's:capitalize(v:val)')
  return l:wordsArr->join(' ')
endif
```

This line actually has nothing to do with `ToTitle()` behavior an operator, but to enable it into a callable TitleCase function (yes, I know that I am violating the Single Responsibility Principle). The motivation is, Vim has native `toupper()` and `tolower()` functions that will uppercase and lowercase any given string. Ex: `:echo toupper('hello')` returns `'HELLO'` and `:echo tolower('HELLO')` returns `'hello'`. I want this plugin to have the ability to run `ToTitle` so you can do `:echo ToTitle('once upon a time')` and get a `'Once Upon a Time'` return value.

By now, you know that when you are calling `ToTitle(type)` with `g@`, the `type` argument will have a value of either `'block'`, `'line'`, or `'char`'. If the argument is neither `'block'` nor `'line'` nor `'char'`, you can safely assume that `ToTitle()` is being called outside of `g@`. In that case, you split them by whitespaces (`\s\+`) with:

```
let l:wordsArr = trim(l:words)->split('\s\+')
```

Then capitalize each element:


```
call map(l:wordsArr, 's:capitalize(v:val)')
```

Before joining them back together:

```
l:wordsArr->join(' ')
```


The `capitalize()` function will be covered later.

## Temporary Variables

The next few lines:

```
let l:sel_save = &selection
let l:reg_save = getreginfo('"')
let l:cb_save = &clipboard
let l:visual_marks_save = [getpos("'<"), getpos("'>")]
```

These lines preserve various current states into temporary variables. Later in this you will use visual modes, marks, and registers. Doing these will tamper with the a few states. Since you don't want to revise the history, you need to save them into temporary variables so you can restore the states later.

## Capitalizing the Selections

The next lines are important:

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
Let's go through them in small chunks. This line:

```
set clipboard= selection=inclusive
```

You first set the `selection` option to be inclusive and the `clipboard` to be empty. The selection attribute is typically used with the visual mode and there are three possible values: `old`, `inclusive`, and `exclusive`. Setting it to be inclusive means that the last character of the selection is included. I won't cover them here, but the point is that choosing it to be inclusive makes it behave consistently in visual mode. By default Vim sets it to inclusive, but you set it here anyway just in case one of your plugins sets it to a different value. Check out `:h 'clipboard'` and `:h 'selection'` if you're curious what they really do.

Next you have this weird-looking hash followed by an execute command:

```
let l:commands = #{line: "'[V']y", char: "`[v`]y", block: "`[\<c-v>`]y"}
silent exe 'noautocmd keepjumps normal! ' .. get(l:commands, a:type, '')
```

First, the `#{}` syntax is Vim's dictionary data type. The local variable `l:commands` is a hash with 'lines', 'char', and 'block' as its keys. The command `silent exe '...'` executes whatever command inside the string silently (otherwise it will display notifications to the bottom of your screen). 

Second, the executed commands are `'noautocmd keepjumps normal! ' .. get(l:commands, a:type, '')`. The first one, `noautocmd`, will execute the subsequent command without triggering any autocommand. The second one, `keepjumps`, is to not record the cursor movement while moving. In Vim, certain motions are automatically recorded in the change list, the jump list, and the mark list. This prevents that. The point of having `noautocmd` and `keepjumps` is to prevent side effects. Finally, the `normal` command executes the strings as normal commands. The `..` is Vim's string interpolation syntax. `get()` is a getter method that accepts either a list, blob, or dictionary. In this case, you are passing it the dictionary `l:commands`. The key is `a:type`. You learned earlier that `a:type` is either one of the three string values: 'char', 'line', or 'block'. So if `a:type` is 'line', you will be executing `"noautocmd keepjumps normal! '[V']y"` (for more, check out `:h silent`, `:h :exe`, `:h :noautocmd`, `:h :keepjumps`, `:h :normal`, and `:h get()`).

Let's go over what `'[V']y` does. First assume that you have this body of text:

```
the second breakfast
is better than the first breakfast
```
Assume that your cursor is on the first line. Then you press `g@j` (run the operator function, `g@`, one line below, with `j`). `'[` moves the cursor to the start of the previously changed or yanked text. Although you technically didn't change or yank any text with `g@j`, Vim remembers the locations of the start and the end motions of the `g@` command with `'[` and `']` (for more, check out `:h g@`). In your case, pressing `'[` moves your cursor to the first line because that's where you started when you ran `g@`. `V` is a linewise visual mode command. Finally, `']` moves your cursor to the end of the previous changed or yanked text, but in this case, it moves your cursor to the end of your last `g@` operation. Finally, `y` yanks the selected text. 

What you just did was yanking the same body of text you performed `g@` on.

If you look at the other two commands in here:

```
let l:commands = #{line: "'[V']y", char: "`[v`]y", block: "`[\<c-v>`]y"}
```

They all perform similar actions, except instead of using linewise actions, you would be using characterwise or blockwise actions. I'm going to sound redundant, but in any three cases you are effectively yanking the same body of text you performed `g@` on.

Let's look at the next line:

```
let l:selected_phrase = getreg('"')
```

This line gets the content of the unnamed register (`"`) and stores it inside the variable `l:selected_phrase`.  Wait a minute... didn't you just yank a body of text? The unnamed register currently contains the text that you had just yanked. This is how this plugin is able to get the copy of the text.

The next line is a regular expression pattern:

```
let l:WORD_PATTERN = '\<\k*\>'
```

`\<` and `\>` are word boundary patterns. The character following `\<` matches the beginning of a word and the character preceding `\>` matches the end of a word. `\k` is the keyword pattern. You can check what characters Vim accepts as keywords with `:set iskeyword?`. Recall that the `w` motion in Vim moves your cursor word-wise. Vim comes with a pre-conceied notion of what a "keyword" is (you can even edit them by altering the `iskeyword` option). Check out `:h /\<`, `:h /\>`, and `:h /\k`, and `:h 'iskeyword'` for more. Finally, `*` means zero or more of the subsequent pattern.

In the big picture, `'\<\k*\>'` matches a word. If you have a string:

```
one two three
```

Matching it against the pattern will give you three matches: "one", "two", and "three".

Finally, you have another pattern:

```
let l:UPCASE_REPLACEMENT = '\=s:capitalize(submatch(0))'
```

Recall that Vim's substitute command can be used with an expression with `\={your-expression}`. For example, if you want to uppercase the string "donut" in the current line, you can use Vim's `toupper()` function. You can achieve this by running `:%s/donut/\=toupper(submatch(0))/g`. `submatch(0)` is a special expression used in the substitute command. It returns the whole matched text.

The next two lines:

```
let l:startLine = line("'<")
let l:startCol = virtcol(".")
```

The `line()` expression returns a line number. Here you pass it with the mark `'<`, representing the first line of the last selected visual area. Recall that you used visual mode to yank the text. `'<` returns the line number of the beginning of that visual area selection. The `virtcol()` expression returns a column number of the current cursor. You will be moving your cursor all over the place in a little bit, so you need to store your cursor location so you can return here later.

Take a break here and review everything so far. Make sure you are still following along. When you're ready, let's continue.

## Handling a Block Operation

Let's go through this section:

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

It's time to actually capitalize your text. Recall that you have the `a:type` to be either 'char', 'line', or 'block'. In most cases, you'll probably be getting 'char' and 'line'. But occasionally you may get a block. It is rare, but it must be addressed nonetheless. Unfortunately, handling a block is not as straight-forward as handling char and line. It will take a little extra effort, but it is doable.

Before you start, let's take an example of how you might get a block. Assume that you have this text:

```
pancake for breakfast
pancake for lunch
pancake for dinner
```

Assume that your cursor is on "c" on "pancake" on the first line. You then use the visual block (`Ctrl-V`) to select down and forward to select the "cake" in all three lines:

```
pan[cake] for breakfast
pan[cake] for lunch
pan[cake] for dinner
```

When you press `gt`, you want to get:

```
panCake for breakfast
panCake for lunch
panCake for dinner

```
Here are your basic assumptions: when you highlight the three "cakes" in "pancakes", you are telling Vim that you have three lines of words that you want to highlight. These words are "cake", "cake", and "cake". You expect to get "Cake", "Cake", and "Cake".

Let's move on to the implementation details. The next few lines have:

```
sil! keepj norm! gv"ad
keepj $
keepj pu_
let l:lastLine = line("$")
sil! keepj norm "ap
let l:curLine = line(".")
```

The first line:

```
sil! keepj norm! gv"ad
```

Recall that `sil!` runs silently and `keepj` keeps the jump history when moving. You then execute the normal command `gv"ad`. `gv` selects the last visually highlighted text (in the pancakes example, it will re-highlight all three 'cakes'). `"ad` deletes the visually highlighted texts and stores them in register a. As a result, you now have:

```
pan for breakfast
pan for lunch
pan for dinner
```

Now you have 3 *blocks* (not lines) of 'cakes' stored in the register a. This distinction is important. Yanking a text with linewise visual mode is different from yanking a text with blockwise visual mode. Keep this in mind because you will see this again later.

Next you have:

```
keepj $
keepj pu_
```

`$` moves you to the last line in your file. `pu_` inserts one line below where your cursor is. You want to run them with `keepj` so you don't alter the jump history.

Then you store the line number of your last line (`line("$")`) in the local variable `lastLine`.

```
let l:lastLine = line("$")
```

Then paste the content from the register with `norm "ap`.

```
sil! keepj norm "ap
```

Keep in mind that this is happening on the new line you created below the last line of the file - you are currently at the bottom of the file. Pasting give you these *block* texts:

```
cake
cake
cake
```

Next, you store the location of the current line where your cursor is.

```
let l:curLine = line(".")
```

Now let's go the next few lines:

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

This line:

```
sil! keepj norm! VGg@
```

`VG` visually highlights them with line visual mode from the current line to the end of the file. So here you are highlighting the three blocks of 'cake' texts with linewise highlight (recall the block vs line distinction). Note that the first time you pasted the three "cake" texts, you were pasting them as blocks. Now you are highlighting them as lines. They may look the same from the outside, but internally, Vim knows the difference between pasting blocks of texts and pasting lines of texts.

```
cake
cake
cake
```

`g@` is the function operator, so you are essentially doing a recursive call to itself. But why? What does this accomplish?

You are making a recursive call to `g@` and passing it with all 3 lines (after running it with `V`, you now have lines, not blocks) of 'cake' texts so it will be handled by the other part of the code (you will go over this later). The result of running `g@` is three lines of properly titlecased texts:

```
Cake
Cake
Cake
```

The next line:

```
exe "keepj norm! 0\<c-v>G$h\"ad"
```

This runs the normal mode command to go to the beginning of the line (`0`), use the block visual highlight to go to the last line and last character on that line (`<c-v>G$`). The `h` is to adjust the cursor (when doing `$` Vim moves one extra line to the right). Finally, you delete the highlighted text and store it in the register a (`"ad`).

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

## Docs

The second directory in the repository is the `docs/` directory. It is good to provide the plugin with a thorough documentation. In this section, I'll briefly go over how to make your own plugin docs.

The `docs/` directory is one of Vim's special runtime paths. Vim reads all the files inside the `docs/` so when you search for a special keyword and that keyword is found in one of the files in the `docs/` directory, it will display it in the help page. Here you have a `totitle.txt`. I name it that way because that's the plugin name, but you can name it anything you want.

A Vim docs file is a txt file at heart. The difference between a regular txt file and a Vim help file is that the latter uses special "help" syntaxes. But first, you need to tell Vim to treat it not as a text file type, but as a `help` file type. To tell Vim to interpret this `totitle.txt` as a *help* file, run `:set ft=help` (`:h 'filetype'` for more). By the way, if you want to tell Vim to interpret this `totitle.txt` as a *regular* txt file, run `:set ft=txt`.

### The Help File Special Syntax

To make a keyword discoverable, surround that keyword with asterisks. To make the keyword `totitle` discoverable when user searches for `:h totitle`, write it as `*totitle*` in the help file.

For example, I have these lines on top of my table of contents:

```
TABLE OF CONTENTS                                     *totitle*  *totitle-toc*

// more TOC stuff
```

Note that I used two keywords: `*totitle*` and `*totitle-toc*` to mark the table of contents section. You can use as many keywords as you want. This means that whenever you search for either `:h totitle` or `:h totitle-toc`, Vim takes you to this location. 

Here is another example, somewhere down the file:

```
2. Usage                                                       *totitle-usage*

// usage
```

If you search for `:h totitle-usage`, Vim takes you to this section. 

You can also use internal links to refer to another section in the help file by surrounding a keyword with the bar syntax `|`. In the TOC section, you see keywords surrounded by the bars, like `|totitle-intro|`, `|totitle-usage|`, etc.

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
This lets you jump to the definition. If you put your cursor somewhere on `|totitle-intro|` and press `Ctrl-]`, Vim will jump to the definition of that word. In this case, it will jump to the `*totitle-intro*` location. This is how you can link to different keywords in a help doc.

There is not a right or wrong way to write a doc file in Vim. If you look at different plugins by different authors, many of them use different formats. The point is to make an easy-to-understand help doc for your users.

Finally, if you are writing your own plugin locally at first and you want to test the documentation page, simply adding a txt file inside the `~/.vim/docs/` won't automatically make your keywords searchable. You need to instruct Vim to add your doc page. Run the helptags command: `:helptags ~/.vim/doc` to create new tag files. Now you can start searching for your keywords.

## Conclusion

You made it to the end! This chapter is the amalgamation of all the Vimscript chapters. Here you are finally putting to practice what you've learned so far. Hopefully having read this, you understood not only how to create Vim plugins, but also encouraged you to write your own plugin. 

Whenever you find yourself repeating the same sequence of actions multiple times, you should try to create your own! It was said that you shouldn't reinvent the wheel. However, I think it can be beneficial to reinvent the wheel for the sake of learning. Read other people's plugins. Recreate them. Learn from them. Write your own! Who knows, maybe you will write the next awesome, super-popular plugin after reading this. Maybe you will be the next legendary Tim Pope. When that happens, let me know! 
