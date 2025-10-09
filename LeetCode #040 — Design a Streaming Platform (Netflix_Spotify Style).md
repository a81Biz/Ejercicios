# 🎬 **LeetCode #040 — Design a Streaming Platform (Netflix/Spotify Style)**

> **Tema:** Catálogo multimedia, usuarios, listas personalizadas y reproducción de contenido
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Composición + Herencia funcional (MediaItem → Movie/Song)*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que modele una **plataforma de streaming**,
> permitiendo **reproducir, pausar, buscar, y organizar contenido multimedia**
> (películas, canciones o episodios) con **listas personalizadas** por usuario.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **usuarios**.
* Registrar **contenido multimedia** (películas o canciones).
* Permitir **buscar y reproducir** contenido.
* Crear **listas personalizadas** (*playlists*).
* Simular acciones básicas de reproducción: **play, pause, stop**.
* Registrar un **historial de reproducciones** por usuario.

---

**💬 Reexplicación en voz alta:**

> “Voy a crear una plataforma tipo Netflix o Spotify.
> Cada `User` puede buscar y reproducir elementos (`MediaItem`),
> que pueden ser `Movie` o `Song`.
> El `StreamingManager` centraliza usuarios, catálogos, playlists y control de reproducción.”

---

**❓ Preguntas al entrevistador:**

* ¿El contenido puede ser de distintos tipos (música, video)? → ✅ Sí.
* ¿Se necesita manejar tiempos de reproducción? → 🟡 Solo simulados (sin streaming real).
* ¿Debe permitir múltiples playlists por usuario? → ✅ Sí.
* ¿Debe llevar historial? → ✅ Sí, con marcas de tiempo.

**🧩 Casos límite:**

* [x] Intentar reproducir contenido inexistente.
* [x] Usuario sin playlists.
* [x] Contenido repetido en lista.
* [x] Detener reproducción sin haber iniciado.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Modelar entidades multimedia y usuarios con listas dinámicas y registro de reproducción.

---

### 🧩 Clases Principales

| Clase              | Responsabilidad                                         | Relaciones                      |
| ------------------ | ------------------------------------------------------- | ------------------------------- |
| `StreamingManager` | Controla usuarios, catálogo y reproducción.             | Coordina `User` y `MediaItem`.  |
| `User`             | Representa al suscriptor.                               | Tiene playlists y un historial. |
| `Playlist`         | Lista personalizada de contenido.                       | Contiene `MediaItem`.           |
| `MediaItem`        | Clase base para todo contenido reproducible.            | Heredada por `Movie` y `Song`.  |
| `Player`           | Controla la sesión de reproducción (play, pause, stop). | Asociada a un `User`.           |

---

### 🔁 Flujo de Operación

1. Se registran usuarios y contenido multimedia.
2. El usuario crea playlists y agrega contenido.
3. Puede buscar por título.
4. El usuario reproduce, pausa o detiene el contenido.
5. Cada reproducción se registra en el historial.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|  StreamingManager    |
+----------------------+
| - users, catalog     |
+----------------------+
| + registerUser()     |
| + addMedia()         |
| + search()           |
| + play()             |
+----------------------+
          |
          v
+----------------------+
|        User          |
+----------------------+
| id, name, playlists, history |
+----------------------+

+----------------------+
|       Playlist       |
+----------------------+
| name, items          |
+----------------------+

+----------------------+
|     MediaItem        |
+----------------------+
| id, title, duration  |
+----------------------+

+----------------------+
|   Movie / Song       |
+----------------------+
| genre, artist, year  |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Construir flujo de registro, búsqueda, playlist y reproducción simulada.

```python
from datetime import datetime

class MediaItem:
    _counter = 1

    def __init__(self, title, duration):
        self.id = MediaItem._counter
        MediaItem._counter += 1
        self.title = title
        self.duration = duration  # en minutos
        self.added_at = datetime.now()

    def play(self):
        print(f"▶️ Reproduciendo: {self.title} ({self.duration} min)")

class Movie(MediaItem):
    def __init__(self, title, duration, genre, year):
        super().__init__(title, duration)
        self.genre = genre
        self.year = year

    def __str__(self):
        return f"🎬 {self.title} ({self.year}) - {self.genre}"

class Song(MediaItem):
    def __init__(self, title, duration, artist):
        super().__init__(title, duration)
        self.artist = artist

    def __str__(self):
        return f"🎵 {self.title} - {self.artist}"

class Playlist:
    def __init__(self, name):
        self.name = name
        self.items = []

    def add_item(self, item):
        if item in self.items:
            print("⚠️ El elemento ya está en la lista.")
            return
        self.items.append(item)
        print(f"➕ '{item.title}' agregado a la playlist '{self.name}'.")

    def show(self):
        print(f"📂 Playlist '{self.name}':")
        for i, item in enumerate(self.items, 1):
            print(f"  {i}. {item}")
        if not self.items:
            print("  (vacía)")

class Player:
    def __init__(self, user):
        self.user = user
        self.current = None
        self.is_playing = False

    def play(self, media):
        self.current = media
        self.is_playing = True
        media.play()
        self.user.history.append((media, datetime.now()))

    def pause(self):
        if not self.is_playing or not self.current:
            print("⏸️ No hay nada en reproducción.")
            return
        print(f"⏸️ Pausado: {self.current.title}")
        self.is_playing = False

    def stop(self):
        if not self.current:
            print("⏹️ Nada que detener.")
            return
        print(f"⏹️ Detenido: {self.current.title}")
        self.current = None
        self.is_playing = False

class User:
    def __init__(self, uid, name):
        self.id = uid
        self.name = name
        self.playlists = []
        self.history = []
        self.player = Player(self)

    def create_playlist(self, name):
        p = Playlist(name)
        self.playlists.append(p)
        print(f"🎧 Playlist '{name}' creada por {self.name}.")
        return p

    def show_history(self):
        print(f"📜 Historial de {self.name}:")
        for media, t in self.history:
            print(f"  [{t.strftime('%H:%M')}] {media.title}")
        if not self.history:
            print("  (sin reproducciones)")

class StreamingManager:
    def __init__(self):
        self.users = {}
        self.catalog = []

    def register_user(self, uid, name):
        if uid in self.users:
            print("⚠️ Usuario ya existente.")
            return
        user = User(uid, name)
        self.users[uid] = user
        print(f"🧍 Usuario {name} registrado.")
        return user

    def add_media(self, media):
        self.catalog.append(media)
        print(f"📼 '{media.title}' agregado al catálogo.")

    def search(self, keyword):
        print(f"🔍 Resultados para '{keyword}':")
        results = [m for m in self.catalog if keyword.lower() in m.title.lower()]
        for r in results:
            print(f"  {r}")
        if not results:
            print("  Sin coincidencias.")
        return results

    def play(self, uid, media):
        user = self.users.get(uid)
        if not user:
            print("🚫 Usuario no encontrado.")
            return
        user.player.play(media)
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear plataforma
platform = StreamingManager()

# Registrar usuario
u1 = platform.register_user("U1", "Isabel")

# Agregar contenido
m1 = Movie("El Castillo Vagabundo", 120, "Fantasía", 2004)
m2 = Song("El Viaje de Chihiro", 4, "Joe Hisaishi")
platform.add_media(m1)
platform.add_media(m2)

# Crear playlist y agregar contenido
p1 = u1.create_playlist("Studio Ghibli")
p1.add_item(m1)
p1.add_item(m2)
p1.show()

# Buscar
platform.search("Castillo")

# Reproducir
platform.play("U1", m1)
platform.play("U1", m2)
u1.player.pause()
u1.player.stop()

# Historial
u1.show_history()
```

**🎯 Salida esperada:**

```
🧍 Usuario Isabel registrado.
📼 'El Castillo Vagabundo' agregado al catálogo.
📼 'El Viaje de Chihiro' agregado al catálogo.
🎧 Playlist 'Studio Ghibli' creada por Isabel.
➕ 'El Castillo Vagabundo' agregado a la playlist 'Studio Ghibli'.
➕ 'El Viaje de Chihiro' agregado a la playlist 'Studio Ghibli'.
📂 Playlist 'Studio Ghibli':
  1. 🎬 El Castillo Vagabundo (2004) - Fantasía
  2. 🎵 El Viaje de Chihiro - Joe Hisaishi
🔍 Resultados para 'Castillo':
  🎬 El Castillo Vagabundo (2004) - Fantasía
▶️ Reproduciendo: El Castillo Vagabundo (120 min)
▶️ Reproduciendo: El Viaje de Chihiro (4 min)
⏸️ Pausado: El Viaje de Chihiro
⏹️ Detenido: El Viaje de Chihiro
📜 Historial de Isabel:
  [21:00] El Castillo Vagabundo
  [21:01] El Viaje de Chihiro
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                                                  |
| ---------------------- | -------------------------------------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(N) para búsqueda en catálogo.                                                        |
| 💾 **Espacio:**        | O(U + M + P) — usuarios, media y playlists.                                            |
| ⚡ **Escalabilidad:**   | Alta — puede ampliarse a streaming concurrente o recomendaciones basadas en historial. |
| 🧩 **Tipo de patrón:** | Controlador + Composición + Herencia funcional.                                        |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de composición multimedia y simulación de sesión de reproducción.

**🔧 Posibles optimizaciones:**

* Implementar `RecommendationEngine` con base en historial.
* Agregar herencia múltiple (por ejemplo, `Podcast` con duración variable).
* Integrar control de sesiones simultáneas o perfiles por usuario.

**📚 Lecciones aprendidas:**

* La **composición** entre `User`, `Playlist` y `Player` permite una experiencia modular.
* El patrón **herencia funcional** simplifica la gestión de múltiples tipos de contenido.
* Este modelo es la base conceptual de plataformas modernas de streaming.

**✅ Conclusión final:**

> “Diseñé una plataforma de streaming que maneja usuarios, playlists y contenido multimedia
> aplicando herencia, composición y control de sesión.
> El diseño refleja los fundamentos estructurales de sistemas como Netflix o Spotify.”

---

📘 **Resumen final**

| Aspecto          | Valor                                          |
| ---------------- | ---------------------------------------------- |
| Patrón           | Controlador + Composición + Herencia funcional |
| Complejidad      | O(N)                                           |
| Palabra clave    | “Streaming modular”                            |
| Tipo de problema | Modelado de plataforma multimedia              |
| Nivel            | 🟡 Medio                                       |

---