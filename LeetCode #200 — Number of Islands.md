# 🏝️ **LeetCode #200 — Number of Islands**

> **Tema:** DFS / BFS en Matriz
> **Nivel:** 🟡 Medio
> **Patrón:** *Contar Componentes Conectados (Graph Traversal)*
> **Categoría:** Algoritmos

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Comprender completamente la naturaleza del problema antes de pensar en código.

**📖 Enunciado resumido:**
Dada una matriz `grid` de `m x n` compuesta por `'1'` (tierra) y `'0'` (agua),
determina cuántas **islas** existen.

Una **isla** es un conjunto de celdas `'1'` conectadas *vertical u horizontalmente* (no en diagonal).
Se puede modificar el grid o usar una estructura auxiliar para marcar las celdas visitadas.

**🔢 Ejemplo de entrada:**

```
grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
```

**🎯 Salida esperada:**

```
3
```

**💬 Reexplicación en voz alta:**

> “Debo recorrer toda la matriz y, cada vez que encuentre un ‘1’,
> iniciar una búsqueda que marque toda la isla como visitada.
> Cada búsqueda representa una isla distinta.”

**❓ Preguntas al entrevistador:**

* ¿Qué representa exactamente una isla? → Conjunto de celdas ‘1’ conectadas ortogonalmente.
* ¿Las diagonales cuentan como conexión? → ❌ No.
* ¿Puedo modificar el grid? → ✅ Sí, puede hacerse in-place.
* ¿Cuál es el tamaño máximo del grid? → Hasta 300x300 en LeetCode.
* ¿Qué pasa si no hay tierra (‘1’)? → Retornar 0.

**🧩 Casos límite:**

* [x] Grid vacío → `0`
* [x] Solo agua (`0`) → `0`
* [x] Solo tierra (`1`) → `1`
* [x] Varias islas separadas → correcto conteo esperado.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Visualizar la estructura de la solución (recorrido + marcado).

**📚 Tipo de problema:**
➡️ Recorrido de grafo implícito sobre una matriz bidimensional → *DFS / BFS sobre grid*.

---

### 💡 Enfoque 1 — DFS Recursivo

**Idea:**

* Recorrer la matriz.
* Al encontrar un `'1'`, iniciar una función `dfs` que explore sus vecinos (arriba, abajo, izquierda, derecha) y marque las celdas conectadas como `'0'` (visitadas).
* Incrementar el contador de islas cada vez que se inicia un nuevo `dfs`.

**Pseudocódigo:**

```
func numIslands(grid):
    count = 0
    para cada i en filas:
        para cada j en columnas:
            si grid[i][j] == '1':
                dfs(i, j)
                count++
    return count

func dfs(i, j):
    si fuera de límites o grid[i][j] == '0': return
    grid[i][j] = '0'
    dfs(i+1, j)
    dfs(i-1, j)
    dfs(i, j+1)
    dfs(i, j-1)
```

**Complejidad:**
⏱️ Tiempo: `O(M×N)` — cada celda se visita una vez.
💾 Espacio: `O(M×N)` (por recursión en el peor caso).

---

### ⚙️ Enfoque 2 — BFS Iterativo

**Idea:**
Usar una **cola (queue)** para recorrer los vecinos por niveles,
marcando como visitadas las celdas conectadas a cada isla.

**Complejidad:**
⏱️ `O(M×N)`
💾 `O(min(M, N))` (profundidad promedio del BFS).

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Mostrar claridad, recursión controlada y recorrido completo.

**✍️ Implementación (JavaScript - DFS Recursivo):**

```js
var numIslands = function(grid) {
    if (!grid || grid.length === 0) return 0;
    let count = 0;

    const rows = grid.length;
    const cols = grid[0].length;

    const dfs = (r, c) => {
        if (r < 0 || c < 0 || r >= rows || c >= cols || grid[r][c] === '0') return;
        grid[r][c] = '0'; // marcar como visitado

        dfs(r + 1, c);
        dfs(r - 1, c);
        dfs(r, c + 1);
        dfs(r, c - 1);
    };

    for (let r = 0; r < rows; r++) {
        for (let c = 0; c < cols; c++) {
            if (grid[r][c] === '1') {
                count++;
                dfs(r, c);
            }
        }
    }

    return count;
};
```

**🗣️ Explicación hablada:**

> “Recorro cada celda.
> Si encuentro una tierra no visitada (‘1’), inicio DFS para marcar toda la isla.
> Al finalizar esa búsqueda, incremento el contador.
> Así, cada llamada DFS representa una isla completa.”

---

### 💡 Alternativa (BFS Iterativo):

```js
var numIslands = function(grid) {
    if (!grid || grid.length === 0) return 0;
    let count = 0;

    const rows = grid.length, cols = grid[0].length;
    const directions = [[1,0],[-1,0],[0,1],[0,-1]];

    const bfs = (r, c) => {
        const queue = [[r, c]];
        grid[r][c] = '0';
        while (queue.length > 0) {
            const [x, y] = queue.shift();
            for (const [dx, dy] of directions) {
                const nx = x + dx, ny = y + dy;
                if (nx >= 0 && ny >= 0 && nx < rows && ny < cols && grid[nx][ny] === '1') {
                    grid[nx][ny] = '0';
                    queue.push([nx, ny]);
                }
            }
        }
    };

    for (let i = 0; i < rows; i++) {
        for (let j = 0; j < cols; j++) {
            if (grid[i][j] === '1') {
                count++;
                bfs(i, j);
            }
        }
    }

    return count;
};
```

> “Recorro con BFS desde cada celda no visitada, marcando todas las celdas conectadas.
> Cada vez que inicio un BFS, cuento una isla más.”

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar el Código

> 🎯 **Objetivo:** Asegurar correcto conteo en distintos tipos de matrices.

**Casos de prueba:**

| # | Grid de entrada                                                             | Resultado esperado | Resultado |
| - | --------------------------------------------------------------------------- | ------------------ | --------- |
| 1 | `[["1","1","1"],["0","1","0"],["1","1","1"]]`                               | `1`                | ✅         |
| 2 | `[["1","1","0","0"],["1","1","0","0"],["0","0","1","0"],["0","0","0","1"]]` | `3`                | ✅         |
| 3 | `[["0","0","0"],["0","0","0"]]`                                             | `0`                | ✅         |
| 4 | `[["1"]]`                                                                   | `1`                | ✅         |
| 5 | `[]`                                                                        | `0`                | ✅         |

**🧠 Simulación (DFS en ejemplo #1):**

```
Inicio (0,0):
  Marca (0,0)
  → (0,1) -> (0,2)
  → (1,1)
  → (2,1) -> (2,0)
  → (2,2)
Todas visitadas → isla #1 completa
```

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Rendimiento

| Métrica                | Valor                                 |
| ---------------------- | ------------------------------------- |
| ⏱️ **Tiempo:**         | O(M×N) — cada celda visitada una vez. |
| 💾 **Espacio:**        | O(M×N) (DFS recursivo) o O(W) (BFS).  |
| ⚡ **Escalabilidad:**   | Hasta 300×300 sin problema.           |
| 🧩 **Tipo de patrón:** | Flood Fill / Componentes conectados.  |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar madurez técnica y capacidad de generalización.

**🔧 Posibles optimizaciones:**

* Usar matriz de booleanos para marcar visitados si no se puede modificar `grid`.
* Cambiar a BFS iterativo para evitar stack overflow en matrices grandes.
* Expandir a diagonales si el contexto del problema lo requiere (8 direcciones).
* Aplicar la misma técnica para problemas de agrupamiento (clusters, regiones, etc.).

**📚 Lecciones aprendidas:**

* Los recorridos DFS/BFS en matrices modelan *grafos no explícitos*.
* Cada “componente conectado” = una isla = un bloque DFS/BFS.
* Este patrón es reutilizable en detección de regiones, simulaciones y mapas.

**✅ Conclusión final:**

> “Recorro la matriz completa; cada vez que hallo una celda ‘1’,
> lanzo un DFS que marca toda su isla como visitada.
> El número total de DFS iniciados equivale al número de islas.”

---

📘 **Resumen final**

| Aspecto          | Valor                                 |
| ---------------- | ------------------------------------- |
| Patrón           | DFS / BFS en grid                     |
| Complejidad      | O(M×N) tiempo, O(H) espacio           |
| Palabra clave    | “Componentes conectados”              |
| Tipo de problema | Grafo implícito / Recorrido de matriz |
| Nivel            | 🟡 Medio                              |


