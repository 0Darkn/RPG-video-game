# 🧠 Sistema de Memória para NPCs

---

# 📌 Objetivos do Sistema

O sistema de memória deve permitir que o NPC:

* Lembre interações passadas
* Desenvolva opinião sobre jogadores
* Evolua emocionalmente
* Tenha objetivos próprios
* Reaja ao estado global do mundo
* Mantenha coerência narrativa

---

# 🏗️ Arquitetura Geral da Memória

![Image](https://media.licdn.com/dms/image/v2/D4E22AQGNz66rb7457w/feedshare-shrink_800/B4EZlLIFfNIoAg-/0/1757902036353?e=2147483647\&t=HNU6FiOZh5rDkX1P_Zvq1jyCpsky1V5PreLg5Z5wd6M\&v=beta)

![Image](https://www.researchgate.net/publication/265421004/figure/fig6/AS%3A668697874161666%401536441405088/Extended-NPC-architecture-visual-perception-working-memory-and-strategic-planning-using.jpg)

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2AW55mlf_j7MqqLwhkREqbjg.jpeg)

![Image](https://miro.medium.com/1%2AqxX1LtmcyynbdS0ghRAlEQ.png)

A memória do NPC é dividida em 5 camadas principais:

1. **Memória de Curto Prazo (STM)**
2. **Memória de Longo Prazo (LTM)**
3. **Estado Emocional**
4. **Identidade e Personalidade**
5. **Consciência do Mundo**

---

# 1️⃣ Memória de Curto Prazo (Short-Term Memory)

### 🎯 Função

Guardar o contexto imediato da interação atual.

### ⏳ Duração

* Últimos 5 a 20 eventos
* Limpa após timeout ou fim de sessão

### 📦 Estrutura Exemplo (JSON)

```json
{
  "npc_id": "guardiao_001",
  "current_context": [
    {"player": "Jose", "said": "Quem és tu?"},
    {"npc": "Sou o guardião do templo."}
  ],
  "current_goal": "Avaliar moral do jogador",
  "location": "Templo Central",
  "threat_level": 0.2
}
```

### 💾 Armazenamento

* Redis (recomendado)
* Cache em memória RAM

---

# 2️⃣ Memória de Longo Prazo (Long-Term Memory)

### 🎯 Função

Guardar informações persistentes ao longo do tempo.

### 📂 Armazenamento

* MySQL / PostgreSQL
* Estrutura indexada por `npc_id` e `player_uuid`

---

## 🗂 Estrutura Base

```json
{
  "npc_id": "guardiao_001",
  "known_players": {
    "player_uuid": {
      "reputation": 72,
      "relationship_type": "suspeito",
      "last_interaction": "2026-02-20",
      "memories": [
        "Mentiu sobre a missão da torre",
        "Ajudou um aldeão ferido"
      ]
    }
  }
}
```

### 🔎 Estratégia Inteligente

* Armazenar memórias como texto curto
* Criar embeddings vetoriais
* Recuperar apenas as 3–5 mais relevantes por interação

---

# 3️⃣ Estado Emocional Dinâmico

NPCs devem ter emoções numéricas contínuas (0.0 – 1.0).

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

---

## 🔄 Atualização Emocional

Cada evento altera valores:

| Evento             | Efeito     |
| ------------------ | ---------- |
| Mentira do jogador | +raiva     |
| Ato moral          | +confiança |
| Ameaça direta      | +medo      |
| Louvor ao NPC      | +orgulho   |

### Fórmula Exemplo

```text
novo_valor = valor_atual + (impacto_evento × personalidade)
```

---

# 4️⃣ Identidade e Personalidade (Camada Fixa)

Define o “DNA” do NPC.

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

### 🧠 Importância

Esta camada orienta o prompt enviado ao modelo de IA.

---

# 5️⃣ Consciência do Mundo

Permite que o NPC reaja ao estado global do RPG.

```json
{
  "world_state": {
    "guerra_ativa": true,
    "nivel_caos": 0.7,
    "lider_atual": "Imperador Kael"
  }
}
```

---

# 🧠 Sistema de Pensamentos Internos

Para criar sensação de consciência:

```json
{
  "internal_thoughts": [
    "Este humano esconde algo.",
    "Preciso testar sua fé."
  ],
  "private_objectives": [
    "Proteger o templo",
    "Descobrir traidores"
  ]
}
```

⚠️ Estes pensamentos não são mostrados ao jogador — apenas influenciam a resposta.

---

# 🔄 Fluxo de Processamento

1. Jogador envia mensagem (LSL → API)
2. Sistema carrega:

   * Identidade
   * Emoção atual
   * 3–5 memórias relevantes
3. Gera resposta com IA
4. Atualiza:

   * Emoções
   * Nova memória
   * Relação com jogador

---

# 📊 Modelo Consolidado

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

# 🚀 Resultado Esperado

Com esta estrutura:

* NPC desenvolve personalidade consistente
* Recorda decisões antigas
* Evolui emocionalmente
* Reage ao mundo global
* Mantém coerência narrativa
* Simula “consciência” emergente

---
