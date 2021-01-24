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

Si ejecutaste previamente `:s/foo/bar/g`, ahora ejecutando `@:` repetirá esa sustitución.

Si ejecutaste `:.!tr '[a-z]' '[A-Z]'`, ejecutando `@:` repetirá el último filtro del comando externo de traducción.

## Command-line Mode Shortcuts

While in the command-line mode, you can move to the left or to the right, one character at a time, with the `Left` or `Right` arrow.

If you need to move word-wise, use `Shift-Left` or `Shift-Right` (in some OS, you might have to use `Ctrl` instead of `Shift`).

To go to the start of the line, use `Ctrl-B`. To go to the end of the line, use `Ctrl-E`.

Similar to the insert mode, inside the command-line mode, you have three ways to delete characters:

```
Ctrl-H    Delete one character
Ctrl-W    Delete one word
Ctrl-U    Delete the entire line
```

## Register and Autocomplete

When programming, whenever possible, do not repeat if you can autocomplete it. This mindset will not only save you time but reduces the chances of typing the wrong characters.

You can insert texts from Vim register with `Ctrl-R` (the same way as the insert mode). If you have the string "foo" saved in the register "a" (`"a`), you can insert it by running `Ctrl-R a`. Everything that you can get from the register in the insert mode, you can do the same from the command-line mode.

You can also autocomplete commands. To autocomplete the `echo` command, while in the command-line mode, type "ec", then press `<Tab>`. You should see on the bottom left Vim commands starting with "ec" (example: `echo echoerr echohl echomsg econ`). To go to the next option, press either `<Tab>` or `Ctrl-N`. To go the previous option, press either `<Shift-Tab>` or `Ctrl-P`.

Some command-line commands accept file names as arguments. One example is `edit`. After typing the command, `:e ` (don't forget the space), press `<Tab>`. Vim will list all the relevant file names.

## History Window

You can view the histoy of command-line commands and search terms (make sure that your Vim build has `+cmdline_hist` when you run `vim --version`).

To open the command-line history, run `:his :`:

```
#  cmd history
2  e file1.txt
3  g/foo/d
4  s/foo/bar/g
```

Vim lists the history of all the `:` commands you run. By default, Vim stores the last 50 commands. To change the amount of the entries that Vim remembers to 100, you can run `:set history=100`.

When you are in the command-line mode, you can traverse this history list by pressing `Up` and `Down` button. Suppose you had the command-line command history that looks like:
```
51  s/foo/bar/g
52  s/foo/baz/g
53  s/foo//g
```

If you press `:` then press `Up` once, you'll see `:s/foo//g`. Press `Up` one more time to see `:s/foo/baz/g`. Vim goes up the history list. 

Similarly, to view the search history, run `:his /`. You can also traverse the history stack by pressing `Up` or `Down` after running the history command `/`.

Vim is smart enough to distinguish different histories. If you press `Up` or `Down` after pressing `:`, Vim automatically the command history. If you press `Up` or `Down` after pressing `/`, Vim automatically searches the search history.

## Command-line Window

The history window displays the list of previously used command-line commands, but you can't execute the command from the history window. To execute a command while browsing, use the *command-line window*. There are three different command-line windows:

```
q:    Command-line window
q/    Forward search window
q?    Backward search window
```

Run `q:` to launch the command-line window for command-line commands. Vim will launch a new window at the bottom of the screen. You can traverse upward with the `Up` or `Ctrl-P` keys and traverse downward with the `Down` or `Ctrl-N` keys. If you press `<Return>`, Vim executes that command. To quit the command-line window, either press `Ctrl-C`, `Ctrl-W C`, or type `:quit`.

Similarly, to launch the command-line window for search, run `q/` to search forward and `q?` to search backward.

## Learn Command-line Mode the Smart Way

Compared to the other three modes, the command-line mode is like the Swiss Army knife of text editing. You can edit text, modify files, and execute commands, just to name a few.  This chapter is a collection of odds and ends of the command-line mode. It also brings Vim modes into closure. Now that you know how to use the normal, insert, visual, and command-line mode you can edit text with Vim faster than ever.

It's time to move away from Vim modes and learn how to do a faster navigation with Vim tags.
