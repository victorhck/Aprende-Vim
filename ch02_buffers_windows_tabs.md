# *Buffers*, ventanas y pestañas

Si has utilizado un editor de texto moderno, probablemente estás familiarizado con las ventanas y las pestañas. Vim tiene tres abstracciones en vez de dos: *buffers*, ventanas y pestañas.

En este capítulo veremos cómo funcionan en Vim los *buffers*, ventanas y pestañas.

Antes de comenzar, debes asegurarte que la opción `set hidden` está presente en tu archivo `vimrc`. Sin este ajuste, cada vez que cambies a un *buffer*, Vim te pedirá que guardes el archivo (no quieres eso si quieres moverte rápidamente entre distintos *buffers*. Para más información, echa un vistazo a la ayuda de Vim `:h hidden`.

# *Buffers*

Un *buffer* es un espacio en la memoria en el que puedes escribir y editar algún texto. Cuando abres un nuevo archivo en Vim, su contenido estará ligado a un nuevo *buffer*:
  1. Dentro de Vim, abre un nuevo *buffer* con el comando `:new` (crea un nuevo *buffer vacío)
  2. Desde tu terminal, abre un nuevo archivo llamado `file1.js` (crea un nuevo *buffer* con `file1.js` ligado a el)

Si tu *buffer* no está ligado todavía a un archivo pero quieres guardar su contenido, puedes guardarlo mediante `:w </ruta/al/archivo>`.

```
vim file1.js
```

![un *buffer* mostrando resaltado de texto](./img/screen-one-buffer-file1-highlighted.png)

Lo que se muestra en la imagen anterior es el *buffer* del archivo `file1.js`. Cada vez que abrimos un nuevo archivo, Vim crea un nuevo *buffer*.

Sal de Vim. Esta vez, abre dos nuevos archivos:

```
vim file1.js file2.js
```
![one buffer displayed.png](./img/screen-one-buffer.png)

Vim muestra el *buffer* de `file1.js`, pero ha creado dos *buffers*: el buffer de `file1.js` y el *buffer* de `file2.js`. Puedes mostrar todos los *buffers* mediante el comando `:buffers` (como alternativa también puedes usar `:ls` o `:files`).

![comando de buffers mostrando 2 buffers](./img/screen-one-buffer-buffers-command.png)

Hay varias formas de poder desplazarse por los *buffers*:
1. `:bnext` para ir al *buffer* siguiente (`:bprevious` para ir al *buffer* previo).
2. `:buffer` + nombre_de_archivo. Vim puede autocompletar el nombre de archivo con `tab`.
3. `:buffer` + `n`, donde `n` es el número del *buffer*. Por ejemplo, escribiendo: `:buffer 2` te llevará al *buffer* número 2.
4. Salta a la antigua posición en la lista de saltos con `Ctrl-o` y a la nueva posición con `Ctrl-i`. Estos no son métodos específicos para *buffers*, pero pueden ser utilizados para saltar entre diferentes *buffers*. Hablaremos más sobre los saltos en el capítulo 5.
5. Ir al *buffer* previamente editado con `Ctrl-^`.

Una vez que Vim crea un *buffer*, este permanecerá en tu lista de *buffers*. Para eliminarlo, puedes escribir `:bdelete`. También acepta un número de *buffer* (`:bdelete 3` para eliminar el *buffer* #3) o un nombre de archivo (`:bdelete` después utiliza `tab` para autocompletar el nombre).

La parte más difícil de aprender para mí sobre los *buffer* fue visualizar cómo funcionan. Una forma de hacerlo es imaginando mis *buffers* como un mazo de cartas. Si tengo 3 *buffers*, tengo una pila de 3 cartas. Si veo `file1.js` que se muestra en el *buffer*, entonces la carta de `file1.js` es la que está en la parte superior del mazo. No puedo ver las cartas `file2.js` ni `file3.js`, pero sé que están en el mazo de cartas. Si cambio el buffer a `file3.js`, estoy poniendo la carta de `file1.js` en el mazo y poniendo la carta de `file3.js` en la parte superior del mazo.

Si no has usado Vim anteriormente, este es un nuevo concepto. Tómate tu tiempo para entenderlo. Cuando estés preparado, movámonos a las ventanas.

# Windows

A window is a viewport on a buffer. You can have multiple windows. Most text editors have the ability to display multiple windows. Below you see a VSCode with 3 windows.

![buffers command showing 2 buffers](./img/screen-vscode-3-windows.png)

Let's open `file1.js` from the terminal again:
```
vim file1.js
```
![one buffer displayed.png](./img/screen-one-buffer.png)

Earlier I said that you're looking at `file1.js` buffer. While that was correct, it was incomplete. You are looking at `file1.js` buffer displayed through **a window**. A window is what you are seeing a buffer through.

Don't quit vim yet. Run:
```
:split file2.js
```

![split window horizontally](./img/screen-split-window.png)

Now you are looking at two buffers through **two windows**. The top window displays `file2.js` buffer. The bottom window displays `file1.js` buffer.

By the way, if you want to navigate between windows, you can use these commands:

```
Ctrl-W h    Moves the cursor to the left window
Ctrl-W j    Moves the cursor to the window below
Ctrl-W k    Moves the cursor to the window upper
Ctrl-W l    Moves the cursor to the right window
```


Now run:
```
:vsplit file3.js
```

![split window vertically and horizontally](./img/screen-split-window-vertically-and-horizontally.png)


You are now seeing three windows displaying three buffers. The top left window displays `file3.js` buffer, the top right window displays `file2.js` buffer, and the bottom window displays `file1.js` buffer.

You can have multiple windows displaying the same buffer. While you're on the top left window, type:
```
:buffer file2.js
```
![split window vertically and horizontally with two file2.js](./img/screen-split-window-vertically-and-horizontally-two-file2.png)


Now both top left and top right windows are displaying `file2.js` buffer. If you start typing on the top left, you'll see that the content on both top left and top right window are changing in real time.

To close the current window, you can run `Ctrl-W c` or type `:quit`. When you close a window, the buffer will still be there (again, to view your buffers, you can use `:buffers, :ls, :files`).

Here are some useful normal mode window commands:
```
Ctrl-W v    Opens a new vertical split
Ctrl-W s    Opens a new horizontal split
Ctrl-W c    Closes a window
Ctrl-W o    Makes the current window the only one on screen and closes other windows
```
And here is a list of useful window Ex commands:
```
:vsplit filename    Split window vertically
:split filename     Split window horizontally
:new filename       Create new window
```

For more, check out `:h window`. Take your time to understand them.

# Tabs

A tab is a collection of windows. Think of it like a layout for windows. In most modern text editors (and modern internet browsers), a tab means an open file/ page and when you close it, that file/page goes away. In Vim, a tab does not represent an open file. When you close a tab in Vim, you are not closing a file. Remember, Vim stores files in-memory via buffers. Closing a tab (or a window) does not make that file disappear from the buffers.

Let's see Vim tabs in action. Open `file1.js`:
```
vim file1.js
```

To open `file2.js` in a new tab:

```
:tabnew file2.js
```
![screen displays tab 2](./img/screen-tab2.png)

You can also let Vim autocomplete the file you want to open in a *new tab* by pressing `tab` (no pun intended).


Below is a list of useful tab navigations:
```
:tabnew file.txt    open file.txt in a new tab
:tabclose           Close the current tab
:tabnext            Go to next tab
:tabprevious        Go to previous tab
:tablast            Go to last tab
:tabfirst           Go to first tab
```
You can also run `gt` to go to next tab page. You can pass count as argument to `gt`, where count is tab number. To go to the third tab, do `3gt`.

One advantage of having multiple tabs is you can have different window arrangements in different tabs. Maybe you want your first tab to have 3 vertical windows and second tab to have a mixed horizontal and horizontal windows layout. Tab is the perfect tool for the job!

![first tab with multiple windows](./img/tabs-file1js.png)

![second tab with multiple windows](./img/tabs-file2js.png)

To start Vim with multiple tabs, you can do this from the terminal:
```
vim -p file1.js file2.js file3.js
```
# Moving in 3D

Moving between windows is like traveling two-dimensionally along X-Y axis in a Cartesian coordinate. You can move to the top, right, bottom, and left window with `Ctrl-W h/j/k/l`.

![cartesian movement in x and y axis](./img/cartesian-xy.png)

Moving between buffers is like traveling across the Z axis in a Cartesian coordinate. Imagine your buffer files are lined up across the Z axis. You can traverse the Z axis one buffer at a time with `:bnext` and `:bprevious`. You can jump to any coordinate in Z axis with `:buffer filename/buffernumber`.

![cartesian movement in z axis](./img/cartesian-z.png)

You can move in *three-dimensional space* by combining window and buffer movements. You can move to the top, right, bottom, or left window (X-Y navigations) with window navigations. Since each window contains buffers, you can move forward and backward (Z navigations) with buffer movements.

![cartesian movement in x, y, and z axis](./img/cartesian-xyz.png)


# Using Buffers, Windows, and Tabs the Smart Way

You have learned how buffers, windows, and tabs work in Vim. To use them efficiently, you need to understand what they are designed for and apply them in your own workflow. Everyone has a different workflow, here is one example.

I would use buffers to open up all required files to get the current task done. This task may require opening seven or seven times seven buffer files, but it doesn't matter because Vim can handle many buffers before it starts slowing down. Plus having many buffers opened won't crowd my screen. I am only seeing one buffer (assuming I have only one window) at any time and I can quickly fly to any buffer I wish.

I would use multiple windows to view multiple buffers at once, usually when diffing files, comparing codes, or following a code flow. I usually don't keep more than three windows opened at the same time because my screen will get crowded. When I am done, I would close any extra windows. I prefer having as few opened windows as possible.

I would use a tab to handle client-side codes and a separate tab for back-end codes. Personally, I do not use tabs in my workflow. I use [tmux](https://github.com/tmux/tmux/wiki) windows as substitute for Vim tabs. My tmux workflow is similar to Vim tabs workflow. Instead of having multiple **Vim tabs** opened for each context, I would have multiple **tmux windows** (one tmux window for client-side codes and another window for back-end codes).

My workflow may look different than yours based on your editing style and that's fine. Experiment around to discover your own flow and find what works for you best.

