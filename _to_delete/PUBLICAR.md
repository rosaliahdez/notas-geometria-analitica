# Publicar el libro desde RStudio

**Geometría Analítica (6886) · Libro digital del curso**

Todo el trabajo se hace con el proyecto `notas-geometria-analitica.Rproj` abierto en RStudio. La parte A se hace una sola vez, cuando el repositorio todavía no existe en GitHub. La parte B es la rutina de cada actualización, y son cinco clics en la pestaña **Git**.

La dirección final del libro es

> `https://rosaliahdez.github.io/notas-geometria-analitica/`

que es la que aparece en las diapositivas y en Moodle.

---

## Dos comprobaciones previas

**1. Que RStudio vea a Git.** Menú `Tools → Global Options → Git/SVN`. El campo *Git executable* debe apuntar a `git.exe`, normalmente

```
C:/Program Files/Git/bin/git.exe
```

Si el campo está vacío, falta instalar **Git for Windows** ([git-scm.com](https://git-scm.com)). Se instala con las opciones que trae por omisión y después hay que cerrar y volver a abrir RStudio.

**2. Que esté el paquete `usethis`.** En la consola de RStudio:

```r
install.packages("usethis")
```

Es el paquete que hace desde la consola lo que de otro modo habría que hacer en la terminal.

---

## Parte A. El alta, una sola vez

### A.1 Abrir el proyecto

Doble clic en `notas-geometria-analitica.Rproj`. Todo lo que sigue ocurre con ese proyecto abierto.

### A.2 Limpiar la carpeta

En la pestaña **Files**, marcar la carpeta `_to_delete` y borrarla con el botón **Delete**. Contiene un zip y un PDF viejo que no tiene caso versionar.

### A.3 Decir quién eres (una vez en la vida)

En la consola, con el correo de tu cuenta de GitHub:

```r
usethis::use_git_config(
  user.name  = "Rosalia Hernandez",
  user.email = "rosalia.hdez@gmail.com"
)
```

Ese nombre y ese correo son los que quedan firmando cada envío.

### A.4 Convertir la carpeta en repositorio

```r
usethis::use_git()
```

Pregunta dos cosas y a las dos se responde que sí: si quiere hacer el primer commit con todo lo que hay, y si quiere reiniciar RStudio. Al volver, aparece la pestaña **Git** junto a Environment y History.

El mismo paso por menú, si lo prefieres: `Tools → Version Control → Project Setup → Version control system: Git`.

**Comprobación.** En la esquina derecha de la pestaña Git aparece el nombre de la rama. Debe decir **main**. Si dijera `master`, corregir con

```r
usethis::git_default_branch_rename()
```

porque el flujo de publicación está escrito para `main`.

### A.5 Guardar el token de GitHub (una vez)

GitHub ya no acepta contraseñas para subir código, sino un token. Se genera y se guarda desde la consola:

```r
usethis::create_github_token()
```

Se abre el navegador con los permisos ya marcados. Al final de la página, **Generate token**, y copiar la cadena que aparece (empieza con `ghp_`). Sólo se ve una vez.

De vuelta en RStudio:

```r
gitcreds::gitcreds_set()
```

Elegir la opción de reemplazar o añadir credenciales y pegar el token. Queda guardado en el administrador de credenciales de Windows, así que no hay que repetirlo.

### A.6 Crear el repositorio en GitHub y subir todo

Una sola línea:

```r
usethis::use_github(private = FALSE)
```

Esto crea el repositorio en tu cuenta con el nombre de la carpeta, lo conecta con la carpeta local, sube todo y abre el repositorio en el navegador.

Si algo no cuadra, el diagnóstico completo está en

```r
usethis::git_sitrep()
```

### A.7 Activar GitHub Pages

Este es el **único paso que no se puede hacer desde RStudio**, y se hace una sola vez. En el repositorio, dentro del navegador:

1. `Settings → Pages`.
2. En **Build and deployment**, Source: **GitHub Actions**.

No hay que elegir carpeta ni rama. El resto ya está escrito en `.github/workflows/publish.yml`.

### A.8 Esperar el primer flujo

Pestaña **Actions** del repositorio. El flujo *Publicar libro* tarda entre tres y cinco minutos la primera vez, porque instala Quarto y TinyTeX. Cuando queda en verde, el libro está publicado.

---

## Parte B. Cada actualización

Con el proyecto abierto, esta es toda la rutina.

1. **Editar** los archivos `.qmd` y guardar con `Ctrl+S`.
2. **Ver el resultado**, que es opcional pero evita publicar un error. Pestaña **Build → Render Book**, o `Ctrl+Shift+B`. El libro se arma en `_book/` y se abre en el visor.
3. **Marcar los cambios.** Pestaña **Git**: aparece la lista de archivos modificados. Marcar la casilla de la columna **Staged** de los que quieras subir. El botón **Diff** muestra exactamente qué cambió en cada uno, renglón por renglón.
4. **Commit.** Botón **Commit**, escribir en el recuadro un resumen breve de lo que cambió, por ejemplo `Capítulo 1: distancia en el espacio`, y pulsar **Commit**.
5. **Push.** Botón **Push**, la flecha verde hacia arriba. Ahí es cuando el cambio llega a GitHub.

Después, en el navegador, la pestaña **Actions** del repositorio debe quedar en verde, y la página del libro se recarga con `Ctrl+F5`. Sin eso, el navegador muestra la versión guardada y parece que nada cambió.

Conviene publicar al cierre de cada unidad y no cada vez que se escribe un párrafo. El historial queda más legible.

### Los símbolos de la pestaña Git

| Símbolo | Significado |
|---|---|
| `?` amarillo | Archivo nuevo, que Git todavía no sigue |
| `M` azul | Archivo modificado |
| `A` verde | Archivo nuevo ya marcado para subir |
| `D` rojo | Archivo borrado |

Marcar la casilla **Staged** es lo que decide qué entra en el envío. Lo que no se marca se queda en tu máquina.

---

## Antes de enviar, tres comprobaciones

1. Que **Render Book** termine sin errores.
2. Que las figuras nuevas estén en `imagenes/` y se citen con ruta relativa, por ejemplo `![](imagenes/espacio-octantes.svg)`.
3. Que un capítulo nuevo esté registrado en `_quarto.yml`, en la lista `chapters`. Un archivo que no aparece ahí no se publica, aunque exista.

---

## Si algo sale mal

| Síntoma | Qué hacer |
|---|---|
| No aparece la pestaña Git | `Tools → Global Options → Git/SVN` (que esté la ruta de `git.exe`) y `Tools → Project Options → Git/SVN` (que el sistema sea Git) |
| Al hacer Push dice *Authentication failed* | El token venció o no se guardó. Repetir `usethis::create_github_token()` y `gitcreds::gitcreds_set()` |
| Push rechazado, *fetch first* | Hubo un cambio hecho desde la web. Pulsar **Pull** (flecha azul hacia abajo) y luego **Push** |
| El flujo de GitHub falla en *Renderizar el libro* | Abrir el registro de ese paso en Actions. Casi siempre es sintaxis de un `.qmd`, una llave sin cerrar o una referencia `@sec-` que no existe |
| La página no cambia | Recargar con `Ctrl+F5` y comprobar que el último flujo esté en verde |
| Aparece `_book/` en la pestaña Git | No debería, está en `.gitignore`. Si ya se subió alguna vez, en la pestaña **Terminal**: `git rm -r --cached _book` y volver a hacer commit |
| El PDF descargable no aparece en el sitio | Es el paso de TinyTeX del flujo. La web puede publicarse aunque el PDF falle |

---

## Lo que se hace en el navegador

Sólo dos cosas, y la primera nada más una vez:

- Activar Pages (`Settings → Pages`, Source: GitHub Actions).
- Mirar la pestaña **Actions** para saber si la publicación terminó bien.

---

## Mapa del repositorio

| Archivo o carpeta | Para qué sirve |
|---|---|
| `index.qmd` | Portada del libro, antes del capítulo 1 |
| `01-...qmd` a `07-...qmd` | Los siete capítulos, uno por unidad |
| `_quarto.yml` | Estructura del libro, formato, tema y lista de capítulos |
| `estilos.scss` | Paleta y tipografía del sitio |
| `referencias.bib` | Bibliografía |
| `imagenes/` | Figuras del libro, en SVG, con su fuente TikZ |
| `applets/` | Figuras interactivas en HTML |
| `diapositivas/` | Las presentaciones en LaTeX (los PDF no se versionan) |
| `invernadero/` | Sección opcional de Python |
| `.github/workflows/publish.yml` | El flujo que renderiza y publica |
| `_book/`, `.quarto/` | Resultados locales. No se suben |

---

## Apéndice. Regenerar las figuras del capítulo 1

Las figuras están hechas en TikZ, en `imagenes/figuras-cap01.tex`, con una figura por página. Si alguna se edita, se vuelven a generar desde la pestaña **Terminal** de RStudio:

```bash
cd imagenes
pdflatex figuras-cap01.tex
pdftocairo -svg -f 1 -l 1 figuras-cap01.pdf plano-distancia-origen.svg
```

y así con cada página, en el orden en que aparecen en el archivo. El PDF intermedio no se versiona.
