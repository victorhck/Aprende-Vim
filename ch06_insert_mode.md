# Capítulo 6: EL modo insertar

El modo insertar es el modo predeterminado de la mayoría de los editores de texto. En este modo, lo que escribes es lo que aparece en la pantalla.

Sin embargo, eso no significa que no haya cosas que aprender. El modo insertar de Vim contiene muchas funcionalidades útiles. En este capítulo, aprenderás cómo utilizar  estas funcionalidades del modo insertar en Vim para mejorar tu eficiencia a la hora de escribir.

## Formas de entrar al modo insertar

Hay varias formas de entrar en el modo insertar desde el modo normal. Aquí tienes algunas de ellas:

```
i    Inserta texto antes del cursor
I    Inserta texto antes del primer carácter que no sea un espacio en blanco de la linea
a    Añadir texto después del cursor
A    Añadir texto al final de la linea
o    Crea una nueva línea debajo del cursor y cambia al modo insertar
O    Crea una nueva línea encima del cursor y cambia al modo insertar
s    Elimina el carácter debajo del cursor e inserta texto
S    Elimina la línea actual e inserta texto
gi   Inserta texto en la misma posición donde el modo insertar fue detenido por última vez en al *buffer* actual
gI   Inserta texto al comienzo de una línea (columna 1)
```

Ten en cuenta el patrón de minúscula/mayúscula. Para cada comando en minúscula existe su contraparte en mayúscula. Si eres un usuario nuevo, no te preocupes si no recuerdas toda la lista anterior. Empieza con `i` y `o`. Estos comandos deberían ser suficientes para empezar. Gradualmente ve aprendiendo más con el paso del tiempo.

## Formas diferentes de salir del modo insertar

Hay unas cuantas maneras de regresar al modo normal mientras estás en el modo insertar:

```
<Esc>    Salir del modo insertar y volver al modo normal
Ctrl-[   Salir del modo insertar y volver al modo normal
Ctrl-c   Similar a Ctrl-[ y <Esc>, pero no controla las abreviaciones
```

Encuentro la tecla `<Esc>` está algo apartada, por lo que mapeo el teclado de mi equipo para que la tecla de `<Bloq mayús>` se comporte como `<Esc>`. Si buscas el teclado de la ADM-3A de Bill Joy (creador de Vi), verás que la tecla `<Esc>` no estaba en la esquina superior izquierda como en los teclados modernos, si no al lado de la tecla `q`. Esta es la razón por lo que creo que tiene sentido cambiar el `<Bloq mayús>` por `<Esc>`.

Otra convención común que he visto en personas que utilizan Vim es que mapean `<Esc>` a `jj` o `jk` en el modo insertar. Si quieres probar esta opción, añade una de estas líneas (o ambas) en tu archivo vimrc.

```
inoremap jj <esc>
inoremap jk <esc>
```

## Repetir el modo insertar

Puedes pasar un parámetro de conteo antes de entrar al modo insertar. Por ejemplo:

```
10i
```

Si después escribes "¡Hola mundo!" y sales del modo insertar, Vim repetirá el texto 10 veces. Esto funcionará con cualquier otro método del modo insertar (por ejemplo: `10I`, `11a`, `12o`).

## Borrar segmentos en el modo insertar

Cuando comentes un error al escribir, puede ser molesto pulsar la tecla `<Retroceso>` repetidamente. Podría tener más sentido si vas al modo normal y borras tu error. También puedes borrar varios caracteres al mismo tiempo mientras estás en el modo insertar:

```
Ctrl-h    Borrar un carácter
Ctrl-w    Borrar una palabra
Ctrl-u    Borrar una línea entera
```

## Insert From Register

Registers are like in-memory scratchpads that store and retrieve texts. To insert a text from any named register while in insert mode, type `Ctrl-r` plus the register symbol. There are many symbols you can use, but for this section, just know that you can use named registers (a-z).

To see it in action, first you need to yank a word to register a. You can do this with:
```
"ayiw
```
- `"a` tells Vim that the target of your next action will go to register a.
- `yiw` yanks inner word. Review the chapter on Vim grammar.

Register "a" now contains the word you just yanked. While in insert mode, to paste the text stored in register "a":

```
Ctrl-r a
```

There are multiple types of registers in Vim. I will cover them in greater detail in the next chapter.

## Scrolling

Did you know that you can scroll while inside insert mode? While in insert mode, if you go to `Ctrl-x` sub-mode, you can do additional operations. Scrolling is one of them.

```
Ctrl-x Ctrl-y    Scroll up
Ctrl-x Ctrl-e    Scroll down
```

## Autocompletion

Vim has a built-in autocompletion mechanism using `Ctrl-x` sub-mode (like scrolling). Although it is not as good as [intellisense](https://code.visualstudio.com/docs/editor/intellisense) or any other Language Server Protocol (LSP), but for something that is available right out-of-the-box, it is a very capable feature.

Here are some useful autocomplete commands to get started:
```
Ctrl-x Ctrl-l	   Insert a whole line
Ctrl-x Ctrl-n	   Insert a text from current file
Ctrl-x Ctrl-i	   Insert a text from included files
Ctrl-x Ctrl-f	   Insert a file name
```

When you trigger autocompletion, Vim will display a pop-up window. To navigate up and down the pop-up window, use `Ctrl-n` and `Ctrl-p`.

Vim also has two autocompletions that don't use `Ctrl-x` sub-mode:

```
Ctrl-n             Find the next word match
Ctrl-p             Find the previous word match
```

In general, Vim looks at the text in all available buffers for autocompletion source. If you have an open buffer with a line that says "Chocolate donuts are the best":
- When you type "Choco" and do `Ctrl-x Ctrl-l`, it will match and print the entire line.
- When you type "Choco" and do `Ctrl-p`, it will match and print the word "Chocolate".

Autocomplete is a vast topic in Vim. This is just the tip of the iceberg. To learn more, check out `:h ins-completion`.

## Executing a Normal Mode Command

Did you know Vim can execute a normal mode command while inside insert mode?

While in insert mode, if you press `Ctrl-o`, you'll be in `insert-normal` sub-mode. If you look at the mode indicator on bottom left, normally you will see `-- INSERT --`, but pressing `Ctrl-o`  changes it to `-- (insert) --`. In this mode, you can do *one* normal mode command. Some things you can do:

**Centering and jumping**
```
Ctrl-o zz       Center window
Ctrl-o H/M/L    Jump to top/middle/bottom window
Ctrl-o 'a       Jump to mark 'a'
```

**Repeating text**
```
Ctrl-o 100ihello    Insert "hello" 100 times
```

**Executing terminal commands**
```
Ctrl-o !! curl https://google.com    Run curl
Ctrl-o !! pwd                        Run pwd
```

**Deleting faster**
```
Ctrl-o dtz    Delete from current location till the letter "z"
Ctrl-o D      Delete from current location to the end of the line
```

## Learn Insert Mode the Smart Way

If you are like me and you come from another text editor, it can be tempting to stay in insert mode. However, staying in insert mode when you're not entering a text is an anti-pattern. Develop a habit to go to normal mode when your fingers aren't typing new texts.

When you need to insert a text, first ask yourself if that text already exists. If it does, try to yank or move that text instead of typing it. Should you have to enter insert mode, see if you can autocomplete that text as much as possible. Avoid typing the same word more than once if you can.
