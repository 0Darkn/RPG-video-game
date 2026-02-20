
O **Panda3D** é um motor 3D open-source muito usado em Python, ideal para:

* Visualização 3D em tempo real
* Controlo por teclado e rato
* Integração com IA
* Cliente 3D personalizado (tipo viewer de OpenSim)

---

# 🧠 Arquitetura Geral

Integração:

* 🧠 IA (LLM / memória NPC)
* 🌍 OpenSim (mundo virtual)
* 🎮 Panda3D (render + controlo)
* 🪟 Qt (interface tipo browser 3D)

```
+------------------------+
| Qt Window              |
|  +------------------+  |
|  | Panda3D Render   |  |
|  | 3D World View    |  |
|  +------------------+  |
|  UI lateral / chat     |
+------------------------+

        ↓

+------------------------+
| Game Controller        |
| - Input                |
| - Avatar Controller    |
| - Camera               |
+------------------------+

        ↓

+------------------------+
| OpenSim Connector      |
| - Login                |
| - Sync posição         |
| - Eventos              |
+------------------------+

        ↓

+------------------------+
| IA / Memória NPC       |
+------------------------+
```

---

# 🚀 Passo 1 — Instalar Panda3D

```bash
pip install panda3d
```

---

# 🧩 Estrutura do Projeto

```
viewer/
│
├── main.py
├── world.py
├── avatar.py
├── camera.py
├── input_controller.py
└── opensim_connector.py
```

---

# 🎮 Passo 2 — Janela 3D Base com Panda3D

```python
# main.py

from direct.showbase.ShowBase import ShowBase
from panda3d.core import WindowProperties

class Viewer(ShowBase):
    def __init__(self):
        super().__init__()

        self.disableMouse()  # desativa câmara padrão

        # Criar chão simples
        self.scene = self.loader.loadModel("models/environment")
        self.scene.reparentTo(self.render)
        self.scene.setScale(0.1)
        self.scene.setPos(-8, 42, 0)

        # Criar avatar (cubo simples)
        self.avatar = self.loader.loadModel("models/box")
        self.avatar.reparentTo(self.render)
        self.avatar.setScale(1)
        self.avatar.setPos(0, 10, 1)

        # Controlos
        self.accept("w", self.move_forward)
        self.accept("s", self.move_backward)

    def move_forward(self):
        self.avatar.setY(self.avatar, 1)

    def move_backward(self):
        self.avatar.setY(self.avatar, -1)

app = Viewer()
app.run()
```

---

# 🎮 Controlo com Teclado + Rato

Movimento suave usando task:

```python
self.taskMgr.add(self.update, "update")

def update(self, task):
    dt = globalClock.getDt()

    if self.key_map["forward"]:
        self.avatar.setY(self.avatar, 5 * dt)

    return task.cont
```

---

# 🎥 Câmara tipo Third-Person

```python
self.camera.setPos(0, -15, 5)
self.camera.lookAt(self.avatar)
```

Ou câmara livre:

```python
self.disableMouse()
```

---

# 🌍 Integração com OpenSim

O **OpenSim** usa protocolo semelhante ao Second Life.

Para integrar:

1. Login via API
2. Receber posição do avatar
3. Atualizar Panda3D
4. Enviar movimento ao servidor

Arquitetura recomendada:

```
OpenSimConnector
 ├── login()
 ├── send_position(x,y,z)
 ├── receive_updates()
```

Comunicação pode ser:

* XML-RPC
* REST
* WebSocket
* LibOpenMetaverse (via bridge Python)

---

# 🧠 Integração com IA + NPC

Cada NPC pode ser um NodePath em Panda3D:

```python
npc = self.loader.loadModel("models/box")
npc.setPos(5, 15, 1)
```

Memória pode ser estrutura:

```python
npc_memory = {
    "short_term": [],
    "long_term": [],
    "relationships": {},
    "personality": {
        "friendly": 0.8,
        "aggressive": 0.2
    }
}
```

---

# 🖥 Integração com Qt (janela tipo browser 3D)

Usar:

```bash
pip install PyQt5
```

Integração:

* Panda3D renderiza dentro de um widget Qt
* Chat lateral
* Inventário
* Lista de NPCs
* Debug IA

---

# 🔥 Próximo Nível

Podemos evoluir para:

* Sistema de animações
* Física (Bullet integrado no Panda3D)
* Carregamento de mapas OpenSim
* Sistema de tarefas para NPCs
* Streaming de mundo
* Mini-browser de assets

---
