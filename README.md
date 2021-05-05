# Git

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
10. [Submodulos](#submodulos)
11. [Comandos Avanzados](#comandos-avanzados)
  - [Cherry-pick](#cherry-pick)
  - [Stash](#stash)
  - [Bisect](#bisect)
  - [Blame](#blame)
  - [Reflog](#reflog)
12. [Guiás](#guias)
  - [Cambiar direcciones URL remotas de SSH a HTTPS](#cambiar-direcciones-url-remotas-de-ssh-a-https)
  - [Cambiar direcciones URL remotas de HTTPS a SSH](#cambiar-direcciones-url-remotas-de-https-a-ssh)
  - [Generar una llave GPG](#generar-una-llave-gpg)
  - [Informarle a Git acerca de tu llave GPG](#informarle-a-git-acerca-de-tu-llave-gpg)
  - [Asociar un correo electrónico con tu llave GPG](#asociar-un-correo-electrónico-con-tu-llave-gpg)
13. [Referencias](#referencias)

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

### Conexión SSH
#### Github
##### Generar una nueva clave SSH
1. Abre la Terminal.
2. Pega el siguiente texto, que sustituye tu dirección de correo electrónico en GitHub.
```sh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
Esto crea una nueva clave ssh usando el correo electrónico proporcionado como etiqueta.
  > Generating public/private rsa key pair.

3. Cuando se te indique "Ingresar un archivo donde guardar la clave", presiona Intro. Al hacerlo aceptas la ubicación predeterminada del archivo.
    > Enter a file in which to save the key (/home/you/.ssh/id_rsa): [Press enter]

4. Donde se indica, escribe una contraseña segura. Para obtener más información, consulta "[Trabajar con frases de contraseña de la clave SSH](https://docs.github.com/es/articles/working-with-ssh-key-passphrases)".
    > Enter passphrase (empty for no passphrase): [Type a passphrase]

    > Enter same passphrase again: [Type passphrase again]

##### Agregar tu clave SSH al ssh-agent
Antes de agregar una nueva clave SSH al ssh-agent para gestionar tus claves, debes haber comprobado las claves SSH existentes y generado una nueva clave SSH.

1. Inicia el agente SSH en segundo plano.
```sh
eval "$(ssh-agent -s)"
```
2. Agrega tu llave privada SSH al ssh-agent. Si creaste tu llave con un nombre distinto, o si estás agregando una llave existente que tiene un nombre distinto, reemplaza id_rsa en el comando con el nombre de tu archivo de llave privada.
```sh
ssh-add ~/.ssh/id_rsa
```
3. [Agrega la clave SSH a tu cuenta de GitHub](https://docs.github.com/es/articles/adding-a-new-ssh-key-to-your-github-account)

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
  Para realizar un clone superficial con un historial truncado al número de confirmaciones especificado. Implica `--single-branch` a menos que se indique `--no-single-branch` para obtener los historiales cercanos a las puntas de todas las ramas. Si quiere clonar submódulos de forma superficial, pase también --shallow-submodules, **(Util para CI)**:
  ```sh
  $ git clone <url> --depth <depth>
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

- **Commits verificados firmados con PGP:**
  ```sh
  $ git commit -S -m your commit message
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
Para poder ver los ficheros modificados en cada commit del histórico de confirmaciones:
```sh
$ git log --stat
```
Visualizar el historial de forma minimalista:
```sh
$ git log --graph --oneline --decorate --all
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
Para visualizar información de un commit concreto se usa:
```sh
$ git show [commit]
```

## Deshacer cosas
 Ésta es una de las pocas áreas de Git que pueden provocar que pierdas datos si haces las cosas incorrectamente.

### Reset
```sh
$ git reset [mode] [commit]
```
Esta forma restablece la cabeza (HEAD) de rama actual a [commit] y posiblemente actualiza el índice (estableciéndolo al árbol de [commit]) y el área de trabajo dependiendo de [mode]. Si se omite [modo], el valor por defecto es *--mixed*. El [modo] debe ser uno de los siguientes:
  - **Soft:** Elimina el commit y devuelve los ficheros confirmados en dicho commit a área de preparación.
    ```sh
    $ git reset --soft [commit]
    ```
  - **Mixed:** Elimina el commit y devuelve los ficheros confirmados en dicho commit a área de trabajo.
    ```sh
    $ git reset --mixed [commit]
    ```
    o
    ```sh
    $ git reset [commit]
    ```
  - **Hard:** Elimina el commit y los cambios realizados en los ficheros modificados del dicho commit desaparecen.
    ```sh
    $ git reset --hard [commit]
    ```

#### Casos de uso
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
  Estos tres comandos acabarán convirtiéndose en una única confirmación y la segunda confirmación reemplazará los resultados de la primera.

- **Deshaciendo la preparación de un archivo:** Cuando quieres sacar un archivo del área de preparación.
  ```sh
  $ git reset HEAD <archivo>
  ```

- **Resetear un fichero a su estado en e repositorio:** Cuando quieres resetear un fichero a su estado en el área de repositorio:
  ```sh
  $ git checkout <fichero>
  ```

### Restore
#### Casos de uso
- **Eliminar uno ficheros de la zona *staged*:**
```sh
$ git restore --stage [fichero]
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

- **Cambiar la URL de un remoto:** Para cambia una URL de un repositorio remoto existente.
```sh
$ git remote set-url [remoto existente] [nueva URL]
```
  - Un nombre de **remoto existente**. Por ejemplo, origin o upstream son dos de las opciones comunes.
  - Una **nueva URL** para el remoto. Por ejemplo:
    - Si estás actualizando para usar HTTPS, tu URL puede verse como:
        ```
        https://github.com/USERNAME/REPOSITORY.git
        ```
    - Si estás actualizando para usar SSH, tu URL puede verse como:
        ```
        git@github.com:USERNAME/REPOSITORY.git
        ```
- **Comprobar existencia de repositorio remoto:** Comprueba que el nombre del remoto es correcto.
```sh
$ git remote set-url sofake [URL repositorio remoto]
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

## Submodulos
Los submódulos permiten mantener un repositorio de Git como un subdirectorio de otro repositorio de Git. Esto permite clonar otro repositorio en un proyecto y mantener sus commits separados.
- **Agregar nuevo submódulo:** Permite Agregar un repositorio de Git existente como un submódulo del repositorio en el que estamos trabajando. Para agregar un nuevo submódulo, se utiliza el comando *git submodule add* con la *URL* del proyecto que desea empezar a rastrear.
  ```sh
  $ git submodule add [URL]
  ```
  Por defecto, los submódulos agregarán el subproyecto a un directorio llamado igual que el repositorio y añadiran un nuevo archivo *.gitmodules*. Este es un archivo de configuración que almacena la asignación entre la URL del proyecto y el subdirectorio local en el que lo ha insertado:
  ```
  [submodule "[name_submodule]"]
  	path = [directorio_submodule]
  	url = [URL]
  ```
  Si tiene múltiples submódulos, tendrá múltiples entradas en este archivo. Es importante tener en cuenta que este archivo está controlado por la versión con sus otros archivos, como su archivo .gitignore.
  **Nota:** Puede sobrescribir el valor de la *URL* localmente usar `git config submodule.[name_submodule].url PRIVATE_URL` para su propio uso.

- **Clonar un repositorio con submodulos:** Con *--recursive* en el comando *git clone*, se inicializará y actualizará automáticamente cada submódulo en el repositorio.
  ```sh
  $ git clone --recursive [URL]
  ```
- **Actualizar todos los submódulos:** Git permite buscar todos los submódulos y actualizar todos los submodulos automaticamente.
  ```sh
  git submodule update --remote [nombre_submodulo] # [nombre_submodulo] opcional
  ```

## Comandos Avanzados
## Cherry-pick
El comando *git cherry-pick* permite copiar **uno** o **varios** commints y pegarlos en otra rama. Para ello hay que posicionarse en la rama a la que se quiere traer el o los commits.
```sh
$ git checkout [rama]
$ git cherry-pick [commit/s]
```
Opciones:
- **-n:** Permite traer los cambios realizados en el commits o commits selecionados y deja esos cambios en el area de preparacion, sin crear un nuevo commit o commits.
```sh
$ git cherry-pick -n [commit] [commit]
```

## Stash
Con *stash* se toma el estado actual del directorio de trabajo – que es, tus archivos controlados por la versión modificados y cambios almacenados – y lo guarda en una pila de cambios sin terminar que puedes volver a usar en cualquier momento.
- **Crear un punto de guardado:** Permite crear el estado actual del directorio de trabajo.
  ```sh
  $ git stash
  ```
- **Visualizar puntos de guardado almacenados:** Para visualizar los puntos de guardados que forman la pila:
  ```sh
  $ git stash list
  ```
- **Retornar a estado guardado:** Existen dos formas de volver al estado del directorio de trabajo almacenado en en la pila de puntos de guardado:
  - **apply:** Devuelve el punto de guardado almacenado sin eliminarlo de la pila de guardado.
    ```sh
    $ git stash apply stash@{[id]}
    ```
  - **pop:** Devuelve el punto de guardado almacenado y lo elimina de la pila de guardado.
    ```sh
    $ git stash pop stash@{[id]}
    ```
- **Eliminar punto de guardado:** Para eliminar un punto de guardado.
  ```sh
  $ git stash drop stash@{[id]}
  ```
- **Crear una rama desde un punto de guardado:** Crear una nueva rama desde un punto de guardado, verifica el “commit” en el que estaba cuando se realizo el guardado rápido, recrea el trabajo allí, y luego arroja el guardado rápido si la entrada se realiza con éxito.
  ```sh
  $ git stash branch [rama]
  ```

## Bisect
El comando `bisect` hace una búsqueda binaria a través de su historial de commits para ayudarte a identificar lo más rápidamente posible qué commit introdujo un problema. Para comenzar la busqueda del commit que introdujo el error, primero hay que ejecuta `git bisect start` para para comenzar la busqueda, y luego usar `git bisect bad` para decirle al sistema que el “commit” actual está roto. Entonces, debes decir a `bisect` cuándo fue el último estado bueno conocido, usando `git bisect good [good_commit]`:
```sh
$ git bisect start
$ git bisect bad [bad_commit] # Por ejemplo el commit HEAD
$ git bisect good [good_commit]
```
A continuación git calcula el commit que se encuentra en la mitad entre el commit bad y el good, para que se pueda comprobar si en ese commit se encuentra el error que se busca. Si el error esta presente en el commit hay que introducir `git bisect bad` o `git bisect good` en caso de no estar presente el error. Este proceso se realizara hasta encontrar el primer commit en donde esta presente el error. Cuando haya terminado, se debe ejecutar `git bisect reset` para reiniciar el HEAD a donde estaba antes de comenzar, o terminará en un estado raro:
```sh
$ git bisect reset
```
**Nota:** Con un script que retornare 0 si el proyecto está bien u otro número si el proyecto está mal, se puede automatizar completamente `git bisect`. En primer lugar, se indica el alcance de la bisectriz, proporcionando los “commits” malos y buenos. Se puede hacer enumerándolos con el comando `bisect start`, listando primero el “commit” malo conocido y segundo el “commit” bueno conocido:
```sh
$ git bisect start [bad_commit] [good_commit]
$ git bisect run test-error.sh
```
Esto ejecuta automáticamente test-error.sh en cada “commit” de “check-out” hasta que Git encuentre el primer “commit” roto. También se puede ejecutar algo como make o `make tests` o lo que sea que ejecute pruebas automatizadas.

## Blame
Con `git blame` se muestra qué commit fue el último en modificar cada línea de cualquier archivo y por quién.
```sh
$ git blame [archivo]
```
Ademas, con la opcion `-L` se puede limitar la salida con una linea inicial y una final:
```sh
$ git blame -L [linea_inicial],[linea_final] [archivo]
```
El primer campo que muestra la salida es el SHA-1 parcial del “commit” que modificó esa línea. Los siguientes dos campos son valores extraídos del “commit” - el nombre del autor y la fecha del commit - así se puede ver de manera sencilla quién modificó esa línea y cuándo. Tras estos viene el número de línea y el contenido del archivo.

## Reflog
Una de las cosas que Git hace en segundo plano, mientras estás trabajando, es mantener un “reflog” - un log de a dónde se apuntan las referencias del HEAD y la rama en los últimos meses.
```sh
$ git reflog

# Ejemplo de salida
734713b HEAD@{0}: commit: fixed refs handling, added gc auto, updated
d921970 HEAD@{1}: merge phedders/rdocs: Merge made by recursive.
1c002dd HEAD@{2}: commit: added some blame and merge stuff
1c36188 HEAD@{3}: rebase -i (squash): updating HEAD
95df984 HEAD@{4}: commit: # This is a combination of two commits.
1c36188 HEAD@{5}: rebase -i (squash): updating HEAD
7e05da5 HEAD@{6}: rebase -i (pick): updating HEAD
```
Cada vez que el HEAD de la rama es actualizada por cualquier razón, Git guarda esa información en este historial temporal. Y es así como se puede especificar commits antiguos con esta información. Si se quiere ver el quinto valor anterior a tu HEAD en el repositorio, se puede usar la referencia @{n} que se ve en la salida de reflog:
```sh
$ git show HEAD@{5}
```
También se puede utilizar esta sintaxis para ver dónde se encontraba una rama dada una cierta cantidad de tiempo. Por ejemplo, para ver dónde se encontraba la rama master ayer, se puede utilizar
```sh
$ git show master@{yesterday}
```
Esto muestra a dónde apuntaba tu rama el día de ayer. Esta técnica solo funciona para información que permanece en tu reflog, por lo que no se puede utilizar para ver commits que son anteriores a los que aparecen en él.

**Nota:** Es importante notar que la información de reflog es estríctamente local, es un log de lo que se ha hecho en el repositorio local. Las referencias no serán las mismas en otra copia del repositorio; y justo después de que se ha inicializado el repositorio, se tendrá un reflog vacío, dado que no ha ocurrido ninguna actividad todavía en el mismo. Utilizar `git show HEAD@{2.months.ago}` funcionará solo si se clonó el proyecto hace al menos dos meses - si se clonó hace cinco minutos, no se obtendrán resultados.

### Caso de uso
Se ha realizado un `git reset --hard HEAD~3` y se han pedido algunos commits que no querías perder. Con `git reflog` se puede observar cual es la referencia previa del HEAD a dicho reset para volver a ese punto utilizando `git reset --hard HEAD@{[id_deseado]}`, así se volvería al estado anterior al primer `git reset`.

## Guiás
### Cambiar direcciones URL remotas de SSH a HTTPS
1. Abre la Terminal.
2. Cambiar el directorio de trabajo actual en tu proyecto local.
3. Enumerar tus remotos existentes a fin de obtener el nombre de los remotos que deseas cambiar.
```sh
git remote -v
```
3. Cambiar tu URL remota de SSH a HTTPS con el comando git remote set-url.
```sh
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
```
4. Verificar que la URL remota ha cambiado.
```sh
git remote -v
# Verify new remote URL
> origin  https://github.com/USERNAME/REPOSITORY.git (fetch)
> origin  https://github.com/USERNAME/REPOSITORY.git (push)
```

La próxima vez que ejecutes git, git pull o git push en el repositorio remoto, se te pedirá el nombre de usuario y la contraseña de GitHub.

1. Si tienes habilitada la [autenticación de dos factores](https://docs.github.com/es/articles/securing-your-account-with-two-factor-authentication-2fa), debes [crear un token de acceso personal](https://docs.github.com/es/github/authenticating-to-github/creating-a-personal-access-token) para usar en lugar de tu contraseña de GitHub.
2. Puedes [utilizar un ayudante de credenciales](https://docs.github.com/es/github/using-git/caching-your-github-credentials-in-git) para que Git recuerde tu nombre de usuario y contraseña cada vez que se comunique con GitHub.

### Cambiar direcciones URL remotas de HTTPS a SSH
1. Abre la Terminal.
2. Cambiar el directorio de trabajo actual en tu proyecto local.
3. Enumerar tus remotos existentes a fin de obtener el nombre de los remotos que deseas cambiar.
```sh
$ git remote -v
> origin  https://github.com/USERNAME/REPOSITORY.git (fetch)
> origin  https://github.com/USERNAME/REPOSITORY.git (push)
```
4. Cambiar tu URL remota de HTTPS a SSH con el comando git remote set-url.
```sh
$ git remote set-url origin git@github.com:USERNAME/REPOSITORY.git
```
5. Verificar que la URL remota ha cambiado.
```sh
$ git remote -v
# Verify new remote URL
> origin  git@github.com:USERNAME/REPOSITORY.git (fetch)
> origin  git@github.com:USERNAME/REPOSITORY.git (push)
```

### Generar una llave GPG
1. Descarga e instala las herramientas de la línea de comando GPG para tu sistema operativo. Generalmente recomendamos instalar la versión más reciente para tu sistema operativo.
2. Abre la Terminal.
3. Genera un par de la llave GPG. Ya que existen varias versiones de GPG, puede que necesites consultar la página man relevante para encontrar el comando adecuado para la generación de llaves. Tu llave debe utilizar RSA.
  - Si estás usando una versión 2.1.17 o superior, copia el siguiente texto para generar un par de la llave GPG.
    ```sh
    $ gpg --full-generate-key
    ```
  - Si no estás usando la versión 2.1.17 ni una superior, el comando gpg --full-generate-key no funciona. Copia el siguiente texto y continúa con el paso 6.
    ```sh
    $ gpg --default-new-key-algo rsa4096 --gen-key
    ```
4. En el prompt, especifica la clase de llave que quieres, o presiona Enter para aceptar el RSA y DSA predeterminado.
5. Ingresa el tamaño de la llave que deseas. Tu llave debe ser de al menos 4096 bits.
6. Ingresa el periodo de validez que deberá tener la llave. Presiona Enter para especificar la selección predeterminada, indicando que la llave no expira.
7. Verifica que tus selecciones sean correctas.
8. Ingresa tu información de ID de usuario.
  - **Nota:** Cuando se te pida que ingreses tu dirección de correo electrónico, asegúrate de ingresar la dirección de correo electrónico verificada para tu cuenta Github. Para mantener tu dirección de correo electrónico como privada, utiliza tus direcciones de tipo no-reply proporcionadas por GitHub. Para obtener más información, consulta "[Verificar tu dirección de correo electrónico](https://docs.github.com/es/articles/verifying-your-email-address)" y "[Establecer tu dirección de correo electrónico para confirmaciones](https://docs.github.com/es/articles/setting-your-commit-email-address)".
9. Escribe una contraseña segura.
10. Utiliza el comando `gpg --list-secret-keys --keyid-format LONG` para enumerar las llaves GPG para las cuales tienes tanto una llave pública como privada. Se requiere una llave privada para registrar confirmaciones o etiquetas.
  ```sh
  $ gpg --list-secret-keys --keyid-format LONG
  ```
  **Nota:** Algunas instalaciones GPG en Linux pueden requerir que uses gpg2 --list-keys --keyid-format LONG para visualizar una lista de tus llaves existentes en su lugar. En este caso también deberás configurar Git para que use gpg2 by running git config --global gpg.program gpg2.
11. De la lista de llaves GPG, copia la ID de la llave GPG que quieres utilizar. En este ejemplo, el ID de la llave GPG es 3AA5C34371567BD2:
```sh
$ gpg --list-secret-keys --keyid-format LONG
/Users/hubot/.gnupg/secring.gpg
/------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```
12. Pega el siguiente texto sustituyendo el ID de la llave GPG que deseas usar. En este ejemplo, el ID de la llave GPG es 3AA5C34371567BD2:
```sh
$ gpg --armor --export 3AA5C34371567BD2
# Prints the GPG key ID, in ASCII armor format
```
13. Copia tu llave GPG, comenzando con *-----BEGIN PGP PUBLIC KEY BLOCK-----* y terminando con *-----END PGP PUBLIC KEY BLOCK-----*.
14. [Agrega la llave GPG a tu cuenta de GitHub](https://docs.github.com/es/articles/adding-a-new-gpg-key-to-your-github-account).

### Informarle a Git acerca de tu llave GPG
Si estás usando una llave GPG que coincide con la identidad de la persona que confirma el cambio y con tu dirección de correo electrónico verificada asociada a tu GitHub cuenta, puedes comenzar a firmar confirmaciones y firmar etiquetas.
Si tienes múltiples llaves GPG, le debes decir a Git cuál utilizar.

1. Abre la Terminal.

2. Utiliza el comando gpg --list-secret-keys --keyid-format LONG para enumerar las llaves GPG para las cuales tienes tanto una llave pública como privada. Se requiere una llave privada para registrar confirmaciones o etiquetas.
```sh
$ gpg --list-secret-keys --keyid-format LONG
```
  **Nota:** Algunas instalaciones GPG en Linux pueden requerir que uses gpg2 --list-keys --keyid-format LONG para visualizar una lista de tus llaves existentes en su lugar. En este caso también deberás configurar Git para que use gpg2 by running git config --global gpg.program gpg2.

3. De la lista de llaves GPG, copia la ID de la llave GPG que quieres utilizar. En este ejemplo, el ID de la llave GPG es 3AA5C34371567BD2:
```sh
  $ gpg --list-secret-keys --keyid-format LONG
  /Users/hubot/.gnupg/secring.gpg
  ------------------------------------
  sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
  uid                          Hubot
  ssb   4096R/42B317FD4BA89E7A 2016-03-10
```
4. Para configurar tu llave de firma GPG en Git, pega el siguiente texto en sustitución de la ID de la llave GPG que quieras utilizar. En este ejemplo, el ID de la llave GPG es 3AA5C34371567BD2:
```sh
$ git config --global user.signingkey 3AA5C34371567BD2
```
5. Para agregar tu llave GPG a tu perfil bash, pega el texto que aparece a continuación:
```sh
    $ test -r ~/.bash_profile && echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile
    $ echo 'export GPG_TTY=$(tty)' >> ~/.profile
```
    **Nota:** Si no tienes .bash_profile, este comando agrega tu llave GPG al .profile.


### Asociar un correo electrónico con tu llave GPG
Tu llave GPG debe estar asociada con un correo electrónico verificado de GitHub que coincida con tu identidad de persona que confirma el cambio.

1. Abre la Terminal.
2. Utiliza el comando gpg --list-secret-keys --keyid-format LONG para enumerar las llaves GPG para las cuales tienes tanto una llave pública como privada. Se requiere una llave privada para registrar confirmaciones o etiquetas.
```sh
$ gpg --list-secret-keys --keyid-format LONG
```
    **Nota:** Algunas instalaciones GPG en Linux pueden requerir que uses gpg2 --list-keys --keyid-format LONG para visualizar una lista de tus llaves existentes en su lugar. En este caso también deberás configurar Git para que use gpg2 by running git config --global gpg.program gpg2.

3. De la lista de llaves GPG, copia la ID de la llave GPG que quieres utilizar. En este ejemplo, el ID de la llave GPG es 3AA5C34371567BD2:
```sh
  $ gpg --list-secret-keys --keyid-format LONG
  /Users/hubot/.gnupg/secring.gpg
  ------------------------------------
  sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
  uid                          Hubot
  ssb   4096R/42B317FD4BA89E7A 2016-03-10
```
4. Escribe gpg --edit-key GPG key ID, sustituyendo la ID de la llave GPG que te gustaría usar. En el siguiente ejemplo, el ID de llave GPG es 3AA5C34371567BD2:
```sh
$ gpg --edit-key 3AA5C34371567BD2
```
5. Escribe gpg> adduid para agregar los detalles de ID de usuario.
```sh
$ gpg> adduid
```
6. Sigue las indicaciones para suminsitrar tu nombre real, dirección de correo electrónica o cualquier comentario. Puedes modificar tus entradas al elegir N, C o E. Para mantener tu dirección de correo electrónico como privada, utiliza tus direcciones de tipo no-reply proporcionadas por GitHub. Para obtener más información, consulta "[Configurar la confirmación de tu dirección de correo electrónico.](https://docs.github.com/es/articles/setting-your-commit-email-address)"
```
  Real Name: Octocat
    Email address: octocat@github.com
    Comment: GitHub key
    Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
```
7. Escribe O para guardar tus selecciones.
8. Escribe la contraseña de tu llave.
9. Escribe gpg --armor --export GPG key ID, sustituyendo la ID de la llave GPG que te gustaría usar. En el siguiente ejemplo, el ID de llave GPG es 3AA5C34371567BD2:
```sh
$ gpg --armor --export 3AA5C34371567BD2
# Prints the GPG key, in ASCII armor format
```
10. Carga la llave GPG al [agregarla a tu cuenta GitHub](https://docs.github.com/es/articles/adding-a-new-gpg-key-to-your-github-account).


### Eliminar clave GPG
1. Para eliminar una clave GPG de GitHub, ve a https://github.com/settings/keys y en claves GPG, selecciona la que quieras y elimínala.
2. Eliminar la clave GPG de la configuracion de Git:
```sh
git config --global --unset user.signingkey
```
3. Desactivar globalmente la clave de firma GPG para los commits
```sh
git config --global commit.gpgsign false
```


## Referencias
- [Pro Git](https://git-scm.com/book/es/v2). Scott Chacon, Ben Straub. 2nd Edition (2014). Apress.

## Licencia
Todo el contenido está bajo la licencia [Creative Commons Attribution Non Commercial Share Alike 3.0 license](https://creativecommons.org/licenses/by-nc-sa/3.0/).
