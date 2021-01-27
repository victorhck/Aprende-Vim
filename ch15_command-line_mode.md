# Capítulo 15: El modo de línea de comando.

En los últimos tres capítulos, aprendiste cómo utilizar los comandos de búsqueda (`/`, `?`), sustitución (`:s`), el comando global (`:g`) y el comando externo (`!`). Estos son ejemplos de comandos para la línea de comandos.

En este capítulo, aprenderás varios trucos para el modo línea de comandos.

## Entrar y salir del modo línea de comandos
El modo línea de comandos es un modo en sí mismo, como el modo normal, el modo insertar y el modo visual. Cuando estás en este modo, el cursor se situa en la parte inferior de la pantalla en donde se pueden escribir diferentes comandos.

Hay 4 comandos diferentes que puedes introducir cuando estás en el modo línea de comandos:
- Patrones de búsqueda (`/`, `?`)
- Comandos de la línea de comandos (`:`)
- Comandos externos (`!`)

Puedes entrar en el modo línea de comandos desde el modo normal o desde el modo visual. 

Para dejar el modo línea de comandos, puedes utilizar `<esc>`, `Ctrl-C, o Ctrl-[`.

*Algunas veces en otras guías o libros puede que refieran a los "comandos de la línea de comandos" como "comandos Ex" y los "comandos externos" como "comandos de filtros" u "operadores bang".*

## Repitiendo el comando previo

Puedes repetir tanto el comando o comando externo ejecutado previamente medianto `@:`. 

Si ejecutaste previamente `:s/foo/bar/g`, ahora ejecutando `@:` repetirá esa sustitución. Si ejecutaste `:.!tr '[a-z]' '[A-Z]'`, ejecutando `@:` repetirá el último filtro del comando externo de traducción.

## Atajos de teclado del modo línea de comandos

Mientras estás en el modo línea de comandos, puede moverta a izquierda o derecha un caracter cada vez con las flechas de dirección del cursor `Izquierda` o `Derecha`.

Si necesitas moverte una palabra cada vez, utiliza `Shift-Izquierda` o `Shift-Derecha` (en algunos sistemas operativos, deberás utilizar la tecla `Ctrl` en vez de `Shift`).

Para ir al comienzo de la línea, utiliza `Ctrl-B`. Para ir al final de la línea, utiliza `Ctrl-E`.

De manera similar al modo de insertar, dentro del modo línea de comandos, tienes tres maneras de eliminar caracteres:

```
Ctrl-H    Elimina un caracter
Ctrl-W    Elimina una palabra
Ctrl-U    Elimina la línea entera
```

Finalmente, si quieres editar el comando como si fuera un archivo de texto normal, utiliza `Ctrl-f`.

Esto también te permite buscar entre comandos previos, editarlos y volverlos a ejecutar pulsando `<Enter` en el "modo normal de edición de línea de comandos".

## Registro y autocompletado

Mientras estás en el modo de línea de comandos, puedes insertar textos desde los registros de Vim con `Ctrl-R` de la misma manera que lo haces en el modo insertar. Si tienes la cadena "foo" guardada en el registro "a", puedes insertarlo ejecutando `Ctrl-R a`. Todo lo que puedes hacer desde los registros en el modo normal, también puedes hacerlo en el modo línea de comandos.

Además, también puedes insetar la palabra que está bajo el cursor con `Ctrl-R Ctrl-W` (puedes utilizar `Ctrl-R Ctrl-A` para la PALABRA bajo el cursor). Para insertar la línea que está bajo el cursor utiliza `Ctrl-R Ctrl-L`. Para insertar todo el nombre del archivo bajo el cursor, usa `Ctrl-R Ctrl-F`.

También puedes autocompletar los comandos existentes. Para autocompletar el comando `echo`, mientras estás en el modo línea de comandos, escribe "ec", después pulsa `<Tab>`. Deberías ver en la parte inferior izquierda de la pantalla los comandos de Vim que empiezan con "ec" (ejemplo: `echo echoerr echohl echomsg econ`). Para ir a la siguiente opción, pulsa tanto `<Tab>` o `Ctrl-N`. Para ir a la opción previa, puedes pulsar tando `<Shift-Tab>` o `Ctrl-P`.

Algunos de los comandos de la línea de los comandos aceptan nombres de archivos como argumentos. Un ejemplo es `edit`. También puedes utilizar el autocompletado en este caso. Después de ecribir el comando, `:e ` (no olvides añadir un espacio al final), presiona `<Tab>`. Vim mostrará un listado de todos los nombres de archivos relevantes entre los que puedes escoger, así no tendrás que escribirlo tu mismo.

## History Window And Command-line Window

You can view the histoy of command-line commands and search terms (this requires the `+cmdline_hist` feature).

To open the command-line history, run `:his :`. You should see something like the following:

```
##  cmd History
2  e file1.txt
3  g/foo/d
4  s/foo/bar/g
```

Vim lists the history of all the `:` commands you run. By default, Vim stores the last 50 commands. To change the amount of the entries that Vim remembers to 100, you run `set history=100`.

A more useful use of the command-line history is through the command-line window,`q:`. This will open a searchable, editable history window. Suppose you have these expressions in the history when you press `q:`:

```
51  s/verylongsubstitutionpattern/pancake/g
52  his :
53  wq
```

If your current task is to do `s/verylongsubstitutionpattern/donut/g`, instead of typing the command from scratch, why don't you reuse `s/verylongsubstitutionpattern/pancake/g`? After all, the only thing that's different is the word substitute, "donut" vs "pancake". Everything else is the same.

After you ran `q:`, find that `s/verylongsubstitutionpattern/pancake/g` in the history (you can use the Vim navigation in this environment) and edit it directly! Change "pancake" to "donut" inside the history window, then press `<Enter>`. Boom! Vim executes `s/verylongsubstitutionpattern/donut/g` for you. Super convenient!

Similarly, to view the search history, run `:his /` or `:his ?`. To open the search history window where you can search and edit past history, run `q/` or `q?`.

To quit this window, press `Ctrl-C`, `Ctrl-W C`, or type `:quit`.

## More Command-line Commands

Vim has hundreds of built-in commands. To see all the commands Vim have, check out `:h ex-cmd-index` or `:h :index`.

## Learn Command-line Mode The Smart Way

Compared to the other three modes, the command-line mode is like the Swiss Army knife of text editing. You can edit text, modify files, and execute commands, just to name a few. This chapter is a collection of odds and ends of the command-line mode. It also brings Vim modes into closure. Now that you know how to use the normal, insert, visual, and command-line mode you can edit text with Vim faster than ever.

It's time to move away from Vim modes and learn how to do an even faster navigation with Vim tags.
