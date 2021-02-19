# Aprende Vim (de la manera más inteligente)

## ¿De qué trata esto?

Hay muchos lugares donde aprender a usar Vim: el `vimtutor` es un buen lugar para comenzar y los manuales de la ayuda de Vim `:help` tienen todas las referencias que vayas a poder necesitar.

Sin embargo, el usuario medio necesita algo más que lo que ofrece `vimtutor` y algo menos que el manual de `:help`. Esta guía trata de cerrar esa brecha destacando solo las funcionalidades más importante para aprender las partes más útiles de Vim en el menor tiempo posible.

Lo más probable es que no vayas a necesitar el 100% de las funcionalidades que ofrece Vim. Probablemente solo necesitarás saber el 20% de estas para convertirte en *Vimmer* destacado. Esta guía te enseñará cuales de las funcionalidades de Vim vas a encontrar más útiles.

Esta es una guía obstinada. Trata de técnica que utilizo habitualmente cuando uso Vim. La secuencia de los capítulos está pensada basandome en lo que tendría un sentido más lógico para un principiante a la hora de aprender Vim.

Esta guía está llena de ejemplos. Al aprender una nueva materia, los ejemplos son indispensables, el disponer de numerosos ejemplos hará que se fijen estos conceptos de una manera más efectiva.

Quiźas alguna persona se pregunten ¿por qué voy a necesitar aprender Vimscript? En mi primer año de uso de Vim, estaba satisfecho con solo saber utilizar Vim. A medida que pasó el tiempo y comencé a necesitar Vimscript más y más para escribir comandos personalizados para mis necesidades específicas a la hora de editar. Mientras estás dominando Vim, antes o después necesitarás aprender Vimscript. Así que ¿por qué no mejor cuanto antes? Vimscript es un lenguajes pequeño. Puedes aprender lo básico en solo cuatro capítulos de esta guía.

Puedes llegar lejos utilizando Vim sin conocer en absoluto Vimscript, pero conociéndolo te ayudará a sobresalir aún más.

Esta guía está escrita tanto para principiantes como para personas que utilicen Vim de una manera más avanzada. Comienza con conceptos amplios y simples y acaba con conceptos específicos y avanzados. Si ya crees que eres una persona con conocimientos avanzados, te recomendaría que igualmente leyeras esta guía desde el principio hasta el fin, porque ¡aprenderás algo nuevo!

## Cómo surgió la idea
Es una traducción de la guía original en inglés que puedes encontrar en: [https://github.com/iggredible/Learn-Vim](https://github.com/iggredible/Learn-Vim) creada por **Igor Irianto**

La idea es trauducir la guía del inglés al español para difundir y dar a conocer este potente editor de texto que es Vim. Traducciones, correcciones y mejoras son bienvenidas. No dudes en hacer un PR o abrir un *issue*.

## Índice de contenidos (Trabajo en progreso. Se irán traduciendo los diferentes capítulos)

### Prólogo

- [Cap 0     - ¿Nuevo o nueva en Vim? Lee esto primero](./ch00_new_to_vim_read_this_first.md) .................................... En progreso [ ] / Traducido ✅  / Revisado [ ]

### Parte 1: Aprendiendo Vim de la manera más inteligente

- [Cap 1  - Arrancando Vim](./ch01_starting_vim.md) .................................... En progreso [ ]  / Traducido ✅ / Revisado [ ]
- [Cap 2  - *Buffers*, ventanas y pestañas](./ch02_buffers_windows_tabs.md) ................ En progreso [ ]  / Traducido ✅  / Revisado [ ]
- [Cap 3  - Abriendo y buscando archivos](./ch03_opening_and_searching_files.md) ............... En progreso [ ]  / Traducido ✅  / Revisado [ ]
- [Cap 4  - La gramática de Vim](./ch04_vim_grammar.md) ............................. En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 5  - Moviéndote en un archivo](./ch05_moving_in_file.md) ...................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 6  - El modo insertar](./ch06_insert_mode.md) ................................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 7  - El comando del punto](./ch07_the_dot_command.md) ........................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 8  - Registros](./ch08_registers.md) ................................................ En progreso ✅ / Traducido [ ] / Revisado [ ]
- [Cap 9  - Macros](./ch09_macros.md) ................................................. En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 10 - Deshacer](./ch10_undo.md) ............................................ En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 11 - El modo visual](./ch11_visual_mode.md) ................................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 12 - Buscar y sustituir](./ch12_search_and_substitute.md) ................................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 13 - El comando global](./ch13_the_global_command.md) ............................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 14 - Comandos externos](./ch14_external_commands.md) ........................... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 15 - El modo de línea de comandos](./ch15_command-line_mode.md) .......... En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 16 - Etiquetas](./ch16_tags.md) ............................................ En progreso [ ] / Traducido ✅ / Revisado [ ]
- [Cap 17 - Desplegables](./ch17_fold.md) ........................................ En progreso [ ] / Traducido [ ] / Revisado [ ]
- [Cap 18 - Git](./ch18_git.md) ....................................................... En progreso [ ] / Traducido [ ] / Revisado [ ]
- [Cap 19 - Compilar](./ch19_compile.md) ............................................... En progreso [ ] / Traducido [ ] / Revisado [ ]
- [Cap 20 - Vistas, sesiones y Viminfo](./ch20_views_sessions_viminfo.md) ................... En progreso [ ] / Traducido [ ] / Revisado [ ] 

### Parte 2: Personaliza Vim de la manera más inteligente

- [Cap 21 - Vimrc](./ch21_vimrc.md) ......... En progreso [ ] / Traducido [ ] / Revisado [ ] 
- [Cap 22 - Paquetes de Vim](./ch22_vim_packages.md)  ......... En progreso [ ] / Traducido [ ] / Revisado [ ]                           
- [Ch 23 - Vim Runtime](./ch23_vim_runtime.md) ......... En progreso [ ] / Traducido [ ] / Revisado [ ] 

### Parte 3: Aprende Vimscript de la manera más inteligente

- [Ch 24 - Vimscript Basic Data Types](./ch24_vimscript_basic_data_types.md)......... En progreso [ ] / Traducido [ ] / Revisado [ ] 
- [Ch 25 - Vimscript Conditionals And Loops](./ch25_vimscript_conditionals_and_loops.md) ......... En progreso [ ] / Traducido [ ] / Revisado [ ] 
- [Ch 26 - Vimscript Variable Scopes](./ch26_vimscript_variables_scopes.md) ......... En progreso [ ] / Traducido [ ] / Revisado [ ] 
- [Ch 27 - Vimscript Functions](./ch27_vimscript_functions.md)  ......... En progreso [ ] / Traducido [ ] / Revisado [ ] 


# Licencia y derechos de autor
El materia original en iglés es autoría de ©2020-2021 [Igor Irianto.](https://github.com/iggredible/Learn-Vim) publicado bajo una licencia <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International</a>.

La traducción en español, es mía junto con las [diferentes personas](https://github.com/victorhck/learn-Vim-es/graphs/contributors) que colaboren en este repositorio. Al colaborar en este repositorio estás de acuerdo en compartir tu trabajo con la licencia utilizada para el repositorio completo.

Si creas tu propia versión utilizando esta traducción, por favor da crédito al repositorio desde el que creas tu versión para respetar la licencia:

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png" /></a><br />

Este trabajo está publicado bajo una licencia <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International</a>.

