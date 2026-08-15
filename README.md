# Notas de Geometría Analítica (6886)

Libro digital del curso de Geometría Analítica, Licenciatura en Matemáticas, Universidad de Sonora. Hecho con [Quarto](https://quarto.org) (tipo de proyecto: `book`).

## Trabajar en RStudio

1. Abre `notas-geometria-analitica.Rproj` en RStudio.
2. Edita los capítulos `.qmd`.
3. Pestaña **Build → Render Book** (`Ctrl+Shift+B`). El libro queda en `_book/`.
4. Para publicarlo: pestaña **Git** (marcar *Staged*, **Commit**, **Push**) y después `quarto publish gh-pages` en la **Terminal**. El paso a paso está en `COMO-ACTUALIZAR.md`.

No requiere R ni Python para renderizar, todo el contenido actual es markdown, LaTeX y HTML.

## Estructura

```
_quarto.yml                     configuración del libro (capítulos, tema, PDF)
index.qmd                       "Antes de empezar"
01-...qmd a 07-...qmd           un capítulo por unidad del programa
invernadero.qmd                 portada de la sección opcional de Python
invernadero/                    los cuadernos del invernadero
applets/                        figuras interactivas (HTML+JS autocontenido)
imagenes/                       figuras estáticas
referencias.bib                 bibliografía
estilos.scss                    tema visual (paleta del curso)
COMO-ACTUALIZAR.md              bitácora de los procedimientos que se repiten
diapositivas/                   fuentes Beamer de las presentaciones (Overleaf)
```

**Estado:** el capítulo 1 (Sistemas de coordenadas) está completo; los capítulos 2 a 7 tienen el esqueleto con el orden y el enfoque fijados, y se escriben durante el semestre.

## Publicar en GitHub Pages

El mismo flujo del libro de Estadística. La rama `main` guarda el código fuente y la rama `gh-pages` guarda el sitio, que Quarto genera y sube sola.

1. Pestaña **Git**: marcar *Staged*, **Commit**, **Push**.
2. Pestaña **Terminal**: `quarto publish gh-pages`.

El paso a paso, incluido el alta la primera vez, está en **`COMO-ACTUALIZAR.md`**.

## El invernadero (celdas ejecutables, pendiente)

Los cuadernos del invernadero muestran el código de Python pero todavía no lo ejecutan en el navegador. Para activarlo, cuando quieras:

```bash
quarto add r-wasm/quarto-live
```

y en `_quarto.yml` descomenta el filtro `live` (está señalado con un comentario). Después, en los archivos de `invernadero/` —y **sólo** en ellos— cambia los bloques ` ```python ` por ` ```{pyodide} `. La regla de la casa: ninguna página del cuerpo del libro carga Python; sólo el invernadero.

## Advertencia sobre Dropbox

Este repositorio debe vivir **fuera** de Dropbox (por eso está en `Documents`). Git guarda su estado en la carpeta `.git` y la sincronización de Dropbox puede corromperla. El respaldo del repositorio es GitHub: cada `push` deja una copia completa del historial.
