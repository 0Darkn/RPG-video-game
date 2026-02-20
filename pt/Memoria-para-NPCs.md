# 🧠 Estrutura de Memória para NPCs (OpenSim + IA)

## 🏗️ Visão Geral da Memória

![Image](https://media.licdn.com/dms/image/v2/D4E22AQHraFcovJcSgQ/feedshare-shrink_800/B4EZuACPn0J0Ag-/0/1767379631618?e=2147483647\&t=lMzbkTEkhkZ3rBXeSKWBIO_2W4NEsqKuyvk_fFSV9KQ\&v=beta)

![Image](https://www.researchgate.net/publication/265421004/figure/fig6/AS%3A668697874161666%401536441405088/Extended-NPC-architecture-visual-perception-working-memory-and-strategic-planning-using.jpg)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AW55mlf_j7MqqLwhkREqbjg.jpeg)

![Image](https://mintcdn.com/langchain-5e9cc07a/dL5Sn6Cmy9pwtY0V/oss/images/short-vs-long.png?auto=format\&fit=max\&n=dL5Sn6Cmy9pwtY0V\&q=85\&s=62665893848db800383dffda7367438a)

Dividimos a memória em **5 camadas principais**:

1. 🧠 Memória de Curto Prazo (contexto imediato)
2. 📚 Memória de Longo Prazo (histórico persistente)
3. ❤️ Estado Emocional
4. 🎭 Identidade e Personalidade
5. 🌍 Memória do Mundo

---

# 1️⃣ Memória de Curto Prazo (STM)

**Função:**
Manter coerência durante a interação atual.

**Duração:**
Últimos 5–20 eventos.

**Exemplo de estrutura (JSON):**

```json
{
  "npc_id": "anjo_001",
  "current_context": [
    {"player": "Jose", "said": "Quem és tu?"},
    {"npc": "Sou o guardião da verdade."}
  ],
  "current_goal": "Avaliar moral do jogador",
  "location": "Templo Central",
  "threat_level": 0.2
}
```

🔹 Guardado em Redis ou memória RAM.
🔹 Limpo após sessão ou timeout.

---

# 2️⃣ Memória de Longo Prazo (LTM)

**Função:**
Guardar tudo que importa ao longo do tempo.

**Armazenamento:** MySQL / PostgreSQL.

### Estrutura Base

```json
{
  "npc_id": "anjo_001",
  "known_players": {
    "player_uuid": {
      "reputation": 72,
      "last_interaction": "2026-02-20",
      "relationship_type": "suspeito",
      "memories": [
        "Mentiu sobre a missão da torre",
        "Ajudou um inocente"
      ]
    }
  }
}
```

🔹 Indexar por `npc_id` + `player_uuid`
🔹 Usar embeddings para recuperar memórias relevantes

---

# 3️⃣ Estado Emocional Dinâmico

Em vez de emoções fixas, usar **variáveis numéricas contínuas**.

```json
{
  "emotional_state": {
    "raiva": 0.1,
    "compaixao": 0.8,
    "medo": 0.05,
    "orgulho": 0.6,
    "confiança_no_jogador": 0.3
  }
}
```

### Atualização

Cada evento altera valores:

* Mentira → +raiva
* Ato moral → +confiança
* Ameaça → +medo

💡 A resposta do NPC depende desses valores.

---

# 4️⃣ Identidade e Personalidade (Camada Fixa)

Esta camada nunca muda drasticamente.

```json
{
  "arquetipo": "Guardião",
  "alinhamento": "Ordem",
  "valores": ["verdade", "justiça", "sacrificio"],
  "limites_morais": {
    "violencia": 0.4,
    "mentira": 0.1
  },
  "nivel_inteligencia": 0.9,
  "espiritualidade": 1.0
}
```

💡 Isto define como o LLM deve responder.

---

# 5️⃣ Memória do Mundo

Permite que o NPC reaja a eventos globais.

```json
{
  "world_state": {
    "guerra_ativa": true,
    "nivel_caos": 0.7,
    "lider_atual": "Imperador Kael"
  }
}
```

NPC pode mudar comportamento conforme o estado global.

---

# 🔄 Sistema de Recuperação Inteligente (Importante)

Não enviar toda a memória para o LLM.

### Estratégia:

1. Jogador fala
2. Sistema gera embedding da mensagem
3. Busca memórias mais relevantes
4. Injeta apenas:

   * Personalidade
   * Emoção atual
   * 3–5 memórias relevantes

---

# 🧠 Sistema de “Imaginação”

Para parecer consciente:

Adicionar:

```json
{
  "internal_thoughts": [
    "Este humano parece esconder algo.",
    "A energia dele está diferente."
  ],
  "private_objectives": [
    "Testar a fé do jogador",
    "Proteger o templo"
  ]
}
```

⚠️ Internal thoughts não são mostrados ao jogador —
apenas influenciam resposta.

---

# 📊 Modelo Final Consolidado

```json
{
  "identity": {...},
  "emotional_state": {...},
  "short_term_memory": {...},
  "long_term_memory": {...},
  "world_awareness": {...},
  "hidden_thoughts": {...}
}
```

---

# 🏆 Resultado Esperado

Com este sistema:

* NPC lembra-se de decisões antigas
* Desenvolve opinião sobre o jogador
* Muda emocionalmente
* Tem objetivos próprios
* Reage ao estado global
* Parece ter “consciência”

---
