# Registros

Aprender los registros de Vim es como aprender álgebra por primera vez. No piensas que los necesitas hasta que lo aprendes.

Probablemente has usado los registros de Vim cuando al copiar o borrar un texto y luego lo pegaste con `p` o `P`. No obstante, ¿sabes que Vim tiene 10 tipos diferentes de registros?

En este capítulo repasaré todos los tipos de registros de Vim y como usarlos eficientemente.

# Los diez tipos de registros

Aquí están los 10 tipos de registros que tiene Vim:

1. El registro predeterminado (`""`).
2. Los registros numerados (`"0-9`).
3. El registro de pequeñas eliminaciones (`"-`).
4. Los registros nominales (`"a-z`).
5. El registro de solo lectura (`":`, `".`,y `"%`).
6. El registro de búfer alterno (`"#`).
7. El registro de expresiones (`"=`).
8. Los registros de selección (`"*` y `"+`).
9. El registro de agujero negro (`"_`).
10. El registro del último patrón de búsqueda (`"/`).


# Operadores del registro

Aquí algunos operadores que almacenan valores en los registros:

```
y    Yank (copiar)
c    Borrar texto e iniciar el modo insertar
d    Borrar texto
```

Existen algunos operadores más (como `s` o `x`), pero estos son los más comunes. El criterio general es, si un operador elimina texto, probablemente lo guarde en un registro.

Para poner (pegar) texto desde los registros, puedes usar:

```
p    Ubica el texto después del cursor
P    Ubica el texto antes del cursor
```

Ambos `p` y `P` aceptan un contador y un símbolo de registro como argumentos. Por ejemplo, para poner diez veces el texto recientemente copiado, debes escribir `10p`. Para poner el texto del registro "a", utiliza `"ap`. Y para poner 10 veces el texto del registro "a", es `10"ap`.

En general la sintaxis para obtener el contenido desde un registro en específico es `"x`, donde `x` es el símbolo del registro.


# Llamar a los registros desde el modo insertar

Todo lo que aprendas en este capítulo puede también ser ejecutado en el modo insertar. Para obtener el texto del registro "a", normalmente debes escribir `"ap`; pero si estás en el modo insertar, ejecuta `Ctrl-r a`. La sintaxis para llamar a los registros desde el modo insertar es la siguiente:
```
Ctrl-r x
```
Donde `x` es el símbolo del registro. Ahora que sabes cómo almacenar y recuperar registros, vamos a ello.

# El registro predeterminado (`""`)

Para obtener texto desde el registro predeterminado, escribe `""p`. Este almacena el último texto que copiaste, modificaste o borraste. Si haces otra copia, modificación o borrado, Vim automáticamente reemplazará el texto. El registro predeterminado es como la operación  copiar/pegar estándar de una computadora.

De manera predeterminada, `p` (o `P`) está conectado al registro predeterminado (desde ahora me referiré al registro predeterminado con `p` en lugar de `""p`).

# Los registros numerados (`"0-9`)

Los registros numerados automáticamente se llenan así mismos en orden ascendente. Hay 2 registros numerados diferentes: el registro de copia (`0`) y los registros numerados (`1-9`). Vamos a ver primero el registro de copia.

## El registro de copia (`"0`)

Si copias una línea entera de texto (`yy`), Vim realmente guarda ese texto en dos registros:

1. El registro predeterminado (`p`).
2. El registro de copia (`"0p`).

Cuando copias un texto diferente, Vim remplaza ambos registros, el de copia y el predeterminado. Cualquier otra operación no será almacenada en el registro 0. Esto puede ser usado para tu provecho, porque a menos que no hagas otra copia, el texto copiado permanecerá siempre allí, no importa cuantos cambios y borrados hagas.

Por ejemplo, si haces lo siguiente:
1. Copiar una línea (`yy`)
2. Borrar una línea (`dd`)
3. Borrar otra línea (`dd`)

El registro de copia tendrá el texto del primer paso.

Pero si en cambio haces esto otro:
1. Copias una línea (`yy`)
2. Borras una línea (`dd`)
3. Copias otra línea (`yy`)

El registro de copia tendrá el texto del paso tres.

Un último consejo, mientras estás en el modo insertar, puedes rápidamente pegar el texto que solo copiaste usando `Ctrl-r 0`.

## Los registros numerados (`"1-9`)

Cuando cambias o borras un texto de al menos una línea de largo, este texto se almacenará en un registro numerado del 1 al 9, ordenados por el más reciente.

Por ejemplo, si tienes estas líneas:
```
linea tres
linea dos
linea uno
```
Con tu cursor en la "linea tres" borra una a una las lineas con `dd`. Una vez que las líneas están borradas, el registro 1 debe contener "linea uno" (el texto más reciente), registro 2 la "linea dos" (el segundo texto más reciente) y el registro 3 "linea tres" (el último texto borrado). Para obtener el contenido del registro uno, "linea uno", escribe `"1p`.

Los registros numerados son automáticamente incrementados cuando usas el comando punto. Si tu registro numerado uno (`"1`) contiene "linea uno", registro dos (`"2`) "linea dos" y el registro tres (`"3`) "linea tres", puedes pegarlos secuencialmente con este truco:
- Escribe `"1P` para pegar el contenido del registro del número uno.
- Escribe `.` para pegar el contenido del registro numerado dos (`"2`).
- Escribe `.` para pegar el contenido del registro numerado tres (`"3`).

Durante cada comando punto secuencial, Vim automáticamente incrementa el registro numerado. Este truco funciona con cualquier registro numerado. Si empiezas con `"5P`, `.` hará `"6P`, otra vez `.` hará `"7P` y así sucesivamente.

Pequeños borrados como un borrado de palabras (`dw`) o un cambio de palabras (`cw`) no se almacenan en registros numerados. Estos se almacenan en un pequeño registro de borrado (`"-`), el cual es el siguiente a tratar.

# El pequeño registro de borrado (`"-`)

Cambios o borrados menores a una linea no se almacenan en los registros numerados del 0-9, pero si en el pequeño registro de borrado (`"-`).

Por ejemplo:
1. Borrar una palabra (`diw`)
2. Borrar una linea (`dd`)
3. Borrar una linea (`dd`)

`"-p` te da la palabra borrada en el último paso.

Otro ejemplo:
1. Yo borro una palabra (`diw`)
2. Yo borro una linea (`dd`)
3. Yo borro una linea (`diw`)

`"-p` te da la palabra borrada en el paso tres. Igualmente, `"1p` te da la linea borrada del paso dos. Desafortunadamente, no hay manera de recuperar la palabra borrada del paso uno porque el registro de borrado pequeño solo guarda un elemento. Sin embargo, si deseas preservar el texto del paso uno, puedes hacerlo con los registros nominales.

# The Named Register (`"a-z`)

The named registers are Vim's most versatile register. It can store yanked, changed, and deleted texts into registers a-z. Unlike the previous 3 register types you've seen which automatically stores texts into registers, you have to explicitly tell Vim to use the named register, giving you full control.

To yank a word into register "a", you can do it with `"ayiw`.
- `"a` tells vim that the next action (delete / change / yank) will be stored in register "a".
- `yiw` yanks the word.

To get the text from register "a", run `"ap`. You can use all twenty-six alphabetical characters to store twenty-six different texts with named registers.

Sometimes you may want to add to your existing named register. In this case, you can append your text instead of starting all over. To do that, you can use the uppercase version of that register. For example, suppose you have the word "Hello " already stored in register "a". If you want to add "world" into register "a", you can find the text "world" and yank it using "A" register (`"Aiw`).

# The Read-Only Registers (`":`, `".`, `"%`)

Vim has three read-only registers: `.`, `:`, and `%`. They are pretty simple to use:
```
.    Stores the last inserted text
:    Stores the last executed command-line
%    Stores the name of current file
```

If you write "Hello Vim", running `".p` will print out the text "Hello Vim". If you want to get the name of current file, run `"%p`. If you run `:s/foo/bar/g` command, running `":p` will print out the literal text "s/foo/bar/g".

# The Alternate File Register (`"#`)

In Vim, `#` usually represents the alternate file. An alternative file is the last file you opened. To insert the name of the alternate file, you can use `"#p`.

# The Expression Register (`"=`)

Vim has an expression register, `"=`, to evaluate expressions. Expression is a vast topic in Vim, so I will only cover the basics here. I will address expressions in greater details in the later chapters.

You can evaluate mathematical expressions `1 + 1` with:

```
"=1+1<Enter>p
```

Here, you are telling Vim that you are using the expression register with `"=`. Your expression is (`1 + 1`). Then you need to type `p` to get the result. As mentioned earlier, you can also access the register from insert mode. To evaluate mathematical expression from insert mode, you can do:

```
Ctrl-r =1+1
```

You can get the values from any register using the expression register with `@`. If you wish to get the text from register "a":

```
"=@a
```
Then press `<enter>`, then `p`. Similarly, to get values from register "a" while in insert mode:

```
Ctrl-r =@a
```

You can also evaluate Vim scripts with the expression register. If you define a variable `i` by running `:let i = 1`, you can get it with `"=i`, press return, then `p`. To get it while in insert mode, run `Ctrl-r=i`.

Suppose you have a function: 
```
function! HelloFunc()
	return "Hello Vim Script!"
endfunction
```

You can retrieve its value by calling it. To call it from normal mode, you can do: `"=HelloFunc()`, press return, then `p`. From insert mode `Ctrl-r =HelloFunc()`.

# The Selection registers (`"*`, `"+`)

Don't you sometimes wish that you can copy a text from external programs and paste it locally in Vim, and vice versa? With Vim's selection registers, you can. Vim has two selection registers: `quotestar` (`"*`) and `quoteplus` (`"+`). You can use them to access copied text from external programs.

If you are on an external program (like Chrome browser) and you copy a block of text with `Ctrl-c` (or `Cmd-c`, depending on your OS), normally you wouldn't be able to use `p` to paste the text in Vim. However, both Vim's `"+` and `"*` are connected to your clipboard, so you can actually paste the text with  `"+p` or `"*p`. Conversely, if you yank a word from Vim with `"+yiw` or `"*yiw`, you can paste that text in the external program with `Ctrl-v` (or `Cmd-v`). Note that this only works if your Vim program comes with `+clipboard` option. Check it out by running `vim --version` from the terminal. If you see a `-clipboard`, you have to install a Vim build with clipboard support on.

You may wonder if `"*` and `"+` do the same thing, why does Vim have two different registers? Some machines use X11 window system. This system has 3 types of selections: primary, secondary, and clipboard. If your machine uses X11, Vim uses X11's *primary* selection with the `quotestar` (`"*`) register and X11's *clipboard* selection with the `quoteplus` (`"+`) register. This is only applicable if you have `xterm_clipboard` option available in your Vim build (`+xterm_clipboard` in `vim --version`). If your Vim doesn't have `xterm_clipboard`, it's not a big deal. It just means that both `quotestar` and `quoteplus` are interchangeable.

I find doing `=*p` or `=+p` to be cumbersome. To make Vim to paste copied text from the external program with just `p`, you can add this in your `vimrc`:

```
set clipboard=unnamed
 ```

Now when I copy a text from an external program, I can paste it with the unnamed register, `p`. I can also copy a text from Vim and paste it to an external program with `Ctrl-v`. If you have `+xterm_clipboard` on, you may want to use both `unnamed` and `unnamedplus` clipboard options.

# The Black Hole Register (`"_`)

Everytime you delete or change a text, that text is stored in Vim register automatically. Sometimes you just don't want to save anything into the register. How can you do that?

You can use the black hole register (`"_`). To delete a line and not have Vim store the deleted line into any register, use `"_dd`. It’s the `/dev/null` of registers. 

# The Last Search Pattern Register (`"/`)

To paste your last search (`/` or `?`) query, you can use the last search pattern register (`"/`). To paste the last search term, use `"/p`.


# Viewing the Registers

To view all your registers, use the `:register` command. To view only registers "a", "1", and "-", use `:register a 1 -`.

There is a plugin called [vim-peekaboo](https://github.com/junegunn/vim-peekaboo) that lets you to peek into the contents of the registers when you hit `"` or `@` in normal mode and `Ctrl-r` in insert mode. I find this plugin very useful because most times, I can't remember the content in my registers. Give it a try!

# Executing a Register

The named registers are not just for storing texts. They can also be used to execute macros with `@`. I will go over macros in the next chapter. If you store the text "Hello Vim" in register "a", and you later record a macro in the same register (`qa{macro-commands}q`), that macro will overwrite your "Hello Vim" text stored earlier (you can execute the macro stored in register "a" with `@a`).

# Clearing a Register

Technically, there is no need to clear any register because the next register you store under the same name will overwrite it. However, you can quickly clear any named register by recording an empty macro. For example, if you run `qaq`, Vim will record an empty macro in the register "a". Another alternative is to run the command `:call setreg('a', '')` where "a" is the register "a". One more way to clear register is to set the content of "a" register to an empty string with the expression `:let @a = ''`.

# Putting the Content of a Register

You can use the `:put` command to paste the content of any one register. For example, if you run `:put a`, Vim will print the content of register "a". This behaves much like `"ap`, with the difference that the normal mode command `p` prints the register content after the cursor and the command `:put` prints the register content at newline.

# Learning Registers the Smart Way

You made it to the end. Congratulations! That was a lot to take. If you are feeling overwhelmed by the sheer information, you are not alone. I was too, when I first started learning about Vim registers.

I don't think you should memorize everything right away. To become productive, you can start by using only these 3 registers: 
1. The unnamed register (`""`).
2. The named registers (`"a-z`).
3. The numbered Registers (`"0-9`).

Since the unnamed register defaults to `p` or `P`, you only have to learn two registers: the named registers and the numbered registers. Gradually learn more when you need it. Take your time. 

The average human has a limited short-term memory capacity, about seven items at once. That is why in my everyday editing, I only use about three to seven named registers. There is no way I can remember all twenty-six in my head. I normally start with register "a", then "b", ascending the alphabetical order. Try it and experiment around to see what technique works best for you.

Vim registers are powerful. Used strategically, it can save you from typing countless repeated texts. But now, it's time to learn about macros.
