# Bitácora del Laboratorio de Rescate

**Nombre:** Sergio Andrés Losada Lavao  
**Programa:** Análisis y Desarrollo de Software – SENA  
**Sesión:** 2 · Laboratorio de Rescate  
**Instructora:** Karol Correa  
**Fecha:** 25 de noviembre de 2026  
**Repositorio:** https://github.com/sergioandreslosadalavao-oss/laboratorio-rescate-sergio-losada

---

## Preparación — Reto 0

Creé el repositorio público en GitHub con el nombre `laboratorio-rescate-sergio-losada`. Lo cloné en mi equipo y configuré el archivo inicial `notas.txt` con tres líneas y el archivo `BITACORA.md`. Luego subí todo con el primer commit:

```bash
git clone https://github.com/sergioandreslosadalavao-oss/laboratorio-rescate-sergio-losada.git
cd laboratorio-rescate-sergio-losada
git add .
git commit -m "feat: agrega notas y bitácora"
git push origin main
```

---

## Reto 1 — Ver el historial

**Situación:** Necesitaba saber qué cambios se habían hecho en el proyecto y quién los hizo.

```bash
git log
git log --oneline
git log --oneline --graph --all
```

**¿Qué muestra cada comando?**

- `git log` → Muestra el historial completo de commits con toda la información: el hash largo del commit, el autor, la fecha y el mensaje completo. Es muy detallado.
- `git log --oneline` → Muestra el historial resumido: solo el hash corto (7 caracteres) y el mensaje del commit en una sola línea. Es mucho más fácil de leer de un vistazo.
- `git log --oneline --graph --all` → Muestra lo mismo que el anterior pero agrega un gráfico en ASCII a la izquierda que representa visualmente las ramas y cómo se unen. También muestra todos los commits de todas las ramas, no solo la rama actual.

**¿Qué es el código al inicio de cada commit?**

Es el **hash SHA-1** del commit: un identificador único generado automáticamente por Git que permite referenciar ese momento exacto del proyecto en cualquier momento.

---

## Reto 2 — Ver qué cambió antes de guardar

**Situación:** Modifiqué `notas.txt` (cambié la Línea 2) y quería revisar exactamente qué había cambiado antes de hacer commit.

```bash
git diff
git add notas.txt
git diff
git diff --staged
git commit -m "docs: modifica línea 2 de notas"
```

**¿Por qué el segundo `git diff` no mostró nada?**

Porque `git diff` sin argumentos solo muestra los cambios que están en el directorio de trabajo pero **aún no han sido agregados** al área de preparación (staging). Una vez que ejecuté `git add notas.txt`, el archivo pasó al área de staging y `git diff` ya no lo detecta.

**¿Qué muestra `--staged`?**

`git diff --staged` muestra exactamente los cambios que ya fueron agregados con `git add` y están listos para el próximo commit, es decir, compara el staging area contra el último commit guardado.

---

## Reto 3 — "Borré un archivo por error"

**Situación:** Eliminé `notas.txt` por error antes de hacer commit.

```bash
rm notas.txt
git status
git restore notas.txt
git status
```

**¿Qué hace `git restore`?**

`git restore` recupera el archivo desde el último commit registrado. Es como pedirle a Git que "restaure" el archivo al estado en el que estaba la última vez que fue guardado en el historial.

**¿Funcionaría si nunca se hubiera hecho commit de ese archivo?**

No. Si el archivo nunca fue rastreado por Git (nunca tuvo commit), Git no tiene ninguna versión guardada de él y no puede restaurarlo. Solo funciona con archivos que ya tienen historial en el repositorio.

---

## Reto 4 — "Agregué un archivo que no debía subir"

**Situación:** Agregué por error con `git add` un archivo `secreto.env` que contiene claves de base de datos.

```bash
git add .
git status
git restore --staged secreto.env
git status
git add .gitignore
git commit -m "chore: agrega .gitignore para archivos .env"
git push
```

**¿Qué diferencia hay entre `git restore` y `git restore --staged`?**

- `git restore archivo` → Descarta los cambios del **directorio de trabajo** y recupera el archivo desde el último commit. Los cambios se pierden.
- `git restore --staged archivo` → Saca el archivo del **área de staging** (lo "desagrega"), pero los cambios en el directorio de trabajo se conservan intactos. El archivo sigue modificado/presente, simplemente ya no está marcado para el próximo commit.

Creé el `.gitignore` con `*.env` para que Git ignore automáticamente cualquier archivo con esa extensión en el futuro.

---

## Reto 5 — "Escribí mal el mensaje del commit"

**Situación:** Hice un commit con el mensaje genérico `"cambios"` que no describe nada útil.

```bash
git add notas.txt
git commit -m "cambios"
git log --oneline
git commit --amend -m "docs: agrega línea 4 a notas"
git log --oneline
git push
```

`git commit --amend` reemplaza el último commit con uno nuevo que tiene el mensaje corregido. Es importante usarlo **antes** de hacer push, porque si ya se subió el commit al repositorio remoto y otros lo tienen, cambiar el historial causaría conflictos.

---

## Reto 6 — "Subí un commit que dañó el proyecto"

**Situación:** Borré todas las líneas de `notas.txt`, hice commit y push. El commit ya estaba en GitHub.

```bash
git add notas.txt
git commit -m "fix: limpia notas"
git push
git revert HEAD
git log --oneline
git push
```

**¿`git revert` borró el commit equivocado o creó uno nuevo?**

Creó un **commit nuevo** que hace exactamente lo opuesto al commit anterior: si el commit dañino borró líneas, el revert las agrega de vuelta. El historial conserva ambos commits.

**¿Por qué es la opción segura cuando ya se hizo push?**

Porque no reescribe el historial. Si otras personas ya descargaron el commit equivocado, hacer `git reset` en remoto generaría conflictos graves. `git revert` agrega un commit nuevo que corrige el error sin tocar el historial existente, por lo que todos pueden sincronizar sin problemas con `git pull`.

---

## Reto 7 — "Hice un commit antes de tiempo"

**Situación:** Hice un commit con `borrador.txt` sin que estuviera listo y sin haberlo subido.

```bash
git add borrador.txt
git commit -m "feat: agrega borrador"
git log --oneline
git reset --soft HEAD~1
git log --oneline
git status
git add borrador.txt
git commit -m "feat: agrega borrador completo"
git push
```

**¿Qué diferencia hay entre `git reset --soft` y `git revert`?**

| | `git reset --soft HEAD~1` | `git revert HEAD` |
|---|---|---|
| **¿Qué hace?** | Deshace el último commit local, conservando los cambios en staging | Crea un nuevo commit que invierte los cambios del commit señalado |
| **¿Reescribe el historial?** | Sí | No |
| **¿Cuándo se usa?** | Cuando el commit **no se ha subido** (push) aún | Cuando el commit **ya está en el remoto** y otros lo pueden tener |

**En resumen:** `reset --soft` es para arreglar errores locales antes de subir. `revert` es para corregir errores que ya están publicados.

---

## Reto 8 — "Tengo trabajo a medias y debo cambiar de tarea"

**Situación:** Estaba editando `notas.txt` (agregando Línea 5) cuando llegó una "urgencia" que me pedía cambiar de rama sin hacer commit de algo incompleto.

```bash
git stash
git status
git stash list
git switch -c fix/urgente
git switch main
git stash pop
git status
git add notas.txt
git commit -m "docs: agrega línea 5 a notas"
git push
```

**¿Para qué sirve `git stash`?**

`git stash` guarda temporalmente todos los cambios no confirmados (tanto del directorio de trabajo como del staging area) en una "pila" especial, dejando el directorio de trabajo limpio. Es como poner el trabajo en un cajón para poder cambiar de tarea sin perder nada ni hacer un commit a medias.

**¿Qué hace `git stash pop`?**

Recupera los cambios guardados más recientemente en el stash y los aplica de vuelta al directorio de trabajo, además de eliminarlos de la pila del stash. Es la operación inversa: "abres el cajón, sacas lo que guardaste y el cajón queda vacío".

---

## Cierre — Historial final del repositorio

```
* 8f6a968 (HEAD -> main, origin/main) docs: agrega línea 5 a notas
* a9e0701 (fix/urgente) feat: agrega borrador completo
* fad83b5 Revert "fix: limpia notas"
* 379da5c fix: limpia notas
* 7f1d5e6 docs: agrega línea 4 a notas
* 5664ff3 chore: agrega .gitignore para archivos .env
* d61231d docs: modifica línea 2 de notas
* 05693ad feat: agrega notas y bitácora
```

---

*Bitácora redactada con mis propias palabras como parte de la evaluación del Laboratorio de Rescate – Sesión 2.*
