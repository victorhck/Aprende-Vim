# Capítulo 6: El modo insertar

El modo insertar es el modo predeterminado de la mayoría de los editores de texto. En este modo, lo que escribes es lo que aparece en la pantalla.

Sin embargo, eso no significa que no haya cosas que aprender. El modo insertar de Vim contiene muchas funcionalidades útiles. En este capítulo, aprenderás cómo utilizar estas funcionalidades del modo insertar en Vim para mejorar tu eficiencia a la hora de escribir.

## Formas de entrar al modo insertar

Hay varias formas de entrar en el modo insertar desde el modo normal. Aquí tienes algunas de ellas:

```text
i    Inserta texto antes del cursor
I    Inserta texto antes del primer carácter que no sea un espacio en blanco de la línea
a    Añadir texto después del cursor
A    Añadir texto al final de la línea
o    Crea una nueva línea debajo del cursor y cambia al modo insertar
O    Crea una nueva línea encima del cursor y cambia al modo insertar
s    Elimina el carácter debajo del cursor e inserta texto (sustituye texto)
S    Elimina la línea actual e inserta texto (sustituye toda la línea)
gi   Inserta texto en la misma posición donde el modo insertar fue detenido por última vez en el *buffer* actual
gI   Inserta texto al comienzo de una línea (columna 1)
```

Ten en cuenta el patrón de minúscula/mayúscula. Para cada comando en minúscula existe su equivalente en mayúscula. Si eres un usuario nuevo, no te preocupes si no recuerdas toda la lista anterior. Empieza con `i` y `o`. Estos comandos deberían ser suficientes para empezar. Gradualmente ve aprendiendo más con el paso del tiempo.

## Formas diferentes de salir del modo insertar

Hay unas cuantas maneras de regresar al modo normal mientras estás en el modo insertar:

```text
<Esc>    Salir del modo insertar y volver al modo normal
Ctrl-[   Salir del modo insertar y volver al modo normal
Ctrl-C   Similar a Ctrl-[ y <Esc>, pero no controla las abreviaciones
```

Encuentro que la tecla `<Esc>` está algo apartada del resto de las teclas, por lo que mapeo el teclado de mi equipo para que la tecla de `<Bloq mayús>` se comporte como `<Esc>`. Si buscas el teclado de la ADM-3A de Bill Joy \(creador de Vi\), verás que la tecla `<Esc>` no estaba en la esquina superior izquierda como en los teclados modernos, si no al lado de la tecla `q`. Esta es la razón por lo que creo que tiene sentido cambiar el `<Bloq mayús>` por `<Esc>`.

Otra convención común que he visto en personas que utilizan Vim es que mapean `<Esc>` a `jj` o `jk` en el modo insertar. Si quieres probar esta opción, añade una de estas líneas \(o ambas\) en tu archivo vimrc.

```text
inoremap jj <esc>
inoremap jk <esc>
```

## Repetir el modo insertar

Puedes pasar un parámetro de conteo antes de entrar al modo insertar. Por ejemplo:

```text
10i
```

Si después escribes "¡Hola mundo!" y sales del modo insertar, Vim repetirá el texto 10 veces. Esto funcionará con cualquier otro método del modo insertar \(por ejemplo: `10I`, `11a`, `12o`\).

## Borrar segmentos en el modo insertar

Cuando cometes un error al escribir, puede ser molesto pulsar la tecla `<Retroceso>` repetidamente. Podría tener más sentido si vas al modo normal y borras tu error. También puedes borrar varios caracteres al mismo tiempo mientras estás en el modo insertar:

```text
Ctrl-H    Borrar un carácter
Ctrl-W    Borrar una palabra
Ctrl-U    Borrar una línea entera
```

## Insertar desde un registro

Los registros de Vim pueden almacenar textos para un uso futuro. Para insertar un texto en cualquier registro nominal mientras estás en el modo insertar, escribe `Ctrl-R` más el símbolo del registro. Hay muchos símbolos que puedes utilizar, pero para esta sección vamos a utilizar solo los registros nominales \(a-z\).

Para ver esto en acción, primero necesitas copiar una palabra al registro "a". Sitúa tu cursor encima de cualquier palabra y después teclea:

```text
"ayiw
```

Veamos el comando en detalle:

* `"a` le dice a Vim que resultado de tu siguiente acción irá al registro "a"
* `yiw` copia la palabra completa sobre la que está el cursor. Revisa el capítulo sobre la gramática de Vim

El registro "a" ahora contiene la palabra que acabas de copiar. Mientras estás en el modo insertar, para pegar el texto almacenado en el registro "a", escribe:

```text
Ctrl-R a
```

Hay múltiples de tipos de registros en Vim. Trataré sobre ellos con gran detalle en el siguiente capítulo.

## Desplazamiento de la pantalla \(_scroll_\)

¿Sabías que puedes mover el contenido de la pantalla \(hacer _scroll_\) mientras estás en el modo insertar? Mientras estás en el modo insertar, si entras en el submodo con `Ctrl-x`, puedes realizar operaciones adicionales. Desplazar la pantalla es una de ellas.

```text
Ctrl-x Ctrl-Y    Desplaza el contenido de la pantalla hacia arriba
Ctrl-x Ctrl-E    Desplaza el contenido de la pantalla hacia abajo
```

## Autocompletado

Vim tiene un mecanismo de auto completado propio utilizando un submodo con `Ctrl-x` \(como con el desplazamiento que hemos visto antes\). Aunque esto no es tan bueno como [intellisense](https://code.visualstudio.com/docs/editor/intellisense) o cualquier otro Protocolo de lenguaje de servidor \(o LSP por sus siglas en inglés\), pero para algo que está disponible de manera predeterminada, es una característica muy capaz.

Aquí tienes algunos comandos de autocompletado útiles para empezar a utilizarlo:

```text
Ctrl-x Ctrl-L       Inserta una línea completa
Ctrl-x Ctrl-N       Inserta un texto desde el archivo actual
Ctrl-x Ctrl-I       Inserta un texto desde los archivos incluidos
Ctrl-x Ctrl-F       Inserta un nombre de archivo
```

Cuando lanzas el auto completado, Vim mostrará una ventana emergente. Para navegar arriba y abajo por la ventana, utiliza `Ctrl-n` y `Ctrl-p`.

Vim también tiene dos auto completados que no utiliza el submodo `Ctrl-x`:

```text
Ctrl-N             Encuentra la siguiente palabra que coincide
Ctrl-P             Encuentra la palabra anterior que coincide
```

En general, Vim busca en todo el texto disponible en todos los _buffers_ la fuente para el auto completado. Si tienes abierto un _buffer_ con una línea que dice: "Los donuts de chocolate son los mejores":

* Cuando escribes "Choco" y después `Ctrl-x Ctrl-l`, esto encontrará y añadirá la línea completa.
* Cuando escribes "Choco" y después `Ctrl-p`, esto encontrará y añadirá la palabra "Chocolate".

El auto completado es un tema muy amplio en Vim. Esto es solo la punta de iceberg. Para aprender más, echa un vistazo a la ayuda de Vim `:h ins-completion`.

## Ejecutar un comando del modo normal

¿Sabías que Vim puede ejecutar un comando del modo normal mientras estás en el modo insertar?

Mientras estás en el modo insertar, su pulsas `Ctrl-O`, estarás en el submodo `insert-normal`. Si miras el indicador de modo en la parte inferior izquierda, normalmente verás `-- INSERT --`, pero al pulsar `Ctrl-O` este cambia a `-- (insert) --`. En este modo, solo puedes ejecutar _un solo comando_ del modo normal. Algunas cosas que puedes hacer:

**Centrado y salto**

```text
Ctrl-O zz       Lleva la línea donde está el cursor al centro de la ventana
Ctrl-O H/M/L    Mueve el cursor a la parte superior/media/baja de la ventana
Ctrl-O 'a       Salta a la marca a
```

**repitiendo texto**

```text
Ctrl-O 100ihola    Inserta "hola" 100 veces
```

**Ejecutar comandos de la terminal**

```text
Ctrl-O !! curl https://google.com    Ejecuta curl
Ctrl-O !! pwd                        Ejecuta pwd
```

**Borrado más rápido**

```text
Ctrl-O dtz    Elimina desde la ubicación actual del cursor hasta justo antes de la primera letra "z" que encuentre
Ctrl-O D      Elimina desde la posición actual del cursor hasta el final de la línea
```

## Aprende el modo insertar de la manera más inteligente

Si tu, al igual que yo provienes de otro editor de texto, puede ser tentador permanecer en el modo insertar. Sin embargo, el permanecer en el modo insertar cuando no tienes que introducir texto no es lo más indicado. Desarrolla un hábito de volver al modo normal cuando tus dedos no estén escribiendo texto nuevo.

Cuando necesites insertar un texto, pregúntate si ese texto ya existe. Si lo hay, trata de copiarlo o mover ese texto en vez de escribirlo. Si tienes que entrar en el modo insertar, comprueba si puedes auto completar el texto lo máximo posible. Evita escribir la misma palabra más de una vez si puedes.

