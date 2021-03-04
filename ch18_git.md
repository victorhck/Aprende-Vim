# Capítulo 18: Git

Vim y git son dos grandes herramientas para dos cosas diferentes. Git es una herramienta para el control de versiones. Vim es un editor de texto.

En este capítulo, aprenderás diferentes maneras de integrar Vim y git.

## Destacando las diferencia

Recuerda del capítulo anterior que puedes ejecutar el comando `vimdiff` para mostrar las diferencias entre múltiples archivo.

Supongamos que tienes dos archivos, `archivo1.txt` y `archivo2.txt`. 

Dentro de `archivo1.txt`:

```
tortitas
gofres
manzanas

leche
zumo de manzana

yogur
```

Dentro de `Archivo2.txt`:

```
tortitas
gofres
naranjas

leche
zumo de naranja

yogur
```

Para ver las diferencias entre ambos archivos, ejecuta:

```
vimdiff archivo1.txt archivo2.txt
```

De manera alternativa también podrías ejecutar:

```
vim -d archivo1.txt archivo2.txt
```

<p align="center">
  <img alt="Basic diffing with Vim" width="900" height="auto" src="images/diffing-basic.png">
</p>

`vimdiff` muestra dos *buffers* uno al lado del otro. En la izquierda está `archivo1.txt` y en la derecha está `archivo2.txt`. La primera diferencia (manzanas y naranjas) aparecen resaltadas en ambas líneas.

Supongamos que quieres hacer que el segundo *buffer* tenga también manzanas, en vez de naranjas. PAra transferir el contenido del *buffer* actual (estando el cursor en el archivo `archivo1.txt`) a `archivo2.txt`, primero ve a la siguiente diferencia con `]c` (para saltar a una diferencia previa, utiliza `[c`). El cursor debería estar ahora en manzanas. Ejecuta `:diffput`. Ambos archivos deberían tener ahora la palabra manzanas.

<p align="center">
  <img alt="Diffing Apples" width="900" height="auto" src="images/diffing-apples.png">
</p>

Si necesitas transferir el texto desde el otro *buffer* (zumo de naranja, `archivo2.txt`) para reemplazar el texto del *buffer* actual (zumo de manzana, `archivo1.txt`), con tu cursor todavía en la ventana de `archivo1.txt`, primero dirígete hasta la siguiente diferencia mediante `]c`. Tu cursor ahora debería estar sobre el texto zumo de manzana. Ejecuta `:diffget` para incorporar el texto zumo de naranja desde el otro *buffer* para reemplazar el zumo de manzana en el *buffer* actual.

`:diffput` *pone* el texto desde el *buffer* actual a otro *buffer*. `:diffget` *incorpora* el texto desde otro *buffer* al *buffer* actual.

Si tienes múltiples *buffers*, puedes ejecutar `:diffput archivoN.txt` y `:diffget archivoN.txt` para apuntar al *buffer* del archivoN.

## Vim como herramienta para la fusión (merge)

> "¡Me encanta resolver los conflictos de fusión en git!" - Nadie

No conozco a nadie que le guste resolver conflictos a la hora de fusionar código en git (*merge*). Sin embargo, son inevitables. En esta sección aprenderás cómo aprovechar los recursos de Vim como herramienta para la resolución de conflictos de fusión de código en git.

Primero, cambia la herramienta para realizar la fusión para utilizar `vimdiff` ejecutando:

```
git config merge.tool vimdiff
git config merge.conflictstyle diff3
git config mergetool.prompt false
```

De manera alternativa, puedes modificar directamente en `~/.gitconfig` (de manera predeterminada ese archivo debería estar en la raíz de tu usuario en sistemas basados en Unix, pero tu archivo puede que este en un lugar diferente). Los comandos anteriores deberían modificar tu archivo gitconfig para tener un aspecto similar al texto que se reproduce a continuación, si no todavía no has ejecutado los comandos anteriores, puedes editar manualmente tu archivo gitconfig.

```
[core]
  editor = vim
[merge]
  tool = vimdiff
  conflictstyle = diff3
[difftool]
  prompt = false
```

Vamos a crear un falso conflicto de fusión para probar esto. Vamos a crear un directorio llamado `/comida` y hacer que sea un repositorio de git:

```
git init
```

Añade un fichero llamado `desayuno.txt`. Dentro escribimos:

```
tortitas
gofres
naranjas
```

Añade el archivo y haz un *commit*:

```
git add .
git commit -m "commit inicial de desayuno"
```

Ahora, vamos a crear una nueva rama y llamarla manzanas:

```
git checkout -b manzanas
```

Edita el archivo y cambia el archivo `desayuno.txt`:

```
tortitas
gofres
manzanas
```

Guarda el archivo, añádelo a git y haz un *commit* con los cambios:

```
git add .
git commit -m "Manzanas en vez de naranjas"
```

Genial. Ahora tenemos naranjas en el archivo de la rama principal y manzanas en el archivo de la rama manzanas. Vamos a volver a la rama principal:

```
git checkout master
```

Dentro de `desayuno.txt`, deberías ver naranjas. Vamos a cambiar esa línea a uvas porque son buenas para la salud:

```
tortitas
gofres
uvas
```

Guarda los cambios, añádelo a git y realiza un *commit*:

```
git add .
git commit -m "Uvas en vez de naranjas"
```

Ahora ya puedes realizar una fusión (*merge*) de la rama manzanas a la rama principal:

```
git merge manzanas
```

Deberías ver un error:

```
Auto-merging desayuno.txt
CONFLICT (content): Merge conflict in desayuno.txt
Automatic merge failed; fix conflicts and then commit the result.
```

¡Un conflicto, genial! Vamos a resolver el conflicto utilizando nuestra recién configurada herramienta `mergetool`. Ejecuta:

```
git mergetool
```

<p align="center">
  <img alt="Three-way mergetool with Vim" width="900" height="auto" src="images/mergetool-initial.png">
</p>

Vim muestra cuatro ventanas. Pon atención a las tres superiores:

- `LOCAL` continene `uvas`. Este es el cambio realizado en "local", en el que vas a fusionar.
- `BASE` continene `naranjas`. Este es el ancestro común entre `LOCAL` y `REMOTE` para comparar cómo difieren.
- `REMOTE` continene `manzanas`. Esto es lo que está siendo fusionado.

En la parte inferior (la cuarta ventanas) verás:

```
tortitas
gofres
<<<<<<< HEAD
uvas
||||||| db63958
naranjas
=======
manzanas
>>>>>>> manzanas
```

La cuarta ventana contiene los textos del conflito de fusión. Con este escenario, es sencillo ver qué cambios tiene cada entorno. Puedes ver el contenido de `LOCAL`, `BASE` y `REMOTE` al mismo tiempo. 

Tu cursor debería estar en la cuarta ventana, en el área resaltada. Para obtener los cambios desde `LOCAL` (uvas), ejecuta `:diffget LOCAL`. Para incorporar los cambios desde `BASE` (naranjas), ejecuta `:diffget BASE` y para obtener los cambios desde `REMOTE` (manzanas), ejecuta `:diffget REMOTE`.

En este caso, vamos a incorporar el cambio desde `LOCAL`. Por lo que ejecutaremos `:diffget LOCAL`. En la cuarta ventana ahora tendremos uvas. Guarda y sal de todos los archivos (`:wqall`) cuando hayas terminado. ¿No fue tan mal, verdad?

Quizás has notado que ahora también tienes un archivo llamado `desayuno.txt.orig`. Git crea un archivo de respaldo en caso de que las cosas no hayan salido bien. Si no quieres que git cree ese archivo durante la fusión, ejecuta:

```
git config --global mergetool.keepBackup false
```

## Git dentro de Vim

Vim no tiene una funcionalidad propia para trabajar con git. Una manera de ejecutar comandos de git desde Vim es utilizando el operador `!`, en el modo línea de comandos.

Cualquier comando de git puede ser ejecutado con `!`:

```
:!git status
:!git commit
:!git diff
:!git push origin master
```

También puedes utilizar las convenciones de Vim para el *buffer* actual con `%` o para otro *buffer* con `#`:

```
:!git add %         " git añade el archivo actual
:!git checkout #    " git hace un *checkout* para el archivo alternativo
```

Un truco de Vim que puedes utilizar para añadir múltiples archivos en una ventana diferente de Vim es ejecutar:

```
:windo !git add %
```

Después haz un *commit*:

```
:!git commit "Añadido todo a git en mi ventana de Vim, genial"
```

El comando `windo` es uno de los comandos "do", similar a `argdo` que ya has visto previamente. `windo` ejecuta el comando en cada ventana.

De manera alternativa, también puedes utilizar `bufdo !git add %` para añadir en git todos los *buffers* o `argdo !git add %` para añadir todos los argumentos del archivo, dependiendo de tu forma de trabajar.

## Plugins

There are many Vim plugins for git support. Below is a list of some of the popular git-related plugins for Vim (there is probably more at the time you read this):

- [vim-gitgutter](https://github.com/airblade/vim-gitgutter)
- [vim-signify](https://github.com/mhinz/vim-signify)
- [vim-fugitive](https://github.com/tpope/vim-fugitive)
- [gv.vim](https://github.com/junegunn/gv.vim)
- [vimagit](https://github.com/jreybert/vimagit)
- [vim-twiggy](https://github.com/sodapopcan/vim-twiggy)
- [rhubarb](https://github.com/tpope/vim-rhubarb)

One of the most popular ones is vim-fugitive. For the remaining of the chapter, I will go over a several git workflow using this plugin.

## Vim-fugitive

The vim-fugitive plugin allows you to run the git CLI without leaving the Vim editor. You will find that some commands are better when executed from inside Vim.

To get started, install the vim-fugitive with a vim plugin manager ([vim-plug](https://github.com/junegunn/vim-plug), [vundle](https://github.com/VundleVim/Vundle.vim), [dein.vim](https://github.com/Shougo/dein.vim), etc).

## Git Status

When you run the `:Git` command without any parameters, vim-fugitive displays a git summary window. It shows the untracked, unstaged, and staged file(s). While in this "`git status`" mode, you can do several things:
- `Ctrl-N` / `Ctrl-P` to go up or down the file list.
- `-` to stage or unstage the file name under the cursor.
- `s` to stage the file name under the cursor.
- `u` to unstage the file name under the cursor.
- `>` / `<` to display or hide an inline diff of the file name under the cursor.

<p align="center">
  <img alt="Fugitive Git" width="900" height="auto" src="images/fugitive-git.png">
</p>

For more, check out `:h fugitive-staging-maps`.

## Git Blame

When you run the `:Git blame` command from the current file, vim-fugitive displays a split blame window. This can be useful to find the person responsible for writing that buggy line of code so you can yell at him / her (just kidding).

Some things you can do while in this `"git blame"` mode:
- `q` to close the blame window.
- `A` to resize the author column.
- `C` to resize the commit column.
- `D` to resize the date / time column.

For more, check out `:h :Git_blame`.

<p align="center">
  <img alt="Fugitive Git Blame" width="900" height="auto" src="images/fugitive-git-blame.png">
</p>

## Gdiffsplit

When you run the `:Gdiffsplit` command, vim-fugitive runs a `vimdiff` of the current file's latest changes against the index or work tree. If you run `:Gdiffsplit <commit>`, vim-fugitive runs a `vimdiff` against that file inside `<commit>`.

<p align="center">
  <img alt="Fugitive Gdiffsplit" width="900" height="auto" src="images/fugitive-gdiffsplit.png">
</p>

Because you are in a `vimdiff` mode, you can *get* or *put* the diff with `:diffput` and `:diffget`.

## Gwrite And Gread

When you run the `:Gwrite` command in a file after you make changes, vim-fugitive stages the changes. It is like running `git add <current-file>`.

When you run the `:Gread` command in a file after you make changes, vim-fugitive restores the file to the state prior to the changes. It is like running `git checkout <current-file>`. One advantage of running `:Gread` is the action is undo-able. If, after you run `:Gread`, you change your mind and want to keep the old change, you can just run undo (`u`) and Vim will undo the `:Gread` action. This would not have been possible if you had run `git checkout <current-file>` from the CLI.

## Gclog

When you run the `:Gclog` command, vim-fugitive displays the commit history. It is like running the `git log` command. Vim-fugitive uses Vim's quickfix to accomplish this, so you can use `:cnext` and `:cprevious` to traverse to the next or previous log information. You can open and close the log list with `:copen` and `:cclose`.

<p align="center">
  <img alt="Fugitive Git Log" width="900" height="auto" src="images/fugitive-git-log.png">
</p>

While in this `"git log"` mode, you can do two things:
- View the tree.
- Visit the parent (the previous commit).

You can pass to `:Gclog` arguments just like the `git log` command. If your project has a long commit history and you only need to view the last three commits, you can run `:Gclog -3`. If you need to filter it based on the committer's date, you can run something like `:Gclog --after="January 1" --before="March 14"`.

## More Vim-Fugitive

These are only a few examples of what vim-fugitive can do. To learn more about vim-fugitive, check out `:h fugitive.txt`. Most of the popular git commands are probably optimized with vim-fugitive. You just have to look for them in the documentation.

If you are inside one of vim-fugitive's "special mode" (for example, inside `:Git` or `:Git blame` mode) and you want to learn what shortcuts are available, press `g?`. Vim-fugitive will display the appropriate `:help` window for the mode you are in. Neat!

## Learn Vim And Git The Smart Way

You may find vim-fugitive to be a good compliment to your workflow (or not). Regardless, I would strongly encourage you to check out all the plugins listed above. There are probably others I didn't list. Go try them out.

One obvious way to get better with Vim-git integration is to read more about git. Git, on its own, is a vast topic and I am only showing a fraction of it. With that, let's *git going* (pardon the pun) and talk about how to use Vim to compile your code!
