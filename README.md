([Sobre cómo usar markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))

# Tests
Testing examples with various suites and tasks automating
* Testing with various git functionalities: branch, pull requests
Last changed: 13 june, 2019.

#About git:
## git fetch
The git fetch command downloads commits, files, and refs from a remote repository into your local repo. Fetching is what you do when you want to see what everybody else has been working on.

Examples:
```
$ git fetch origin
$ git fetch [remote-name]
```

## git remote show
Esto lista la URL del repositorio remoto, así como información sobre las ramas bajo seguimiento. Este comando te recuerda que si estás en la rama maestra y ejecutas git pull, automáticamente unirá los cambios a la rama maestra del remoto después de haber recuperado todas las referencias remotas. También lista todas las referencias remotas que ha recibido. Este comando muestra qué rama se envía automáticamente cuando ejecutas git push en determinadas ramas. También te muestra qué ramas remotas no tienes todavía, qué ramas remotas tienes y han sido eliminadas del servidor, y múltiples ramas que serán unidas automáticamente cuando ejecutes git pull.

Ejemplo:
```
git remote show [origin|branch]
```

Ejemplo:
```
$ git remote show origin
* remote origin
  URL: git@github.com:defunkt/github.git
  Remote branch merged with 'git pull' while on branch issues
    issues
  Remote branch merged with 'git pull' while on branch master
    master
  New remote branches (next fetch will store in remotes/origin)
    caching
  Stale tracking branches (use 'git remote prune')
    libwalker
    walker2
  Tracked remote branches
    acl
    apiv2
    dashboard2
    issues
    master
    postgres
  Local branch pushed with 'git push'
    master:master
```

## git checkout
Para obtener datos de la rama indicada (revisar branching y checkout) 

## Branching & checkout
(fuente: https://git-scm.com/book/es/v1/Ramificaciones-en-Git-Procedimientos-b%C3%A1sicos-para-ramificar-y-fusionar) Decides trabajar el problema #53, creas la ramificación en un paso con:
```
$ git checkout -b iss53
```

Esto es un shorthand de 
```
$ git branch iss53
$ git checkout iss53
```
Luego de algunos commits recibes una llamada avisándote de otro problema urgente, hemos de tener en cuenta que teniendo cambios aún no confirmados en la carpeta de trabajo o en el área de preparación, Git no nos permitirá saltar a otra rama con la que podríamos tener conflictos. Lo mejor es tener siempre un estado de trabajo limpio y despejado antes de saltar entre ramas. Y, para ello, tenemos algunos procedimientos (stash y commit ammend).

```
$ git checkout master
Switched to branch "master"
$ git checkout -b 'hotfix'
Switched to a new branch "hotfix"
$ vim index.html
$ git commit -a -m 'fixed the broken email address'
[hotfix]: created 3a0874c: "fixed the broken email address"
 1 files changed, 0 insertions(+), 1 deletions(-)
```

Puedes realizar las pruebas oportunas, asegurarte que la solución es correcta, e incorporar los cambios a la rama master para ponerlos en producción. Esto se hace con el comando git merge:

```
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast forward
 README |    1 -
 1 files changed, 0 insertions(+), 1 deletions(-)
```

Merece destacar la frase "Avance rápido" ("Fast forward") que aparece en la respuesta al comando. Git ha movido el apuntador hacia adelante, ya que la confirmación apuntada en la rama donde has fusionado estaba directamente "aguas arriba" respecto de la confirmación actual. Dicho de otro modo: cuando intentas fusionar una confirmación con otra confirmación accesible siguiendo directamente el registro de la primera; Git simplifica las cosas avanzando el puntero, ya que no hay ningún otro trabajo divergente a fusionar. Esto es lo que se denomina "avance rápido" ("fast forward").

Ahora, los cambios realizados están ya en la instantánea (snapshot) de la confirmación (commit) apuntada por la rama master. Y puedes desplegarlos. Tras la fusión (merge), la rama master apunta al mismo sitio que la rama hotfix.

Tras haber resuelto el problema urgente que te había interrumpido tu trabajo, puedes volver a donde estabas. Pero antes, es interesante borrar la rama hotfix. Ya que no la vamos a necesitar más, puesto que apunta exactamente al mismo sitio que la rama master. Esto lo puedes hacer con la opción -d del comando git branch:

```
$ git branch -d hotfix
Deleted branch hotfix (3a0874c).
```

Regresar al trabajo sobre el problema #53:

```
$ git checkout iss53
Switched to branch "iss53"
$ vim index.html
$ git commit -a -m 'finished the new footer [issue 53]'
[iss53]: created ad82d7a: "finished the new footer [issue 53]"
 1 files changed, 1 insertions(+), 0 deletions(-)
```

Cabe indicar que todo el trabajo realizado en la rama hotfix no está en los archivos de la rama iss53. Si fuera necesario agregarlos, puedes fusionar (merge) la rama master sobre la rama iss53 utilizando el comando git merge master. O puedes esperar hasta que decidas llevar (pull) la rama iss53 a la rama master.


Supongamos que tu trabajo con el problema #53 está ya completo y listo para fusionarlo (merge) con la rama master. Para ello, de forma similar a como antes has hecho con la rama hotfix, vas a fusionar la rama iss53. Simplemente, activando (checkout) la rama donde deseas fusionar y lanzando el comando git merge:

```
$ git checkout master
$ git merge iss53
Merge made by recursive.
 README |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)
```

Es algo diferente de la fusión realizada anteriormente con hotfix. En este caso, el registro de desarrollo había divergido en un punto anterior. Debido a que la confirmación en la rama actual no es ancestro directo de la rama que pretendes fusionar, Git tiene cierto trabajo extra que hacer. Git realizará una fusión a tres bandas, utilizando las dos instantáneas apuntadas por el extremo de cada una de las ramas y por el ancestro común a ambas dos.

En lugar de simplemente avanzar el apuntador de la rama, Git crea una nueva instantánea (snapshot) resultante de la fusión a tres bandas; y crea automáticamente una nueva confirmación de cambios (commit) que apunta a ella. Nos referimos a este proceso como "fusión confirmada". Y se diferencia en que tiene más de un padre.

Merece la pena destacar el hecho de que es el propio Git quien determina automáticamente el mejor ancestro común para realizar la fusión. Diferenciandose de otros sistemas tales como CVS o Subversion, donde es el desarrollador quien ha de imaginarse cuál puede ser dicho mejor ancestro común. Esto hace que en Git sea mucho más fácil el realizar fusiones.

Ahora que todo tu trabajo está ya fusionado con la rama principal, ya no tienes necesidad de la rama iss53. Por lo que puedes borrarla. Y cerrar manualmente el problema en el sistema de seguimiento de problemas de tu empresa.

```
$ git branch -d iss53
$ git push --delete nombre_remoto iss53
```

Usualmente el nombre_remot_ es _origin_

Puedes ver todas las ramas con:

```
$ git branch --all
$ git branch -a
```

Para las ramas sólo remotas:

```
$ git branch -r
```

[Más información en este enlace!](https://git-scm.com/book/no-nb/v1/Git-Branching-Remote-Branches)

*La rama actual es la que a su izquierda tiene un asterisco*

## Tags
El tag le da un nombre de fácil asociación a los cambios guardados por el último commit de la rama en la que estás.

Listar

```
$ git tag 
```

Añadir

```
$ git tag nombre_tag
```

Subir, es igual a actualizar un branch

```
$ git push --delete nombre_remoto tag
```
Respuesta:
```
 * [new tag]         documentacion_v1.1 -> documentacion_v1.1
```

