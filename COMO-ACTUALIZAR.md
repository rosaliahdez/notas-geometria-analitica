# Cómo actualizar el libro de Geometría Analítica

Bitácora de los procedimientos que se repiten, escrita para no tener que
acordarse de nada. Es el mismo flujo del libro de Estadística.

---

## 1. Publicar el libro en GitHub

El libro vive en dos lugares. El **código fuente** (los `.qmd`) está en la rama
`main` del repositorio, y el **sitio que ven los estudiantes** está en la rama
`gh-pages`, que Quarto genera y actualiza sola. Nunca hay que tocar `gh-pages` a
mano.

La dirección del sitio es

```
https://rosaliahdez.github.io/notas-geometria-analitica/
```

### La primera vez, sólo una

El repositorio ya está creado en GitHub, y esta carpeta todavía no es un
repositorio de Git. Estos pasos la conectan.

**a. Que RStudio vea a Git.** `Tools → Global Options → Git/SVN`. El campo *Git
executable* debe apuntar a `git.exe`, normalmente
`C:/Program Files/Git/bin/git.exe`. Si está vacío, falta instalar Git for
Windows y volver a abrir RStudio.

**b. Limpiar.** En la pestaña **Files**, borrar la carpeta `_to_delete`.

**c. Crear el repositorio local.** En la consola de R:

```r
usethis::use_git()
```

Pregunta si hace el primer commit con todo lo que hay y si reinicia RStudio. A
las dos, que sí. Al volver aparece la pestaña **Git**.

**d. Poner la rama, el remoto y el primer envío.** `use_git()` suele dejar la
rama con el nombre `master`, y hasta aquí la carpeta no sabe nada del
repositorio de GitHub. Las dos cosas se arreglan en la pestaña **Terminal**, en
este orden y una sola vez:

```
git branch -M main
git remote add origin https://github.com/rosaliahdez/notas-geometria-analitica.git
git push -u origin main
```

Si el push lo rechaza diciendo *fetch first* o *unrelated histories*, es porque
el repositorio de GitHub se creó con README. Entonces:

```
git pull origin main --rebase --allow-unrelated-histories
git push -u origin main
```

De aquí en adelante, el botón **Push** de la pestaña Git ya funciona solo.

**e. Primera publicación del sitio.** En la Terminal:

```
quarto publish gh-pages
```

Pregunta si se usa el repositorio detectado y se contesta que sí. Crea la rama
`gh-pages`, renderiza el libro y sube el resultado.

**f. Comprobar GitHub Pages.** En el navegador, dentro del repositorio,
`Settings → Pages`. La fuente debe ser **Deploy from a branch**, rama
**gh-pages**, carpeta **/ (root)**. Y en `Settings → General`, que el
repositorio sea **Public**: con cuenta gratuita, Pages sólo publica repositorios
públicos.

---

### Cada actualización

Son dos pasos, y el orden importa.

#### Paso 1. Guardar los cambios en el repositorio

En RStudio, con el proyecto abierto:

1. Pestaña **Git**, arriba a la derecha.
2. Palomear los archivos modificados en la columna **Staged**.
3. **Commit**, escribir un mensaje corto que diga qué se cambió, y **Commit**.
4. Botón **Push** (la flecha verde hacia arriba).

Si no se hace *push*, los cambios quedan solo en la computadora.

**El mensaje del commit.** La fórmula es la misma de siempre: qué cambió, en una
línea, sin punto final. Git ya guarda el orden y la fecha, así que numerar
versiones sobra. Por ejemplo, *Capítulo 1: distancia en el espacio en tres
pasos*, o *Figuras de octantes y planos coordenados*.

#### Paso 2. Publicar el sitio

En la pestaña **Terminal** de RStudio, no en la consola de R:

```
quarto publish gh-pages
```

El sitio tarda uno o dos minutos en reflejar el cambio. Si al abrirlo se ve
igual, conviene recargar con Ctrl + F5, que ignora la copia guardada en el
navegador.

#### Comprobación rápida antes de publicar

Conviene renderizar en local primero, para no publicar algo roto. Pestaña
**Build → Render Book**, o en la Terminal:

```
quarto render
```

Si eso pasa sin errores, `quarto publish gh-pages` también va a pasar.

---

### Si el push falla por autenticación

El mensaje es este:

```
remote: Invalid username or token. Password authentication is not supported
fatal: Authentication failed for 'https://github.com/...'
```

No es que la contraseña esté mal. GitHub dejó de aceptar contraseñas para Git en
2021, y en su lugar se usa un **token** que caduca. Desde la consola de R:

```r
usethis::create_github_token()
```

Abre el navegador con los permisos correctos ya marcados. Se le pone nombre, se
elige la caducidad y **Generate token**. Aparece una clave larga que empieza con
`ghp_` y hay que copiarla en ese momento, porque GitHub no la vuelve a mostrar.
Enseguida, de vuelta en la consola:

```r
gitcreds::gitcreds_set()
```

Se pega el token ahí. Si ya había uno guardado, ofrece reemplazarlo, que es justo
lo que se necesita cuando el viejo caducó. Después de eso el **Push** de RStudio
funciona normal.

---

### Errores que salen seguido

| Lo que dice | Qué pasó |
|---|---|
| `src refspec main does not match any` | La rama local se llama `master`. `git branch -M main` y volver a empujar |
| `'origin' does not appear to be a git repository` | Falta conectar el remoto. `git remote add origin <url del repositorio>` |
| `Unable to publish, uncommitted changes` | Falta el paso 1. Quarto exige el repositorio limpio |
| `Authentication failed` al hacer push | El token caducó. Ver la sección de arriba |
| `quarto: command not found` | Se escribió en la consola de R y no en la Terminal |
| `fetch first` o `unrelated histories` | El repositorio de GitHub tiene algo que no está aquí. `git pull origin main --rebase` y volver a empujar |
| El sitio se ve viejo | Todavía no termina de propagarse, o es la copia del navegador |
| 404 al abrir la dirección | `Settings → Pages`, que la rama sea `gh-pages`; y que el repositorio sea público |
| Falla al renderizar un capítulo | Sintaxis de un `.qmd`. El mensaje dice cuál y en qué renglón |

---

### Sobre el PDF descargable

Está desactivado a propósito. Como `quarto publish gh-pages` renderiza en esta
máquina y no en GitHub, el PDF exige tener LaTeX instalado aquí. Para activarlo
algún día:

```
quarto install tinytex
```

y descomentar en `_quarto.yml` la línea `downloads: [pdf]` y el bloque `pdf:`.

---

## 2. Subir una presentación a Overleaf

Cada archivo de semana es **autocontenido**, trae su propio preámbulo y no llama
a ningún otro archivo. Se abre, se copia entero y se pega en un archivo nuevo
dentro de la carpeta del curso en Overleaf. Se compila con **pdfLaTeX**.

No hay figuras externas que subir: todas las figuras están dibujadas en TikZ
dentro del mismo `.tex`.

---

## 3. Importar preguntas a Moodle

El procedimiento de cada semana está en `Moodle/configuracion-cuestionario-semana-XX.md`,
dentro de la carpeta del curso en Dropbox, con los ajustes que hay que marcar
para que la retroalimentación se vea.

Lo esencial: los `.xml` son **paquetes de preguntas**, no cuestionarios. Se
importan en *Banco de preguntas → Importar*, con **Formato XML de Moodle** y con
la casilla **Obtener categoría desde el archivo** marcada. El cuestionario es una
actividad aparte que se arma después.

Importar dos veces el mismo archivo **duplica** las preguntas.

---

## 4. Regenerar las figuras del libro

Las figuras del capítulo 1 están hechas en TikZ, en `imagenes/figuras-cap01.tex`,
con una figura por página. Si alguna se edita, se vuelven a generar desde la
pestaña **Terminal**:

```
cd imagenes
pdflatex figuras-cap01.tex
pdftocairo -svg -f 1 -l 1 figuras-cap01.pdf plano-distancia-origen.svg
```

y así con cada página, en el orden en que aparecen en el archivo. El PDF
intermedio no se versiona.
