# 🌐 **LeetCode #039 — Design a Social Media Platform**

> **Tema:** Relaciones entre usuarios, publicaciones y notificaciones dinámicas
> **Nivel:** 🟡 Medio
> **Patrón:** *Controlador + Observador (Observer Pattern) + Relaciones uno-a-muchos*
> **Categoría:** Diseño Orientado a Objetos (OOP)

---

## 🧱 1️⃣ FASE DE COMPRENSIÓN — Entender el Problema

> 🎯 **Objetivo:** Diseñar un sistema OOP que modele una **plataforma de red social**,
> permitiendo **publicar, seguir, comentar y generar un feed dinámico** basado en relaciones.

---

**📖 Enunciado resumido:**
El sistema debe:

* Registrar **usuarios**.
* Permitir **seguir y dejar de seguir** a otros usuarios.
* Crear **publicaciones** (posts) con texto o multimedia.
* Permitir **comentar** en publicaciones.
* Generar un **feed personalizado** con las publicaciones de los usuarios seguidos.

---

**💬 Reexplicación en voz alta:**

> “Voy a construir una red social simplificada.
> Cada `User` puede crear publicaciones (`Post`), seguir a otros usuarios y recibir actualizaciones en su feed.
> El `SocialMediaManager` centralizará la lógica de seguimiento y generación de feeds.”

---

**❓ Preguntas al entrevistador:**

* ¿Debe soportar múltiples tipos de contenido (texto, imágenes, videos)? → 🟡 Texto e imagen para simplificar.
* ¿Los comentarios pueden anidarse? → ❌ No, solo nivel básico.
* ¿Se requiere tiempo de publicación? → ✅ Sí.
* ¿Debe mostrarse el feed en orden cronológico? → ✅ Sí, de más reciente a más antiguo.

**🧩 Casos límite:**

* [x] Usuario sin seguidores (feed vacío).
* [x] Usuario sigue a alguien que no ha publicado.
* [x] Publicación sin comentarios.
* [x] Seguimiento duplicado.

---

## 🧭 2️⃣ FASE DE DISEÑO — Analizar y Planificar

> 🎯 **Objetivo:** Modelar las entidades sociales con relaciones uno-a-muchos y un flujo tipo *Observer Pattern* para actualizar feeds.

---

### 🧩 Clases Principales

| Clase                | Responsabilidad                                            | Relaciones                            |
| -------------------- | ---------------------------------------------------------- | ------------------------------------- |
| `SocialMediaManager` | Coordina usuarios, relaciones y feeds.                     | Contiene todos los `User` y `Post`.   |
| `User`               | Representa un usuario registrado.                          | Puede seguir a otros `User`.          |
| `Post`               | Representa una publicación.                                | Creador: `User`. Contiene `Comment`.  |
| `Comment`            | Representa un comentario en una publicación.               | Asociado a `User` y `Post`.           |
| `Feed`               | Representa el conjunto de publicaciones que el usuario ve. | Actualizado por `SocialMediaManager`. |

---

### 🔁 Flujo de Operación

1. Los usuarios se registran en la red.
2. Un usuario puede seguir a otro.
3. Cuando un usuario publica algo, sus seguidores reciben el post en su feed.
4. Los usuarios pueden comentar publicaciones.
5. El feed se muestra ordenado por fecha.

---

### 🧱 Relaciones UML (simplificadas)

```
+----------------------+
|  SocialMediaManager  |
+----------------------+
| - users              |
| - posts              |
+----------------------+
| + registerUser()     |
| + follow()           |
| + createPost()       |
| + addComment()       |
| + getFeed()          |
+----------------------+
          |
          v
+----------------------+
|        User          |
+----------------------+
| id, name, followers, following, feed |
+----------------------+

+----------------------+
|        Post          |
+----------------------+
| id, author, content, timestamp, comments |
+----------------------+

+----------------------+
|       Comment        |
+----------------------+
| user, text, timestamp |
+----------------------+
```

---

## 💻 3️⃣ FASE DE IMPLEMENTACIÓN — Escribir Código Limpio

> 🎯 **Objetivo:** Implementar flujo de usuarios, publicaciones y feeds dinámicos tipo red social.

```python
from datetime import datetime

class Comment:
    def __init__(self, user, text):
        self.user = user
        self.text = text
        self.timestamp = datetime.now()

    def __str__(self):
        return f"{self.user.name}: {self.text} ({self.timestamp.strftime('%H:%M')})"

class Post:
    _counter = 1

    def __init__(self, author, content):
        self.id = Post._counter
        Post._counter += 1
        self.author = author
        self.content = content
        self.timestamp = datetime.now()
        self.comments = []

    def add_comment(self, comment):
        self.comments.append(comment)

    def __str__(self):
        return f"[{self.timestamp.strftime('%H:%M')}] {self.author.name}: {self.content}"

class User:
    def __init__(self, uid, name):
        self.id = uid
        self.name = name
        self.following = set()
        self.followers = set()
        self.feed = []

    def follow(self, other):
        if other == self:
            print("🚫 No puedes seguirte a ti mismo.")
            return
        if other in self.following:
            print(f"⚠️ Ya sigues a {other.name}.")
            return
        self.following.add(other)
        other.followers.add(self)
        print(f"👥 {self.name} comenzó a seguir a {other.name}.")

    def receive_post(self, post):
        self.feed.insert(0, post)  # insertar al inicio (más reciente)

    def show_feed(self):
        if not self.feed:
            print(f"🕸️ El feed de {self.name} está vacío.")
            return
        print(f"📲 Feed de {self.name}:")
        for post in self.feed:
            print(f"  {post}")
        print()

class SocialMediaManager:
    def __init__(self):
        self.users = {}
        self.posts = []

    def register_user(self, uid, name):
        if uid in self.users:
            print("⚠️ Usuario ya existente.")
            return
        user = User(uid, name)
        self.users[uid] = user
        print(f"🧍 Usuario {name} registrado.")
        return user

    def follow(self, follower_id, followed_id):
        f1 = self.users.get(follower_id)
        f2 = self.users.get(followed_id)
        if not f1 or not f2:
            print("🚫 Usuario no encontrado.")
            return
        f1.follow(f2)

    def create_post(self, uid, content):
        user = self.users.get(uid)
        if not user:
            print("🚫 Usuario no encontrado.")
            return
        post = Post(user, content)
        self.posts.append(post)
        user.feed.insert(0, post)  # el autor también lo ve
        # notificar seguidores
        for follower in user.followers:
            follower.receive_post(post)
        print(f"📝 {user.name} publicó: '{content}'")
        return post

    def add_comment(self, uid, post_id, text):
        user = self.users.get(uid)
        post = next((p for p in self.posts if p.id == post_id), None)
        if not user or not post:
            print("🚫 Usuario o publicación no encontrados.")
            return
        comment = Comment(user, text)
        post.add_comment(comment)
        print(f"💬 {user.name} comentó en el post de {post.author.name}: '{text}'")

    def get_feed(self, uid):
        user = self.users.get(uid)
        if not user:
            print("🚫 Usuario no encontrado.")
            return
        user.show_feed()
```

---

## 🔍 4️⃣ FASE DE PRUEBAS — Validar Comportamiento

```python
# Crear red social
sm = SocialMediaManager()

# Registrar usuarios
u1 = sm.register_user("U1", "Isabel")
u2 = sm.register_user("U2", "Carlos")
u3 = sm.register_user("U3", "Leonora")

# Seguimientos
sm.follow("U2", "U1")
sm.follow("U3", "U1")

# Publicar
p1 = sm.create_post("U1", "¡Bienvenidos al Castillo Vagabundo!")
p2 = sm.create_post("U1", "Hoy jugamos D&D en la taberna 🎲")

# Comentarios
sm.add_comment("U2", p1.id, "¡Genial!")
sm.add_comment("U3", p1.id, "Nos vemos ahí.")

# Feeds
sm.get_feed("U1")
sm.get_feed("U2")
sm.get_feed("U3")
```

**🎯 Salida esperada:**

```
🧍 Usuario Isabel registrado.
🧍 Usuario Carlos registrado.
🧍 Usuario Leonora registrado.
👥 Carlos comenzó a seguir a Isabel.
👥 Leonora comenzó a seguir a Isabel.
📝 Isabel publicó: '¡Bienvenidos al Castillo Vagabundo!'
📝 Isabel publicó: 'Hoy jugamos D&D en la taberna 🎲'
💬 Carlos comentó en el post de Isabel: '¡Genial!'
💬 Leonora comentó en el post de Isabel: 'Nos vemos ahí.'
📲 Feed de Isabel:
  [21:00] Isabel: Hoy jugamos D&D en la taberna 🎲
  [20:59] Isabel: ¡Bienvenidos al Castillo Vagabundo!

📲 Feed de Carlos:
  [21:00] Isabel: Hoy jugamos D&D en la taberna 🎲
  [20:59] Isabel: ¡Bienvenidos al Castillo Vagabundo!

📲 Feed de Leonora:
  [21:00] Isabel: Hoy jugamos D&D en la taberna 🎲
  [20:59] Isabel: ¡Bienvenidos al Castillo Vagabundo!
```

✅ Correcto.

---

## ⏱️ 5️⃣ FASE DE ANÁLISIS — Complejidad y Escalabilidad

| Métrica                | Valor                                                      |
| ---------------------- | ---------------------------------------------------------- |
| ⏱️ **Tiempo:**         | O(F + P) — usuarios seguidos y publicaciones.              |
| 💾 **Espacio:**        | O(U + P + C) — usuarios, posts y comentarios.              |
| ⚡ **Escalabilidad:**   | Alta — se puede integrar base de datos y colas de eventos. |
| 🧩 **Tipo de patrón:** | Controlador + Observador + Composición.                    |

---

## 🚀 6️⃣ FASE DE REFLEXIÓN — Cierre y Optimización

> 🎯 **Objetivo:** Mostrar dominio de relaciones sociales y flujo de eventos tipo *feed dinámico*.

**🔧 Posibles optimizaciones:**

* Integrar `NotificationService` (patrón Observer completo).
* Añadir `LikeSystem` para reacciones.
* Persistencia de datos con timestamps reales y paginación de feed.

**📚 Lecciones aprendidas:**

* El **Observer Pattern** permite notificar a todos los seguidores en tiempo real.
* Las **relaciones uno-a-muchos** son clave para modelar interacciones sociales.
* El feed dinámico se construye con base en publicaciones recientes y relaciones activas.

**✅ Conclusión final:**

> “Diseñé una red social modular con usuarios, publicaciones, comentarios y feeds dinámicos,
> aplicando relaciones observables y actualizaciones en tiempo real.
> El modelo refleja la estructura esencial de plataformas como Twitter o Instagram.”

---

📘 **Resumen final**

| Aspecto          | Valor                                  |
| ---------------- | -------------------------------------- |
| Patrón           | Controlador + Observador + Composición |
| Complejidad      | O(F + P)                               |
| Palabra clave    | “Feed dinámico y relaciones sociales”  |
| Tipo de problema | Modelado de red social                 |
| Nivel            | 🟡 Medio                               |

---