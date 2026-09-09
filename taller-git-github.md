# Taller de Git y GitHub — Primeros pasos

Taller introductorio pensado para estudiantes que nunca han usado control de versiones. Incluye teoría breve, la explicación de cómo conectar un repositorio local con GitHub, y ejercicios prácticos en parejas/grupos.

---

## 0. ¿Qué es el control de versiones?

Es un sistema que guarda "fotos" (versiones) del estado de tus archivos a lo largo del tiempo, para que puedas:

- Ver qué cambió, cuándo y quién lo cambió.
- Volver a una versión anterior si algo se rompe.
- Trabajar varias personas sobre el mismo proyecto sin pisarse el trabajo (o al menos, notando cuándo se pisan).

**Git** es la herramienta que hace esto en tu computador (local). **GitHub** es una plataforma en la nube donde se guardan copias de esos repositorios para compartirlos con otros.

---

## 1. Los tres estados de un archivo en Git

Antes de ver comandos, hay que entender por qué `add` y `commit` son dos pasos separados:

```
Working Directory  --git add-->  Staging Area  --git commit-->  Repository (historial)
   (tus archivos          (lo que vas a          (versión guardada
    tal cual los ves)      incluir en el          permanentemente)
                            próximo commit)
```

- **Working directory**: la carpeta del proyecto tal como la ves y editas.
- **Staging area** (o "índice"): una zona intermedia donde eliges *qué cambios específicos* quieres que entren en el próximo commit. Te deja armar commits a la medida (por ejemplo, solo 2 de los 5 archivos que modificaste).
- **Repository**: el historial permanente de commits ya guardados.

---

## 2. Comandos básicos

### `git init`
Convierte la carpeta actual en un repositorio de Git (crea una carpeta oculta `.git/` donde vive todo el historial).
```bash
git init
```

### `git status`
Muestra qué archivos cambiaron, cuáles están en staging y cuáles no, y en qué rama estás. Es el comando que más vas a usar — literalmente pregúntale a Git "¿cómo estoy?".
```bash
git status
```

### `git add`
Mueve cambios del *working directory* al *staging area*. Le dice a Git "esto quiero que entre en el próximo commit".
```bash
git add archivo.txt      # un archivo específico
git add .                # todos los cambios de la carpeta actual
```

### `git commit`
Guarda permanentemente lo que está en staging como una nueva versión del historial, con un mensaje que explica el cambio.
```bash
git commit -m "Agrego tablero inicial del triqui"
```

### `git log`
Muestra el historial de commits: quién, cuándo y qué mensaje dejó cada uno.
```bash
git log
git log --oneline    # versión compacta, una línea por commit
```

### `git branch`
Lista, crea o elimina **ramas**. Una rama es una línea de desarrollo independiente — te deja probar cosas sin afectar la rama principal (`main`) hasta que decidas unirlas.
```bash
git branch                # lista las ramas
git branch nueva-rama     # crea una rama nueva (sin moverte a ella)
```

### `git checkout` (y su primo moderno `git switch`)
Cambia entre ramas, o te lleva a un commit específico del historial. Desde versiones recientes de Git, `switch` (cambiar de rama) y `restore` (descartar cambios de archivos) se separaron de `checkout` porque hacía demasiadas cosas distintas — pero `checkout` sigue siendo el comando más universal y el que vas a encontrar en el 90% de las guías, así que este taller lo usa a él.
```bash
git checkout nombre-de-rama       # moverte a una rama existente
git checkout -b nombre-de-rama    # crear una rama Y moverte a ella en un solo paso
git checkout <hash-del-commit>    # viajar a un commit específico del pasado (¡ojo, sección 5!)
```

### `git merge`
Une los cambios de una rama dentro de otra. Normalmente: te paras en `main` y le dices "trae lo que se hizo en `otra-rama`".
```bash
git checkout main
git merge otra-rama
```
Si dos ramas cambiaron **la misma línea** de un archivo de formas distintas, Git no puede decidir solo y genera un **conflicto de merge** que hay que resolver a mano (lo practicamos en el ejercicio 5).

### `git remote`
Administra las conexiones a repositorios remotos (como uno en GitHub). Le pone un **alias** (por convención, `origin`) a una URL. Ver la sección 3 — ahí se aclara por qué esto *no* es lo mismo que "upstream".

### `git fetch`
Descarga los commits nuevos del remoto y actualiza tu copia local de sus ramas (`origin/main`, etc.), **sin tocar tu rama local ni mezclar nada todavía**. Es la forma segura de "espiar" qué cambió antes de traerlo de verdad.
```bash
git fetch origin
git log main..origin/main --oneline   # qué hay de nuevo, sin haberlo traído aún
```

### `git push`
Envía tus commits locales al repositorio remoto (por ejemplo, a GitHub).
```bash
git push
```

### `git pull`
Trae los commits que hay en el remoto y los mezcla con tu historial local. En el fondo es **`git fetch` + `git merge` en un solo paso**.
```bash
git pull
```

### Bonus — comandos y conceptos que vas a necesitar sí o sí

- **`git clone <url>`**: descarga una copia completa de un repositorio remoto que ya existe, con todo su historial. Es la forma más simple de empezar a trabajar en un repo que ya tiene contenido.
  ```bash
  git clone https://github.com/usuario/repo.git
  ```
- **`.gitignore`**: un archivo de texto donde listas qué archivos/carpetas NO quieres que Git rastree (ej. archivos temporales, contraseñas, carpetas de compilación).
- **Pull Request (PR)**: *no es un comando de Git*, es una función de GitHub. Es una propuesta de "quiero mezclar los cambios de mi rama a la rama principal" que otras personas pueden revisar y comentar antes de aprobar el merge. Es la base de cómo se colabora en proyectos reales.

---

## 3. Conectando con GitHub: push, pull y a qué repositorio apuntan

Esta sección responde las dos dudas más comunes al empezar.

### ¿Cómo le digo a Git a qué repositorio de GitHub apuntar?

Aquí se mezclan dos palabras que suenan parecido pero son cosas distintas: **`origin`** y **`upstream`**. Vale la pena separarlas bien porque es justo la confusión más común al empezar.

#### `origin` — el alias del remoto

Es solo un **nombre corto** para la URL de un repositorio remoto. Por convención se llama `origin`, pero eso es solo eso: una convención, un apodo.

```bash
git remote add origin https://github.com/usuario/mi-repo.git   # conectar por primera vez
git remote -v                                                  # ver a qué URL apunta "origin"
git remote set-url origin https://github.com/usuario/otro.git  # cambiar el remoto si me equivoqué
```

> **Dato curioso:** al trabajar con un *fork*, es normal tener DOS remotos a la vez — `origin` apuntando a tu fork, y otro remoto (frecuentemente llamado, literalmente, `upstream`) apuntando al repositorio original del que hiciste fork. Ese es un tercer uso de la palabra "upstream" — como nombre propio de un remoto — distinto del que viene a continuación.

#### `upstream` — la relación de seguimiento (tracking)

Esto **no es el nombre de un remoto**: es la configuración que liga tu rama local con una rama remota específica, para que `push`/`pull` sepan a dónde ir sin que se lo repitas cada vez. Se establece con `-u` (justamente, de *upstream*):

```bash
git push -u origin main
```

Ese `-u` deja guardado "mi rama `main` local sigue a `origin/main`". De ahí en adelante, `git push` y `git pull` solos ya saben con qué remoto y rama trabajar, sin que tengas que repetir `origin main` cada vez. Puedes ver el upstream de cada rama con:

```bash
git branch -vv
```

**En una frase:** `origin` es el remoto **(a dónde)**; `upstream` es el hilo que conecta tu rama local con una rama de ese remoto **(con cuál, automáticamente)**.

### ¿Cómo hago el primer `pull` si en GitHub no hay nada?

Depende de cómo creaste el repositorio en GitHub. Hay dos escenarios y es clave distinguirlos:

**Escenario A — Creaste el repo en GitHub completamente vacío** (sin marcar "Add a README").
No hay nada que traer todavía, así que **no empiezas con pull, empiezas con push**:

```bash
git init
git add .
git commit -m "Primer commit"
git remote add origin https://github.com/usuario/mi-repo.git
git push -u origin main
```

Si en este punto haces `git pull`, no falla, pero tampoco trae nada nuevo — simplemente no hay historial remoto todavía.

**Escenario B — Creaste el repo en GitHub CON contenido inicial** (README, licencia o `.gitignore` — la opción que GitHub ofrece marcada por defecto al crear un repo nuevo).
Aquí el remoto ya tiene un commit que tu repo local no tiene. Si intentas `git push` directamente, Git lo va a **rechazar** porque las historias no coinciden. Dos formas de resolverlo:

```bash
# Opción 1: traer primero lo que hay en GitHub y luego seguir trabajando
git pull origin main --allow-unrelated-histories

# Opción 2 (más simple para empezar): en vez de init + remote add,
# directamente clona el repo que ya existe en GitHub
git clone https://github.com/usuario/mi-repo.git
```

**Resumen rápido — ¿`clone` o `init` + `remote add`?**

| Situación | Qué usar |
|---|---|
| El repo de GitHub ya existe y tiene contenido | `git clone <url>` |
| Ya tengo una carpeta con archivos en mi PC y quiero subirla a un repo vacío de GitHub | `git init` + `git remote add origin` + `git push -u origin main` |
| Ya tengo una carpeta local y el repo de GitHub tiene README | `git init` + `git remote add origin` + `git pull origin main --allow-unrelated-histories` + trabajar normal |

---

## 4. Ejercicios prácticos

> **Nota de orden:** la conexión SSH a GitHub se configura en clase, paso a paso con el profesor. Mientras tanto, los ejercicios **1, 4 y 5** (y el "modo trampa" de la sección 5) son **100% locales** — no necesitan GitHub ni ningún tipo de autenticación, así que se pueden hacer desde ya. Los ejercicios **2, 3 y 6** sí necesitan el repo conectado a GitHub — se retoman después de la sesión de SSH. El historial que construyan hoy en local no se pierde: cuando conecten el remoto más adelante, un solo `git push -u origin main` sube todos esos commits tal cual quedaron.

### Ejercicio 1 — Calentamiento individual (`init`, `add`, `commit`, `status`, `log`) · Sin GitHub

1. Crea una carpeta nueva y entra en ella.
2. `git init`.
3. Crea un archivo `notas.txt` con una línea de texto.
4. `git status` — observa que aparece como "untracked".
5. `git add notas.txt`, luego `git status` de nuevo — ahora está en staging.
6. `git commit -m "Primer commit"`.
7. Agrega una segunda línea al archivo, repite `add` + `commit` con otro mensaje.
8. `git log --oneline` — deberían ver dos commits.

### Ejercicio 2 — Conexión a GitHub, en parejas · Requiere GitHub (SSH ya configurado)

1. Cada estudiante crea un repositorio en GitHub: **una persona lo crea vacío**, la **otra lo crea con README**.
2. Cada quien conecta su repo local (del ejercicio 1, o uno nuevo) siguiendo el escenario que le tocó (sección 3).
3. Verifiquen con `git remote -v` que apuntan al repo correcto.
4. Cada estudiante agrega a su compañero como colaborador en GitHub (Settings → Collaborators) para el siguiente ejercicio.

### Ejercicio 3 — Historia colaborativa ("cadáver exquisito"), grupos de 3-4 · Requiere GitHub

Objetivo: vivir el ciclo `pull → add → commit → push → pull...` en equipo.

1. Uno del grupo crea el repo en GitHub y agrega a los demás como colaboradores.
2. Crean un archivo `historia.txt` (o una lista de tareas, o un glosario de términos de electrónica digital — lo que el grupo prefiera) con una primera línea, y hacen el primer `push`.
3. Cada integrante, en orden, hace: `git pull` → agrega **una línea nueva** al final del archivo → `git add` → `git commit -m "..."` → `git push`.
4. Si alguien hace push sin haber hecho pull primero y el remoto ya cambió, Git va a rechazar el push — ese error es intencional: discutan por qué pasó y resuélvanlo con `git pull` antes de reintentar.
5. Al final, revisen `git log --oneline` completo: deberían ver un commit por cada aporte, con nombre de autor.

### Ejercicio 4 — Triqui con historial de versiones, en parejas · Sin GitHub (mismo computador)

Objetivo: usar `add`/`commit` como turnos de un juego y `log` como "replay" de la partida — sin necesitar todavía push/pull ni GitHub.

1. Un archivo `tablero.txt` representa el triqui, por ejemplo:
   ```
   _ _ _
   _ _ _
   _ _ _
   ```
2. Uno de los dos crea la carpeta, hace `git init`, crea el archivo y hace el primer commit del tablero vacío.
3. Se turnan el teclado en la misma máquina: cada jugador edita el archivo marcando su jugada (X u O), `git add tablero.txt`, `git commit -m "Jugada 1: X en (1,1)"`. El rival espera su turno, revisa el tablero (ya está ahí mismo, en el archivo) y responde con su propio commit.
4. Al terminar la partida, corran `git log --oneline` — el historial completo es literalmente el replay de la partida, jugada por jugada, sin haber tocado GitHub en ningún momento.

> **Para más adelante:** cuando tengan SSH configurado, pueden subir este mismo repositorio sin perder nada — crean el repo vacío en GitHub, y corren `git remote add origin git@github.com:usuario/repo.git` seguido de `git push -u origin main`. Todos los commits de la partida quedan ahí, con sus mensajes y fechas originales. Desde ese punto, una revancha ya sí puede jugarse a distancia con `push`/`pull` como en la idea original.

### Ejercicio 5 — Conflicto de merge provocado, en parejas o grupos · Sin GitHub

Objetivo: forzar un conflicto real de `merge` y aprender a resolverlo (no basta con la definición).

1. Partiendo de un repo con un archivo `frase.txt` que contiene una sola línea, por ejemplo `El favorito de la clase es...`.
2. Cada integrante crea su propia rama desde el mismo punto: `git checkout -b rama-<nombre>`.
3. **A propósito**, cada quien edita esa misma línea de forma distinta y hace commit en su rama.
4. Se paran en `main` y hacen `git merge rama-<nombre>` con la primera rama — debería fusionar sin problema.
5. Al hacer `git merge` con la segunda rama, Git va a marcar **conflicto** en el archivo, mostrando algo así:
   ```
   <<<<<<< HEAD
   El favorito de la clase es Ana
   =======
   El favorito de la clase es Luis
   >>>>>>> rama-luis
   ```
6. Editan el archivo a mano dejando el resultado que quieran (borrando las marcas `<<<<<<<`, `=======`, `>>>>>>>`), hacen `git add` sobre el archivo resuelto, y `git commit` para cerrar el merge.
7. Discusión de cierre: ¿por qué Git no pudo decidir solo? ¿Qué hubiera pasado si los cambios eran en líneas distintas del archivo?

### Ejercicio 6 (opcional, grupos grandes) — Galería en Pull Request · Requiere GitHub

1. Cada grupo trabaja en su propia rama agregando algo al proyecto común (arte ASCII, una entrada a un glosario, lo que aplique al curso).
2. Suben su rama a GitHub (`git push -u origin nombre-rama`) y abren un **Pull Request** hacia `main`.
3. Otro grupo revisa el PR en GitHub (comentarios, sugerencias) antes de aprobarlo y mergearlo.
4. Cierre: esto es, a grandes rasgos, cómo se colabora en proyectos reales de código abierto o de equipo.

---

## 5. "Modo trampa" — el ejercicio secreto con `checkout`

Este ejercicio se hace **sobre el tablero del triqui** (ejercicio 4, versión local) o sobre la historia colaborativa (ejercicio 3), después de tener varios commits en el historial. También es 100% local — `checkout` a un commit anterior no toca GitHub para nada.

1. Un estudiante corre `git log --oneline` y copia el hash de una jugada anterior (por ejemplo, 3 jugadas atrás).
2. Hace `git checkout <hash>`. Git le va a avisar algo como:
   ```
   You are in 'detached HEAD' state...
   ```
   Esto significa que ya no está parado en ninguna rama — está viendo el proyecto *tal como estaba* en ese commit del pasado.
3. **Ahora viene la trampa**: el estudiante edita el tablero como si fuera esa jugada anterior — por ejemplo, "corrige" la jugada del rival — y hace `commit`.
4. Pregunta para el grupo: *¿ese commit tramposo afecta la partida real en `main`?* Respuesta: **no**, mientras sigan en detached HEAD ese commit queda "flotando" sin pertenecer a ninguna rama, y si vuelven a `main` sin haber creado una rama ahí, ese commit se pierde (queda huérfano).
5. Para que la trampa "cuente" de verdad, tendrían que haber creado una rama en ese punto: `git checkout -b rama-tramposa` antes de comitear — ahí sí queda guardada como una línea de tiempo alterna, separada de la partida real, que podrían intentar mergear a `main` (y ahí el rival se daría cuenta, porque generaría un conflicto o un historial raro).
6. **Salir del susto**: para volver a la partida real, `git checkout main` (o el nombre de la rama donde estaban jugando).

**Moraleja del ejercicio**: `checkout` a un commit viejo es una forma segura de *mirar* el pasado sin romper nada — el peligro (o la trampa) solo se vuelve permanente si decides crear una rama ahí y trabajar sobre ella.
