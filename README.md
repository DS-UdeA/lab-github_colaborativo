![Built with AI](https://img.shields.io/badge/Built%20with-AI-blue.svg)

# Laboratorio 4 — Trabajo en Equipo con GitHub: Lista Enlazada Colaborativa

> **Modalidad:** Equipos de 2–3 personas (obligatorio, no individual)
> **Duración sugerida:** 2.5–3 horas (presencial)
> **Entregables:** PR mergeado, historial de commits por integrante, evidencia de code review
> **Prerrequisito:** Laboratorio 1 aprobado (flujo `clone → add → commit → push`)

**Propósito:** Practicar el flujo de trabajo colaborativo real de GitHub — ramas, pull requests, code review y resolución de merge conflicts — aplicado a la implementación colectiva de una lista enlazada simple en Python. Este flujo es el que usarán en CodeF@ctory y en la industria.

> [!important]
> En este laboratorio **no se puede trabajar en `main` directamente**. Todo cambio debe llegar a través de un Pull Request revisado por otro integrante del equipo.

---

## 1. Objetivos

- Aplicar el flujo `branch → commit → push → PR → review → merge` en un repositorio compartido real.
- Experimentar y resolver merge conflicts generados por trabajo paralelo entre compañeros.
- Practicar pair programming con roles de **driver** y **navigator**.
- Realizar code review: comentar, solicitar cambios y aprobar el trabajo del compañero.
- Integrar las operaciones de una lista enlazada desarrolladas en paralelo por distintos equipos.

---

## 2. Conceptos clave

### 2.1 Flujo GitHub Flow (el que usaremos)

```
main (protegida — nadie escribe directo)
  │
  ├── feature/append   ← Equipo A trabaja aquí
  ├── feature/delete   ← Equipo B trabaja aquí
  └── feature/search   ← Equipo C trabaja aquí
```

Cada equipo trabaja en su rama. Cuando termina, abre un **Pull Request** hacia `main`. Los demás equipos revisan el código antes de que se mergee.

### 2.2 ¿Por qué ocurren los merge conflicts?

Cuando dos ramas modifican **la misma línea del mismo archivo**, Git no sabe cuál versión es la correcta y pide que un humano decida. En este laboratorio **están diseñados para ocurrir** en el archivo de pruebas, exactamente como pasa en proyectos reales.

```
<<<<<<< feature/append
    assert ll.head.data == 10   # lo que escribió el Equipo A
=======
    assert ll.search(10) == True  # lo que escribió el Equipo B
>>>>>>> feature/delete
```

### 2.3 Code Review

Antes de que un PR se mergee, al menos **un compañero diferente al autor** debe:
- Leer el código línea por línea en la pestaña *Files changed* del PR.
- Dejar al menos un comentario constructivo (puede ser una pregunta, una sugerencia o una aprobación justificada).
- Aprobar o solicitar cambios usando el botón *Review changes*.

---

## 3. Preparación del Docente

> [!note]
> Esta sección es **solo para el docente**. Realícela antes del laboratorio.

### 3.1 Crear la organización y el repositorio base

1. Crear una organización en GitHub (p. ej. `estructuras-datos-udea-2026-1`).
2. Crear un repositorio llamado `lab2-lista-enlazada` dentro de la organización con:
   - Visibilidad: **Private** (o Public según su preferencia).
   - Inicializado con `README.md` y `.gitignore` Python.
3. Clonar el repo localmente y crear la siguiente estructura:

```
lab2-lista-enlazada/
  src/
    __init__.py          ← vacío
    linked_list.py       ← código base (ver abajo)
  tests/
    __init__.py          ← vacío
    test_linked_list.py  ← pruebas iniciales del docente (ver abajo)
  pyproject.toml
  requirements.txt
  README.md
```

### 3.2 Archivo `src/linked_list.py` (código base)

```python
# src/linked_list.py
# Estructura base — cada equipo implementa su operación asignada.


class Node:
    """Nodo de la lista enlazada."""

    def __init__(self, data):
        self.data = data
        self.next = None

    def __repr__(self):
        return f"Node({self.data})"


class LinkedList:
    """Lista enlazada simple."""

    def __init__(self):
        self.head = None

    # ------------------------------------------------------------------ #
    # Implementado por el docente — NO modificar                          #
    # ------------------------------------------------------------------ #
    def __str__(self):
        """Retorna una representación legible de la lista."""
        elements = []
        current = self.head
        while current:
            elements.append(str(current.data))
            current = current.next
        return " -> ".join(elements) if elements else "Lista vacía"

    def __len__(self):
        """Retorna el número de nodos."""
        count = 0
        current = self.head
        while current:
            count += 1
            current = current.next
        return count

    # ------------------------------------------------------------------ #
    # TODO — Equipo A: rama feature/append                                #
    # ------------------------------------------------------------------ #
    def append(self, data):
        """Inserta un nuevo nodo al final de la lista.

        Args:
            data: El valor a insertar.
        """
        raise NotImplementedError("Equipo A debe implementar append()")

    # ------------------------------------------------------------------ #
    # TODO — Equipo B: rama feature/delete                                #
    # ------------------------------------------------------------------ #
    def delete(self, data):
        """Elimina el primer nodo cuyo valor sea igual a data.

        Args:
            data: El valor a eliminar.

        Returns:
            True si el nodo fue eliminado, False si no se encontró.
        """
        raise NotImplementedError("Equipo B debe implementar delete()")

    # ------------------------------------------------------------------ #
    # TODO — Equipo C: rama feature/search                                #
    # ------------------------------------------------------------------ #
    def search(self, data):
        """Busca un valor en la lista.

        Args:
            data: El valor a buscar.

        Returns:
            El nodo que contiene data, o None si no existe.
        """
        raise NotImplementedError("Equipo C debe implementar search()")
```

### 3.3 Archivo `tests/test_linked_list.py` (pruebas base del docente)

```python
# tests/test_linked_list.py
# Pruebas base escritas por el docente.
# CADA EQUIPO agregará sus propias pruebas en este archivo
# desde su rama — esto generará merge conflicts intencionales.

import pytest
from src.linked_list import LinkedList, Node


# ------------------------------------------------------------------ #
# Pruebas del docente — __str__ y __len__                             #
# ------------------------------------------------------------------ #

def test_lista_vacia_str():
    ll = LinkedList()
    assert str(ll) == "Lista vacía"


def test_lista_vacia_len():
    ll = LinkedList()
    assert len(ll) == 0


def test_node_repr():
    n = Node(42)
    assert repr(n) == "Node(42)"
```

### 3.4 Archivo `pyproject.toml`

```toml
[tool.pytest.ini_options]
pythonpath = ["."]
testpaths = ["tests"]
```

### 3.5 Commit inicial y protección de `main`

```bash
git add .
git commit -m "feat: estructura base LinkedList con TODOs por equipo"
git push origin main
```

Luego, en GitHub → Settings → Branches → Add rule:
- Branch name pattern: `main`
- ✅ **Require a pull request before merging**
- ✅ **Require approvals** (mínimo 1)
- ✅ **Do not allow bypassing the above settings**

### 3.6 Añadir estudiantes al repositorio

En Settings → Collaborators → Add people: agregar a todos los estudiantes del grupo con rol **Write**.

Compartir el link del repositorio con los estudiantes antes del laboratorio.

---

## 4. Ejercicio Colaborativo

### Fase 0 — Organización del equipo (10 min)

**Formen grupos de 3** (o 2 si el número no da). Dentro del grupo:

1. Elijan quién será el **primer driver** (el que escribe código en la primera fase).
2. Asignen una operación a cada integrante:

| Integrante | Operación | Rama |
|---|---|---|
| Estudiante 1 | `append` | `feature/append` |
| Estudiante 2 | `delete` | `feature/delete` |
| Estudiante 3 | `search` | `feature/search` |

> [!tip]
> En grupos de 2: el Estudiante 1 implementa `append` y el Estudiante 2 implementa `delete`. La función `search` queda como reto opcional.

Llamen al docente o monitor para que les confirme la asignación antes de continuar.

---

### Fase 1 — Clonar y crear la rama (15 min)

**Cada integrante en su propio computador:**

#### 1.1 Clonar el repositorio

```bash
git clone <url-del-repositorio-del-docente>
cd lab2-lista-enlazada
```

#### 1.2 Crear y publicar la rama asignada

Reemplace `<operacion>` con `append`, `delete` o `search` según corresponda:

```bash
git checkout -b feature/<operacion>
git push --set-upstream origin feature/<operacion>
```

Verifique en GitHub que su rama aparece en el dropdown de ramas del repositorio.

#### 1.3 Crear y activar el entorno virtual

```bash
python -m venv .venv
# Linux/macOS:
source .venv/bin/activate
# Windows PowerShell:
.venv\Scripts\Activate.ps1

pip install pytest
pip freeze > requirements.txt
```

> ✅ **Checkpoint 1**
> - `git branch` muestra su rama con un `*` al lado.
> - `pytest -q` corre y muestra `3 passed` (las pruebas base del docente).

---

### Fase 2 — Implementación con Pair Programming (40 min)

> [!note]
> **Regla del pair programming:** Solo el driver escribe código. El navigator guía, revisa y sugiere — no puede tomar el teclado. Cambien roles a mitad de la fase.

#### 2.1 Implemente su operación asignada

Abra `src/linked_list.py` y reemplace el `raise NotImplementedError(...)` de su operación con la implementación real.

**Guía para cada equipo:**

---

**Equipo A — `append(data)`**

Considere dos casos:
- Lista vacía: el nuevo nodo se convierte en `head`.
- Lista no vacía: recorra hasta el último nodo y enlace el nuevo al final.

```python
# Esqueleto de referencia (no es la solución completa)
def append(self, data):
    new_node = Node(data)
    if self.head is None:
        # caso lista vacía
        ...
    else:
        # recorrer hasta el último nodo
        current = self.head
        while current.next is not None:
            current = current.next
        # enlazar
        ...
```

---

**Equipo B — `delete(data)`**

Considere tres casos:
- Lista vacía: retornar `False`.
- El nodo a eliminar es `head`: actualizar `head`.
- El nodo está en el medio o al final: ajustar el `next` del nodo anterior.

```python
# Esqueleto de referencia
def delete(self, data):
    if self.head is None:
        return False
    if self.head.data == data:
        # eliminar head
        ...
        return True
    current = self.head
    while current.next is not None:
        if current.next.data == data:
            # saltar el nodo siguiente
            ...
            return True
        current = current.next
    return False
```

---

**Equipo C — `search(data)`**

Recorra la lista comparando `current.data` con `data`. Retorne el nodo si lo encuentra, `None` si no.

```python
# Esqueleto de referencia
def search(self, data):
    current = self.head
    while current is not None:
        if current.data == data:
            return current
        current = current.next
    return None
```

---

#### 2.2 Prueba manual rápida

Desde la terminal (con el entorno activo), cree un archivo temporal para verificar:

```python
# prueba_rapida.py  ← NO subir, es solo para verificar localmente
from src.linked_list import LinkedList

ll = LinkedList()
ll.append(1)
ll.append(2)
ll.append(3)
print(ll)       # Esperado: 1 -> 2 -> 3
print(len(ll))  # Esperado: 3
```

Ejecute: `python prueba_rapida.py`

#### 2.3 Commit de la implementación

```bash
git add src/linked_list.py
git commit -m "feat: implementa <operacion>() en LinkedList"
git push origin feature/<operacion>
```

> ✅ **Checkpoint 2**
> - `git log --oneline` muestra su commit de implementación.
> - En GitHub, la rama `feature/<operacion>` contiene los cambios.

---

### Fase 3 — Pruebas Unitarias (25 min)

**Cambie el driver**: el segundo integrante (o el navigator) toma el teclado.

Abra `tests/test_linked_list.py` y agregue **al menos 3 pruebas** para su operación al final del archivo.

**Ejemplos de pruebas por equipo:**

---

**Equipo A — pruebas de `append`**

```python
# ------------------------------------------------------------------ #
# Pruebas Equipo A — append                                           #
# ------------------------------------------------------------------ #

def test_append_un_elemento():
    ll = LinkedList()
    ll.append(10)
    assert ll.head is not None
    assert ll.head.data == 10
    assert len(ll) == 1


def test_append_varios_elementos():
    ll = LinkedList()
    ll.append(1)
    ll.append(2)
    ll.append(3)
    assert str(ll) == "1 -> 2 -> 3"
    assert len(ll) == 3


def test_append_orden_preservado():
    ll = LinkedList()
    for v in [5, 10, 15]:
        ll.append(v)
    current = ll.head
    for expected in [5, 10, 15]:
        assert current.data == expected
        current = current.next
```

---

**Equipo B — pruebas de `delete`**

```python
# ------------------------------------------------------------------ #
# Pruebas Equipo B — delete                                           #
# ------------------------------------------------------------------ #

def test_delete_elemento_existente():
    ll = LinkedList()
    ll.append(1)
    ll.append(2)
    ll.append(3)
    resultado = ll.delete(2)
    assert resultado is True
    assert str(ll) == "1 -> 3"


def test_delete_head():
    ll = LinkedList()
    ll.append(10)
    ll.append(20)
    ll.delete(10)
    assert ll.head.data == 20


def test_delete_elemento_inexistente():
    ll = LinkedList()
    ll.append(5)
    resultado = ll.delete(99)
    assert resultado is False
    assert len(ll) == 1


def test_delete_lista_vacia():
    ll = LinkedList()
    assert ll.delete(1) is False
```

---

**Equipo C — pruebas de `search`**

```python
# ------------------------------------------------------------------ #
# Pruebas Equipo C — search                                           #
# ------------------------------------------------------------------ #

def test_search_elemento_existente():
    ll = LinkedList()
    ll.append(10)
    ll.append(20)
    nodo = ll.search(10)
    assert nodo is not None
    assert nodo.data == 10


def test_search_elemento_inexistente():
    ll = LinkedList()
    ll.append(5)
    assert ll.search(99) is None


def test_search_lista_vacia():
    ll = LinkedList()
    assert ll.search(1) is None


def test_search_ultimo_elemento():
    ll = LinkedList()
    for v in [1, 2, 3]:
        ll.append(v)
    nodo = ll.search(3)
    assert nodo is not None
    assert nodo.data == 3
```

---

#### 3.1 Ejecutar todas las pruebas

```bash
pytest -v
```

> [!warning]
> Es normal que pruebas de otras operaciones fallen (`NotImplementedError`) porque sus compañeros aún no han mergeado su código. Enfóquese en que **sus propias pruebas pasen**.

#### 3.2 Commit de las pruebas

```bash
git add tests/test_linked_list.py
git commit -m "test: pruebas unitarias para <operacion>()"
git push origin feature/<operacion>
```

> ✅ **Checkpoint 3**
> - `pytest -v` muestra las pruebas de su operación en verde.
> - El archivo `tests/test_linked_list.py` en su rama contiene sus pruebas.

---

### Fase 4 — Pull Request y Code Review (30 min)

#### 4.1 Abrir el Pull Request

1. Vaya al repositorio en GitHub.
2. Clic en **Compare & pull request** (aparece automáticamente al detectar su rama).
3. Complete:
   - **Title:** `feat: implementa <operacion>() con pruebas`
   - **Description** (obligatoria): Use esta plantilla:

```markdown
## ¿Qué hace este PR?
Implementa la operación `<operacion>()` de la clase `LinkedList`.

## Casos cubiertos
- [ ] Lista vacía
- [ ] Un elemento
- [ ] Múltiples elementos
- [ ] Elemento no encontrado (si aplica)

## Pruebas
- Número de pruebas agregadas: __
- Comando para verificar: `pytest -v tests/test_linked_list.py`

## Notas para el reviewer
(Explique cualquier decisión de diseño no obvia)
```

4. En **Reviewers**, asigne a **un compañero de otro equipo**.
5. Clic en **Create pull request**.

#### 4.2 Revisar el PR de su compañero

Mientras su compañero revisa el suyo, usted revisa el de otro. Vaya a la pestaña **Pull requests** y abra el PR que le fue asignado.

**Checklist del reviewer** (deje un comentario por cada ítem):

- [ ] ¿El código implementa correctamente la operación según el docstring?
- [ ] ¿Los casos borde están contemplados (lista vacía, un elemento)?
- [ ] ¿Los nombres de variables son descriptivos?
- [ ] ¿Las pruebas cubren al menos 3 escenarios distintos?
- [ ] ¿Hay algo que no entiende y quisiera que el autor explicara?

Para comentar en una línea específica: en la pestaña *Files changed*, pase el cursor sobre el número de línea y haga clic en el `+` que aparece.

> [!tip]
> Un buen comentario de review no es solo "bien hecho" ni "está mal". Es específico: *"En la línea 34, si la lista tiene un solo elemento y se elimina, ¿qué queda en `self.head`? ¿Está eso probado?"*

#### 4.3 Responder al review

Si su reviewer solicitó cambios:
1. Haga los ajustes en su rama local.
2. Haga commit y push: los cambios aparecen automáticamente en el PR.
3. Responda al comentario con una explicación de lo que cambió.

> ✅ **Checkpoint 4**
> - Su PR tiene al menos **1 comentario de review** de un compañero.
> - Su PR tiene estado **Approved** (o usted respondió a los cambios solicitados).

---

### Fase 5 — Merge Conflicts (30 min)

> [!important]
> **No mergee su PR todavía.** Espere la instrucción del docente para esta fase.

#### 5.1 ¿Por qué habrá conflictos?

Todos los equipos editaron `tests/test_linked_list.py` en sus ramas. El primero que mergee no tendrá problema. Los siguientes verán esto en su PR:

![Imagen conceptual de conflicto en PR](https://docs.github.com/assets/cb-78720/mcs/images/help/pull_requests/merge-conflict-badge.png)

#### 5.2 El docente mergea el primero (lo hace en clase)

El docente selecciona el PR que esté más listo y lo mergea a `main`. Ahora `main` tiene cambios que las ramas restantes no tienen.

#### 5.3 Resuelva el conflicto desde su terminal

Desde su rama local:

```bash
# 1. Asegúrese de estar en su rama
git checkout feature/<operacion>

# 2. Traiga los últimos cambios de main
git fetch origin
git merge origin/main
```

Git reportará algo similar a:

```
CONFLICT (content): Merge conflict in tests/test_linked_list.py
Automatic merge failed; fix conflicts and then commit the result.
```

#### 5.4 Entienda los marcadores de conflicto

Abra `tests/test_linked_list.py` en VS Code. Verá secciones como:

```python
<<<<<<< HEAD
def test_append_un_elemento():
    ll = LinkedList()
    ll.append(10)
    assert ll.head.data == 10
=======
def test_delete_elemento_existente():
    ll = LinkedList()
    ll.append(1)
    ll.append(2)
    resultado = ll.delete(2)
    assert resultado is True
>>>>>>> origin/main
```

**Interpretación:**
- Lo que está entre `<<<<<<< HEAD` y `=======` es lo que tiene **su rama**.
- Lo que está entre `=======` y `>>>>>>> origin/main` es lo que tiene **main**.
- En este caso, **ambos bloques son válidos** — son pruebas distintas que deben coexistir.

#### 5.5 Resuelva el conflicto

La resolución correcta es **conservar ambas secciones** y eliminar los marcadores:

```python
# RESULTADO CORRECTO — ambas pruebas conviven
def test_append_un_elemento():
    ll = LinkedList()
    ll.append(10)
    assert ll.head.data == 10


def test_delete_elemento_existente():
    ll = LinkedList()
    ll.append(1)
    ll.append(2)
    resultado = ll.delete(2)
    assert resultado is True
```

> [!warning]
> Elimine **todas** las líneas con `<<<<<<<`, `=======` y `>>>>>>>`. Son marcadores de Git, no código Python.

#### 5.6 Verifique y haga commit de la resolución

```bash
# Verifique que no quedan marcadores
grep -n "<<<<<<\|=======\|>>>>>>>" tests/test_linked_list.py
# Debe retornar vacío

# Ejecute las pruebas
pytest -v

# Marque el conflicto como resuelto y haga commit
git add tests/test_linked_list.py
git commit -m "fix: resuelve merge conflict en test_linked_list.py"
git push origin feature/<operacion>
```

El PR se actualiza automáticamente en GitHub y el conflicto queda marcado como resuelto.

> ✅ **Checkpoint 5**
> - `pytest -v` pasa sin errores de sintaxis relacionados con marcadores.
> - El PR ya no muestra el banner rojo de conflicto.

---

### Fase 6 — Integración Final (20 min)

Una vez que todos los PRs estén aprobados y los conflictos resueltos:

1. El docente (o un representante de cada equipo) mergea los PRs restantes en orden.
2. **Todos** hacen pull a `main`:

```bash
git checkout main
git pull origin main
pytest -v
```

> ✅ **Checkpoint 6 (resultado esperado)**
> - `pytest -v` corre todas las pruebas de todos los equipos.
> - La lista enlazada tiene `append`, `delete` y `search` funcionando juntos.
> - El historial de `git log --oneline --graph` muestra las tres ramas mergeadas.

#### 6.1 Prueba de integración manual

Con el entorno activo, ejecute en la terminal:

```python
# integración_final.py
from src.linked_list import LinkedList

ll = LinkedList()

# append
ll.append(10)
ll.append(20)
ll.append(30)
print("Después de append:", ll)          # 10 -> 20 -> 30

# search
nodo = ll.search(20)
print("Buscar 20:", nodo)                # Node(20)
print("Buscar 99:", ll.search(99))       # None

# delete
ll.delete(20)
print("Después de delete(20):", ll)      # 10 -> 30
print("Largo final:", len(ll))           # 2
```

```bash
python integración_final.py
```

---

## 5. Entrega

### ¿Qué se debe entregar?

En el Moodle (o plataforma indicada por el docente), **cada integrante** envía:

```
Nombre completo:
Usuario GitHub:
Rama trabajada:        feature/<operacion>
Link del repositorio:  https://github.com/<org>/lab2-lista-enlazada
Link a MI Pull Request: https://github.com/<org>/lab2-lista-enlazada/pull/<N>
Link al PR que revisé:  https://github.com/<org>/lab2-lista-enlazada/pull/<N>
```

### Criterios de verificación

El docente verificará por cada integrante:

| Criterio | Evidencia |
|---|---|
| Creó su rama con nombre correcto | Rama `feature/<operacion>` visible en GitHub |
| Implementó su operación | Código sin `NotImplementedError` en `linked_list.py` |
| Escribió al menos 3 pruebas | Pruebas en `test_linked_list.py` en su rama |
| Abrió un PR con descripción completa | PR visible con plantilla completada |
| Hizo code review al PR de un compañero | Comentario visible en el PR ajeno |
| Resolvió merge conflict | Commit de resolución en el historial |
| Su código está en `main` mergeado | Visible en la rama `main` del repo |

### Checklist final antes de enviar

- [ ] `pytest -v` desde `main` pasa todas las pruebas.
- [ ] Mi PR está mergeado (estado *Merged* en púrpura en GitHub).
- [ ] El PR que revisé tiene mi comentario visible.
- [ ] `git log --oneline` muestra al menos 3 commits míos: implementación, pruebas, resolución de conflicto.
- [ ] Copié correctamente los links solicitados.

---

## 6. Rúbrica de Evaluación

| Componente | Peso | Criterio |
|---|:---:|---|
| Implementación correcta de la operación asignada | 25% | Pruebas pasan, casos borde contemplados |
| Pruebas unitarias (cantidad y calidad) | 20% | ≥3 pruebas, cubren escenarios distintos |
| Uso correcto de ramas y commits | 20% | Mensajes descriptivos, nunca en `main` directo |
| Pull Request (descripción y proceso) | 15% | Plantilla completada, descripción clara |
| Code Review realizado | 10% | Al menos 1 comentario específico y constructivo |
| Resolución del merge conflict | 10% | Commit de resolución presente, pruebas pasan |

---

## 7. Comandos de Referencia Rápida

```bash
# Ver en qué rama estoy
git branch

# Crear y cambiar a nueva rama
git checkout -b feature/<nombre>

# Publicar rama en GitHub por primera vez
git push --set-upstream origin feature/<nombre>

# Ver estado del repositorio
git status

# Ciclo completo de guardado
git add <archivo>
git commit -m "tipo: descripción del cambio"
git push origin feature/<nombre>

# Traer cambios de main a mi rama (para resolver conflictos)
git fetch origin
git merge origin/main

# Ver historial visual
git log --oneline --graph

# Ver quién cambió qué
git log --oneline --graph --all
```

---

## 8. Preguntas Frecuentes

**¿Qué hago si hice un commit en `main` por error?**

```bash
git checkout main
git log --oneline  # anote el hash del commit erróneo
git revert <hash>  # crea un commit que deshace el anterior
git push origin main
```

**¿Puedo hacer push directo a `main`?**

No — la rama está protegida. GitHub lo rechazará. Todo cambio debe ir por PR.

**¿Qué pasa si mergeo mi PR antes de que el conflicto esté resuelto?**

GitHub no lo permitirá si la protección de rama está activa. Si no está activa, el merge puede romper el código de los demás. Siempre resuelva primero.

**¿Qué tipo de mensajes de commit debo usar?**

Use prefijos convencionales:
- `feat:` — nueva funcionalidad
- `test:` — añadir o modificar pruebas
- `fix:` — corrección de error
- `docs:` — cambios en documentación
- `refactor:` — reorganización sin cambio de comportamiento

---

## 9. Para ir más allá

Si su equipo termina antes del tiempo, explore:

- **Branch protection avanzada**: configuren *required status checks* para que `pytest` deba pasar antes de permitir el merge (requiere GitHub Actions).
- **`git revert` vs `git reset`**: experimenten deshaciendo un commit de dos formas distintas y observen la diferencia en el historial.
- **Issues**: creen un Issue en GitHub describiendo una mejora a la lista enlazada (p. ej. `prepend`, `reverse`) y asígnenlo a un compañero. Así practican el flujo completo issue → branch → PR → close.

---

## Referencias

1. CSE 29 Winter 2025 — Lab 8, UCSD: https://cse29winter2025.github.io/lab8
2. Software Carpentry — Version Control with Git (Collaboration): https://swcarpentry.github.io/git-novice/
3. GitHub Docs — About collaborative development models: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/getting-started/about-collaborative-development-models
4. GitHub Skills — Review pull requests: https://github.com/skills/review-pull-requests
5. GitHub Skills — Resolve merge conflicts: https://github.com/skills/resolve-merge-conflicts
6. Intermediate Research Software Development (SSI): https://softwaresaved.github.io/az-intermediate-software-skills-course/41-code-review/index.html
7. GitHub Blog — Pull requests in the classroom: https://github.blog/developer-skills/career-growth/pull-requests-in-the-classroom/

> [!important]
> Este material fue desarrollado con apoyo de herramientas de IA como asistente de redacción y estructuración, adaptando el Lab 8 de CSE 29 (UCSD, Winter 2025) al contexto del programa de Ingeniería de Sistemas de la Universidad de Antioquia. El contenido ha sido supervisado y debe ser validado por el docente antes de su aplicación.
