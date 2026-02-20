## 🖥️ API 3D browser em Python (Qt)

**Visualizar avatar + mundo e mover com teclado/rato**

A ideia é criar um cliente 3D leve que funcione como “browser” do mundo (ex.: grid em **OpenSim**), com:

* Janela **Qt**
* Renderização 3D
* Avatar controlado por teclado e rato
* Base pronta para integrar API (HTTP/WebSocket)

---

# 🧱 Arquitetura da Aplicação

![Image](https://www.qt.io/hubfs/MicrosoftTeams-image.png)

![Image](https://i.sstatic.net/IrPj8.png)

![Image](https://dfzljdn9uc3pi.cloudfront.net/2024/cs-1926/1/fig-1-2x.jpg)

![Image](https://www.biorxiv.org/content/biorxiv/early/2025/08/19/2021.12.31.474634/F1.large.jpg)

### Camadas:

1. **UI (Qt Window)**
2. **Motor 3D (OpenGL via QOpenGLWidget)**
3. **Sistema de Input (teclado + rato)**
4. **Cliente API (REST/WebSocket)**
5. **Sistema de Avatar**

---

# 📦 Dependências

Instalar:

```bash
pip install PySide6 PyOpenGL numpy websockets requests
```

---

# 🏗️ Estrutura do Projeto

```
rpg_client/
│
├── main.py
├── world.py
├── avatar.py
├── camera.py
└── api_client.py
```

---

# 1️⃣ Criar Janela Qt + Motor 3D

## 📄 main.py

```python
import sys
from PySide6.QtWidgets import QApplication, QMainWindow
from world import WorldWidget

# Passo 1: Criar aplicação Qt
app = QApplication(sys.argv)

# Passo 2: Criar janela principal
window = QMainWindow()
window.setWindowTitle("RPG 3D Client")
window.setGeometry(100, 100, 1280, 720)

# Passo 3: Adicionar widget 3D
world = WorldWidget()
window.setCentralWidget(world)

window.show()
sys.exit(app.exec())
```

---

# 2️⃣ Criar Mundo 3D

## 📄 world.py

```python
from PySide6.QtOpenGLWidgets import QOpenGLWidget
from PySide6.QtCore import Qt
from OpenGL.GL import *
import numpy as np

class WorldWidget(QOpenGLWidget):

    def __init__(self):
        super().__init__()

        # Posição do avatar
        self.avatar_pos = np.array([0.0, 0.0, -5.0])

        # Controle de teclas pressionadas
        self.keys = set()

    # Inicialização OpenGL
    def initializeGL(self):
        glEnable(GL_DEPTH_TEST)

    # Renderização
    def paintGL(self):
        glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)

        glLoadIdentity()

        # Mover cena para posição do avatar
        glTranslatef(self.avatar_pos[0],
                     self.avatar_pos[1],
                     self.avatar_pos[2])

        # Desenhar um cubo simples (avatar placeholder)
        self.draw_cube()

    # Cubo simples
    def draw_cube(self):
        glBegin(GL_QUADS)
        glColor3f(0.2, 0.6, 1.0)

        # Frente
        glVertex3f(-1, -1,  1)
        glVertex3f( 1, -1,  1)
        glVertex3f( 1,  1,  1)
        glVertex3f(-1,  1,  1)

        glEnd()
```

---

# 3️⃣ Movimento com Teclado

Adicionar ao `WorldWidget`:

```python
    # Detectar tecla pressionada
    def keyPressEvent(self, event):
        self.keys.add(event.key())
        self.update_movement()

    # Detectar tecla solta
    def keyReleaseEvent(self, event):
        self.keys.discard(event.key())

    # Atualizar posição do avatar
    def update_movement(self):
        speed = 0.2

        if Qt.Key_W in self.keys:
            self.avatar_pos[2] += speed
        if Qt.Key_S in self.keys:
            self.avatar_pos[2] -= speed
        if Qt.Key_A in self.keys:
            self.avatar_pos[0] += speed
        if Qt.Key_D in self.keys:
            self.avatar_pos[0] -= speed

        self.update()
```

### 🎮 Controles:

| Tecla | Ação     |
| ----- | -------- |
| W     | Frente   |
| S     | Trás     |
| A     | Esquerda |
| D     | Direita  |

---

# 4️⃣ Movimento com Rato (Câmara)

Adicionar rotação:

```python
    def mouseMoveEvent(self, event):
        dx = event.position().x() - self.width()/2
        dy = event.position().y() - self.height()/2

        self.avatar_pos[0] += dx * 0.001
        self.avatar_pos[1] -= dy * 0.001

        self.update()
```

---

# 5️⃣ Cliente API (Comunicação com Servidor IA)

## 📄 api_client.py

```python
import requests

class APIClient:

    def __init__(self, base_url):
        self.base_url = base_url

    def send_action(self, player_id, action):
        response = requests.post(
            f"{self.base_url}/action",
            json={
                "player_id": player_id,
                "action": action
            }
        )
        return response.json()
```

---

# 6️⃣ Enviar Movimento para o Servidor

No `update_movement()`:

```python
from api_client import APIClient

self.api = APIClient("http://localhost:8000")

# Dentro do movimento
self.api.send_action("player_001", {
    "position": self.avatar_pos.tolist()
})
```

---

# 🔄 Fluxo Final

```text
Teclado/Rato
      ↓
Qt Window
      ↓
Motor 3D (OpenGL)
      ↓
Atualiza posição
      ↓
Envia para API
      ↓
Servidor IA / OpenSim
```

---

# 🚀 Próximo Nível (Recomendado)

Para algo mais profissional:

* Substituir OpenGL manual por **moderngl**
* Usar motor como Panda3D
* Implementar WebSocket em tempo real
* Importar modelos .GLTF
* Implementar colisões físicas
* Sincronizar múltiplos jogadores

---

# 🎯 Resultado

Agora tens:

* Janela Qt
* Renderização 3D básica
* Avatar controlável
* Comunicação com servidor
* Estrutura pronta para integrar OpenSim

---
