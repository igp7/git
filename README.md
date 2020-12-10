# **Git**

## Indice
1. [Configuración inicial](#configuración-inicial)
2. [Creación y obtención de un repositorio Git](#creación-y-obtención-de-un-repositorio-git)
3. [Guardado de cambios en el repositorio](#guardado-de-cambios-en-el-repositorio)
4. [Histórico de confirmaciones](#histórico-de-confirmaciones)
5. [Deshacer cosas](#deshacer-cosas)
6. [Repositorios remotos](#repositorios-remotos)
7. [Ramas](#ramas)
8. [Ramas remotas](#ramas-remotas)
9. [Etiquetas](#etiquetas)
10. [Referencias](#referencias)

## Configuración inicial
Git trae una herramienta llamada ***git config*** que te permite obtener y establecer variables de configuración, que controlan el aspecto y funcionamiento de Git.

- **Visualizar configuracion:** Si quieres comprobar tu configuración, listando todas las propiedades que Git ha configurado.
  ```sh
  $ git config --list
  ```
  **Nota:** También puedes comprobar qué valor cree Git que tiene una clave específica ejecutando *git config {clave}*.
  ```sh
  $ git config user.name
  ```

- **Identidad:** Para establecer tu nombre de usuario y dirección de correo electrónico en Git.
  ```sh
  $ git config --global user.name "<name>"
  $ git config --global user.email <mail>
  ```
  **Nota:** La opción --global, permite a Git usar siempre esta información para todo lo que hagas en ese sistema.

- **Editor:** Puedes elegir el editor de texto por defecto que se utilizará cuando Git necesite que introduzcas un mensaje. Si no indicas nada, Git usa el editor por defecto de tu sistema, que generalmente es Vi o Vim.
  ```sh
  $ git config --global core.editor <name editor>
  ```

- **Herramienta de diferencias:** Para configurar es la herramienta de diferencias por defecto, usada para resolver conflictos de unión (merge).
  ```sh
  $ git config --global merge.tool <name herramienta>
  ```
  **Nota:** Git acepta kdiff3, tkdiff, meld, xxdiff, emerge, vimdiff, gvimdiff, ecmerge, y opendiff como herramientas válidas.

- **Colores en la línea de comandos:** Git permite la coloración útil de la salida de línea de comando.
  ```sh
  $ git config --global color.ui auto
  ```


## Creación y obtención de un repositorio Git
Puedes obtener un proyecto Git de dos maneras. La primera toma un proyecto o directorio existente y lo importa en Git. La segunda clona un repositorio Git existente desde otro servidor.

- **Crear un repositorio:** Si estás empezando el seguimiento en Git de un proyecto existente, necesitas ir al directorio del proyecto y escribir:
  ```sh
  $ git init
  ```
  Esto crea un nuevo subdirectorio llamado. git que contiene todos los archivos necesarios del repositorio —un esqueleto de un repositorio Git.

- **Clonar un repositorio existente:** Para obtener una copia de un repositorio Git existente, se usa:
  ```sh
  $ git clone <url>
  ```
  Si se quiere cambiar el nombre del repositorio usa el siguiente comando:
  ```sh
  $ git clone <url> <name-nuevo>
  ```

## Guardado de cambios en el repositorio
 Cada archivo de tu directorio de trabajo puede estar en uno de estos dos estados: bajo seguimiento (tracked), o sin seguimiento (untracked). Los archivos bajo seguimiento son aquellos que existían en la última instantánea; pueden estar sin modificaciones, modificados, o preparados. Los archivos sin seguimiento son todos los demás (cualquier archivo de tu directorio que no estuviese en tu última instantánea ni está en tu área de preparación). La primera vez que clonas un repositorio, todos tus archivos estarán bajo seguimiento y sin modificaciones, ya que los acabas de copiar y no has modificado nada.

- **Comprobando el estado de tus archivos:** Herramienta para determinar el estado de los archivos.
  ```sh
  $ git status
  ```

- **Seguimiento de nuevos archivos:** Para empezar el seguimiento de un nuevo archivo.
  ```sh
  $ git add <name-archivo>
  ```
  Para añadir todos los archivos puedes usar:
  ```sh
  $ git add *
  ```
  Si modificas un archivo después de a ver realizado **git add** será necesario añadir otra vez el fichero con **git add** para que la modificación pueda ser incluida en el commit. Se recomienda hacer siempre **git add** antes de un **commit**.

- **Ignorar archivos:** A través de un archivo llamado .gitignore, puedes listas los patrones de nombres de archivos o directorios que deseas que sean ignorados. He aquí un archivo .gitignore de ejemplo:
  ```sh
  *.[oa]
  *~
  <name_directorio>/
  ```
  La primera línea le dice a Git que ignore cualquier archivo cuyo nombre termine en .o o .a (archivos objeto que suelen ser producto de la compilación de código). La segunda línea le dice a Git que ignore todos los archivos que terminan en tilde (~), usada por muchos editores de texto, como Emacs, para marcar archivos temporales. La ultima línea le dice a Git que ignore un directorio concreto.
  Las reglas para los patrones que pueden ser incluidos en el archivo .gitignore son:
    - Las líneas en blanco, o que comienzan por #, son ignoradas.
    - Puedes usar patrones glob estándar (expresiones regulares simplificadas que pueden ser usadas por las shells).
    - Puedes indicar un directorio añadiendo una barra hacia delante (/) al final.
    - Puedes negar un patrón añadiendo una exclamación (!) al principio.


- **Ver los cambios preparados y no preparados:** Para visualizar exactamente las líneas añadidas y eliminadas en los archivos, usamos:
  ```sh
  $ git diff
  ```

- **Confirmación de cambios:** Cuando tienes los archivos preparados para realizar una confirmación, se usa:
  ```sh
  $ git commit -m "<texto del commit>"
  ```
  *Nota:* Si quieres saltarte el área de preparación, Git proporciona un atajo. Pasar la opción -a al comando git commit hace que Git prepare todo archivo que estuviese en seguimiento antes de la confirmación, permitiéndote obviar toda la parte de git add.
  ```sh
  $ git commit -a -m "<texto del commit>"
  ```

- **Eliminar archivos:** Para eliminar un archivo de Git, debes eliminarlo de tus archivos bajo seguimiento (más concretamente, debes eliminarlo de tu área de preparación), y después confirmar.
  ```sh
  $ git rm <name archivo>
  ```
  Si ya habías modificado el archivo y lo tenías en el área de preparación, deberás forzar su eliminación con la opción -f.
  ```sh
  $ git rm -f <name archivo>
  ```
  Para mantener el archivo en tu disco duro, pero interrumpir su seguimiento por parte de Git. Esto resulta particularmente útil cuando olvidaste añadir algo a tu archivo .gitignore y lo añadiste accidentalmente, como un archivo de log enorme, o un montón de archivos .a. Para hacer esto, usa la opción --cached:
  ```sh
  $ git rm --cached readme.txt
  ```
  *Nota:* El comando git rm acepta archivos, directorios, y patrones glob. Es decir, que podrías hacer algo así:
  ```sh
  $ git rm log/\*.log
  ```

- **Renombrar archivos:** Git no se da cuenta explícitamente del cambio de nombre de un archivo, pero permite realizar un renombramiento de forma rápida:
  ```sh
  $ git mv <file_from> <file_to>
  ```

## Histórico de confirmaciones
Después de haber hecho varias confirmaciones, o si has clonado un repositorio que ya tenía un histórico de confirmaciones, probablemente quieras mirar atrás para ver qué modificaciones se han llevado a cabo. La herramienta más básica y potente para hacer esto es:
```sh
$ git log
```
Una de las opciones más útiles es -p, que muestra las diferencias introducidas en cada confirmación. También puedes usar la opción -2, que hace que se muestren únicamente las dos últimas entradas del histórico:
```sh
$ git log -p -<número de ultimas entradas a mostrar>
```
Las opciones temporales como --since (desde) y --until (hasta) muestran las entradas desde una fecha o hasta una fecha. El formato de las fechas acepta varios tipos, como una fecha concreta (“2008-01-15”), o relativa, como “2 years 1 day 3 minutes ago” (“hace 2 años, 1 día y 3 minutos”).
```sh
$ git log --since=<2.weeks> o <"2008-10-01">
```
También puedes filtrar la lista para que muestre sólo aquellas confirmaciones que cumplen ciertos criterios. La opción --author te permite filtrar por autor, y --grep te permite buscar palabras clave entre los mensajes de confirmación. (Ten en cuenta que si quieres aplicar ambas opciones simultáneamente, tienes que añadir --all-match, o el comando mostrará las confirmaciones que cumplan cualquiera de las dos, no necesariamente las dos a la vez.).
```sh
$ git log --all-match --author=<name autor> --grep="<texto a buscar en los commit>"
```

## Deshacer cosas
 Ésta es una de las pocas áreas de Git que pueden provocar que pierdas datos si haces las cosas incorrectamente.

 - **Modificar la última confirmación:** Si quieres volver a hacer la confirmación par añadir algún cambio en un archivo que se te ha olvidado, puedes usar:
   ```sh
   $ git commit --amend
   ```
   Este comando utiliza lo que haya en tu área de preparación para la confirmación. Si no has hecho ningún cambio desde la última confirmación (por ejemplo, si ejecutas este comando justo después de tu confirmación anterior), esta instantánea será exactamente igual, y lo único que cambiarás será el mensaje de confirmación.
   Por ejemplo, si confirmas y luego te das cuenta de que se te olvidó preparar los cambios en uno de los archivos que querías añadir, puedes hacer algo así:
   ```sh
  $ git commit -m 'initial commit'
  $ git add forgotten_file
  $ git commit --amend
   ```
  Estos tres comandos acabarán convirtiéndose en una única confirmación —la segunda confirmación reemplazará los resultados de la primera.

- **Deshaciendo la preparación de un archivo:** Cuando quieres sacar un archivo del área de preparación.
  ```sh
  $ git reset HEAD <archivo>
  ```

## Repositorios remotos
Los repositorios remotos son versiones de tu proyecto que se encuentran alojados en Internet o en algún punto de la red. Puedes tener varios, cada uno de los cuales puede ser de sólo lectura, o de lectura/escritura, según los permisos que tengas. Colaborar con otros implica gestionar estos repositorios remotos, y mandar (push) y recibir (pull) datos de ellos cuando necesites compartir cosas.

- **Mostrando repositorios remotos:** Mostrar los repositorios remotos configurados. Si has clonado tu repositorio, deberías ver por lo menos "origin" (es el nombre predeterminado que le da Git al servidor del que clonast).
área de preparación.
  ```sh
  $ git remote
  ```
  También puedes añadir la opción -v, que muestra la URL asociada a cada repositorio remoto:
  ```sh
  $ git remote -v
  ```

- **Añadir repositorios remotos:** Para añadir un nuevo repositorio Git remoto, asignándole un nombre con el que referenciarlo fácilmente.
  ```sh
  $ git remote add [nombre] [url]
  ```
  Ahora puedes usar la cadena [nombre] en la línea de comandos, en lugar de toda la URL. Para recuperar toda la información del repositorio, puedes ejecutar:
  ```sh
  $ git fetch [nombre]
  ```

- **Recibir de tus repositorios remotos:** Para recuperar todos los datos de los repositorios remotos puedes ejecutar:
  ```sh
  $ git fetch [remote-name]
  ```
  Si clonas un repositorio, el comando añade automáticamente ese repositorio remoto con el nombre de "origin". Por tanto, git fetch origin recupera toda la información enviada a ese servidor desde que lo clonaste (o desde la última vez que ejecutaste fetch). Es importante tener en cuenta que el comando fetch sólo recupera la información y la pone en tu repositorio local (no la une automáticamente con tu trabajo ni modifica aquello en lo que estás trabajando). Tendrás que unir ambos manualmente a posteriori.
  Si has configurado una rama para seguir otra rama remota, puedes recuperar y unir automáticamente la rama remota con tu rama actual con el comando:
  ```sh
  $ git pull [remote-name]
  ```
  Por defecto, el comando git clone automáticamente configura tu rama local maestra para que siga la rama remota maestra del servidor del cual clonaste (asumiendo que  el repositorio remoto tiene una rama maestra). Al ejecutar git pull, por lo general se recupera la información del servidor del que clonaste, y automáticamente se intenta unir con el código con el que estás trabajando actualmente.

- **Envió a repositorios remotos:** Enviar el estado de tu repositorio a un repositorio remoto.
  ```sh
  $ git push [nombre-remoto] [nombre-rama]
  ```
  Por ejemplo: Si quieres enviar tu rama maestra (master) a tu servidor origen (origin), ejecutarías esto para enviar tu trabajo al servidor:
  ```sh
  $ git push origin master
  ```
  Este comando funciona únicamente si has clonado de un servidor en el que tienes permiso de escritura, y nadie ha enviado información mientras tanto. Si tú y otra persona clonáis a la vez, y él envía su información y luego envías tú la tuya, tu envío será rechazado. Tendrás que bajarte primero su trabajo e incorporarlo en el tuyo para que se te permita hacer un envío.

- **Inspeccionar un repositorio remoto:** Para ver más información acerca de un repositorio remoto en particular.
  ```sh
  $ git remote show [nombre]
  ```
  Esto lista la URL del repositorio remoto, así como información sobre las ramas bajo seguimiento. Este comando te recuerda que, si estás en la rama maestra y ejecutas git pull, automáticamente unirá los cambios a la rama maestra del remoto después de haber recuperado todas las referencias remotas. También lista todas las referencias remotas que ha recibido.

- **Renombrar repositorios remotos:** Para renombrar una referencia a un repositorio remoto.
  ```sh
  $ git remote rename [nombre actual] [nuevo nombre]
  ```

- **Eliminar repositorios remotos:** Para eliminar una referencia, has movido el servidor o ya no estás usando un determinado mirror, o quizás un contribuidor ha dejado de contribuir.
  ```sh
  $ git remote rm [nombre]
  ```

## Ramas
Una rama Git es simplemente un apuntador móvil apuntando a una de las confirmaciones. La rama por defecto de Git es la rama master. Con la primera confirmación de cambios que realicemos, se creará esta rama principal master apuntando a dicha confirmación. En cada confirmación de cambios que realicemos, la rama irá avanzando automáticamente. Y la rama master apuntará siempre a la última confirmación realizada.

- **Mostrar ramas:** Para mostrar las ramas almacenadas en el repositorio.
  ```sh
  $ git branch
  ```
  Para ver la última confirmación de cambios en cada rama.
  ```sh
  $ git branch -v
  ```

- **Crear ramas:** Para crear ramas nuevas.
  ```sh
  $ git branch [name-rama]
  ```

- **Eliminar ramas:** Para eliminar ramas.
  ```sh
  $ git branch -d [name-rama]
  ```

- **Cambiar de rama:** Para cambiar entre ramas.
  ```sh
  $ git checkout [name-rama]
  ```

- **Fusionar ramas:** Para fusionar dos ramas, nos posicionamos sobre la rama a la que queremos incorporar las confirmaciones de otra rama y ejecutamos el siguiente comando:
  ```sh
  $ git merge [rama de donde se traen las confirmaciones]
  ```

- **Reorganización:** Con la reorganización puedes coger todos los cambios confirmados en una rama, y replicarlos sobre otra. Esto ayuda a que el desarrollo del proyecto parezca lineal y más fácil de seguir. Para realizar la reorganización te posicionas sobre la rama donde están las confirmaciones que quieres volcar sobre otra rama y ejecutas el siguiente comando:
  ```sh
  $ git rebase [rama donde se quieren volcar las confirmaciones para reorganizar]
  ```
  Después, te posicionas sobre la rama donde has volcado las confirmaciones y realizas un merge con la rama de donde trajiste las confirmaciones.
  ```sh
  $ git merge [rama donde estaban las confirmaciones para reorganizar]
  ```

### Ramas remotas
Las ramas remotas son referencias al estado de ramas en tus repositorios remotos. Son ramas locales que no puedes mover; se mueven automáticamente cuando estableces comunicaciones en la red. Las ramas remotas funcionan como marcadores, para recordarte en qué estado se encontraban tus repositorios remotos la última vez que conectaste con ellos.

- **Publicar en ramas remotas:** Tus ramas locales no se sincronizan automáticamente con los remotos en los que escribes. Sino que tienes que llevar (push) expresamente, cada vez, al remoto las ramas que desees compartir.
  ```sh
  $ git push [name-remoto] [name-rama-local]
  ```
  También puedes utilizar publicar una rama local con otro nombre en la rama en remoto, mediante el siguiente comando.
  ```sh
  $ git push [name-remoto] [name-rama-local]:[name-rama-remoto]
  ```

- **Seguimiento a las ramas remotas:** Las ramas de seguimiento son ramas locales que tienen una relación directa con alguna rama remota. Si estás en una rama de seguimiento y tecleas el comando git push, Git sabe automáticamente a qué servidor y a qué rama ha de llevar los contenidos. Igualmente, tecleando git pull mientras estamos en una de esas ramas, recupera (fetch) todas las referencias remotas y las consolida (merge) automáticamente en la correspondiente rama remota.

  - **Relacionar rama existente en local con rama remota:** Para realizar el seguimiento entre una rama ya creada en local y una rama en remoto se usa el siguiente comando:
    ```sh
    $ git branch --set-upstream-to=[name-remoto]/[name-rama-remoto] [name-rama-local]
    ```
  - **Crear una rama nueva en local y relacionarla con rama remota:** Para realizar el seguimiento entre una rama nueva sin crear en local y una rama en remoto se usa el siguiente comando:
    ```sh
    $ git checkout --track [name-remote]/[name-rama-remota]
    ```
    Para preparar una rama local con un nombre distinto a la del remoto, puedes utilizar:
    ```sh
    $ git checkout -b [name-rama-nueva-local] [nombre-remoto]/[rama-remoto]
    ```

- **Borrar ramas remotas:** Puedes borrar una rama remota utilizando el siguiente comando:
    ```sh
    $ git push [nombre-remoto] :[rama-remoto]
    ```

## Etiquetas
Git tiene la habilidad de etiquetar (tag) puntos específicos en la historia como importantes. Generalmente la gente usa esta funcionalidad para marcar puntos donde se ha lanzado alguna versión (v1.0, y así sucesivamente).

- **Listar etiquetas:** Listar las etiquetas disponibles en orden alfabético, el orden en el que aparecen no es realmente importante.
  ```sh
  $ git tag
  ```
  También se puede buscar etiquetas de acuerdo a un patrón en particular. Por ejemplo, si solo estás interesado en la serie 1.4.2:
  ```sh
  $ git tag -l 'v1.4.2.*'
  ```

- **Crear etiquetas:** Git usa dos tipos principales de etiquetas: ligeras y anotadas. Una etiqueta ligera es muy parecida a una rama que no cambia (un puntero a una confirmación específica). Sin embargo, las etiquetas anotadas son almacenadas como objetos completos en la base de datos de Git. Tienen suma de comprobación; contienen el nombre del etiquetador, correo electrónico y fecha; tienen mensaje de etiquetado; y pueden estar firmadas y verificadas con GNU Privacy Guard (GPG).
  - **Etiquetas anotadas:** Las etiquetas anotadas son almacenadas como objetos completos en la base de datos de Git.
    ```sh
    $ git tag -a [version (v1.2)] -m 'mensaje'
    ```

  - **Etiquetas firmadas:** Puedes firmar tus etiquetas con GPG, siempre que tengas una clave privada, lo único que debes hacer es usar -s en vez de -a:
    ```sh
    $ git tag -s [version (v1.2)] -m 'mensaje'
    ```

  - **Etiquetas ligeras:** Es básicamente la suma de comprobación de la confirmación almacenada en un archivo (ninguna otra información es guardada).
    ```sh
    $ git tag [version (v1.2)]
    ```

  - **Verificar etiquetas:** Para verificar una etiqueta firmada, se utiliza GPG para verificar la firma. Necesitas la clave pública del autor de la firma en tu llavero para que funcione correctamente.
    ```sh
    $ git tag -v [tag-name]
    ```

- **Etiquetar un commit anterior:** Para etiquetar una confirmación especifica la suma de comprobación de la confirmación (o una parte de la misma) al final del comando:
  ```sh
  $ git tag -a [tag-name] -m 'mensaje' [encabezado (9fceb02)]
  ```

- **Enviar etiquetas al servidor:** Enviarlas explícitamente a un servidor compartido después de haberlas creado.
  ```sh
  $ git push [nombre-remoto] [tagname]
  ```
  Para transferir todas las etiquetas que no estén ya en el servidor remoto.
  ```sh
  $ git push origin --tags
  ```

## Referencias
- [Pro Git](https://git-scm.com/book/es/v2). Scott Chacon, Ben Straub. 2nd Edition (2014). Apress.

## Licencia
Todo el contenido está bajo la licencia [Creative Commons Attribution Non Commercial Share Alike 3.0 license](https://creativecommons.org/licenses/by-nc-sa/3.0/).
