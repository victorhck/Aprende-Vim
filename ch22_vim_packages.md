# Capítulo 22. El gestor de complementos de Vim (Packages)

En los capítulos anteriores, he mencionado el uso de un gestor externo de complementos para instalar complementos. Desde la versión 8, Vim ya incorpora su propio gestor de complementos llamado *packages*. En este capítulo, aprenderás cómo utilizar este gestor para instalar nuevos complementos.

Para ver si la compilación de Vim que usas tiene la posibilidad de utilizar este gestor propio, ejecuta `:version` y busca el atributo `+packages`. De manera alternativa, también puedes ejecutar `:echo has('packages')` (si esto devuelve un 1, entonces tu Vim viene con su gestor propio incorporado).

## El directorio de Pack 

Comprueba si tienes un directorio llamado `~/.vim/` en la ruta raíz de tu usuario. Si no lo tienes, crealo. Dentro, crea un directorio llamado `pack` (`~/.vim/pack/)`. Vim de manera automática sabe que tiene que buscar dentro de este directorio para encontrar los paquetes o complementos.

## Dos tipos de cargas

El gestor de complementos de Vim tiene dos mecanismos de carga: automático y manual.

### Carga automática

Para cargar complementos de manera automática cuando arranque Vim, necesitas ponerlos dentro del directorio `start/`. La ruta tendía un aspecto similar a este:

```
~/.vim/pack/*/start/
```

Ahora podrías preguntar, "¿Qué significa el símbolo `*` entre `pack/` y `start/`?" `*` es un nombre arbitrario y puede ser cualquier cosa que quieras. Vamos a llamarle `packdemo/`:

```
~/.vim/pack/packdemo/start/
```

Ten en cuenta que si lo omites y en vez de eso haces algo así:

```
~/.vim/pack/start/
```

El sistema del gestor *package* no funcionará. Es imperativo el poner un nombre entre `pack/` y `start/`.

Para esta demostración, vamos a tratar de instalar el complemento llamado [NERDTree](https://github.com/preservim/nerdtree). Ve directamente al directorio `start/` (`cd ~/.vim/pack/packdemo/start/`) y clona el repositorio NERDTree desde GitHub:

```
git clone https://github.com/preservim/nerdtree.git
```

¡Eso es todo! Ya está todo listo. La próxima vez que arranques Vim, podrás ejecutar inmediatamente comandos de NERDTree como `:NERDTreeToggle`.

Puedes clonar tantos repositorios de complementos como quieras dentro de la ruta `~/.vim/pack/*/start/`. Vim de manera automática cargará cada uno de ellos. Si quieres eliminar el repositorio clonado (`rm -rf nerdtree/`), ese complemento no estará disponible en tu Vim nunca más.

### Carga manual

Para cargar complementos de manera manual cuando arranca Vim, necesitas ponerlos dentro del directorio `opt/`. De manera similar a la carha automática, la ruta tendrá un aspecto similar a este:

```
~/.vim/pack/*/opt/
```

Vamos a utilizar el mismo directorio `packdemo/` que hemos usado antes:

```
~/.vim/pack/packdemo/opt/
```

Esta vez, vamos a instalar el juego [killersheep](https://github.com/vim/killersheep) (esto necesita como mínimo Vim 8.2). Ve al directorio `opt/` (`cd ~/.vim/pack/packdemo/opt/`) y clona el repositorio:

```
git clone https://github.com/vim/killersheep.git
```

Arranca Vim. El comando para ejecutar el juego es `:KillKillKill`. Trata de ejecutarlo. Vim te avisará de que ese no es un comando válido del editor. Necesitas primero cargar *manualmente* el complemento. Vamos a hacer eso:

```
:packadd killersheep
```

Ahora trata de ejecutar de nuevo el comando `:KillKillKill`. Ahora el comando debería funcionar.

Quizás te preguntes, "¿Por qué iba a querer alguna vez cargar manualmente los complementos? ¿No sería mejor que se cargaran automáticamente todos al inicio?".

Buena pregunta. A veces hay complementos que no querrás utilizar todo el tiempo, como el juego KillerSheep. Lo más probable es que no necesites cargar 10 juegos diferentes y ralentizar el arranque de Vim. Sin embargo, de vez en cuando si estás aburrido o aburrida, quizás quieras jugar algún juego en Vim. Utiliza la carga manual para complementos que no sean esenciales.

También puedes utilizar esto para para añadir de manera condicional complementos. Quizás tu usas tanto Neovim como Vim y hay complementos optimizados para Neovim. Puedes utilizar algo como esto en tu vimrc:

```
if has('nvim')
  packadd! neovim-only-plugin
else
  packadd! generic-vim-plugin
endif
```

## Organizing packages

Recall that the requirement to use Vim's package system is to have either:

```
~/.vim/pack/*/start/
```

Or:

```
~/.vim/pack/*/opt/
```

The fact that `*` can be *any* name can be used to organize your packages. Suppose you want to group your plugins based on categories (colors, syntax, and games):

```
~/.vim/pack/colors/
~/.vim/pack/syntax/
~/.vim/pack/games/
```

You can still use `start/` and `opt/` inside each of the directories.

```
~/.vim/pack/colors/start/
~/.vim/pack/colors/opt/

~/.vim/pack/syntax/start/
~/.vim/pack/syntax/opt/

~/.vim/pack/games/start/
~/.vim/pack/games/opt/
```

## Adding Packages The Smart Way

You may wonder if Vim package will make popular plugin managers like vim-pathogen, vundle.vim, dein.vim, and vim-plug obsolete.

The answer is, as always, "it depends."

I still use vim-plug because it makes it easy to add, remove or update plugins. If you use many plugins, it may be more convenient to use plugin managers because it is easy to update many simultaneously. Some plugin managers also offer asynchronous functionalities.

If you are a minimalist, try out Vim packages. If you a heavy plugin user, you may want to consider using a plugin manager.
