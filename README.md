# Seedance Director Mode V3 — Claude Code Skill

Gerador de prompts cinematográficos para **Seedance 2.0 / Jimeng / Doubao / Runway** rodando localmente via Claude Code. Zero custo de API — usa apenas tokens da sua sessão Claude Code.

Replica o agente `cine-diretor` (padrão Michael Mann / Ridley Scott / Emmanuel Lubezki) do sistema Redpine Prompt, produzindo:

- **JSON em chinês** (≤3500 chars) pronto pra colar em Seedance/Jimeng/Doubao/Runway
- **Tabela PT-BR** de beats com composição em camadas (FG/Subject/BG)
- **4 arcos narrativos** obrigatórios: cromático, musical, perspectiva, tese visual
- **Director insight** por beat (sombra que chega antes do sujeito, frame-within-frame, lens flare narrativo, etc)
- **Auto-open no Cursor** ao finalizar (abre o `.md` gerado direto no editor)

## Dois modos de uso

| Modo | Trigger | Quando usar |
|------|---------|-------------|
| **Vibe** | `/seedance vibe` ou `/seedance <briefing>` | Você já sabe o que quer — manda briefing inline e gera direto |
| **Director** | `/seedance director` | Fluxo guiado passo-a-passo em **15 etapas** (tabelas numeradas com ★ defaults) |

Se digitar só `/seedance` (sozinho), o skill pergunta qual modo. Se passar um briefing parcial junto com o modo (ex: `/seedance director 15s thriller`), ele pré-preenche e só pergunta o que falta.

---

## Requisitos

- [Claude Code](https://claude.com/claude-code) instalado
- macOS, Linux ou Windows (WSL)

---

## Instalação

### Opção 1 — Clone direto (recomendado)

```bash
git clone https://github.com/jonatascosta-create/seedance-claude-skill ~/.claude/skills/seedance
```

### Opção 2 — Clone + symlink (mantém o repo separado pra `git pull`)

```bash
# Clona em qualquer lugar
git clone https://github.com/jonatascosta-create/seedance-claude-skill ~/dev/seedance-claude-skill

# Cria symlink no diretório de skills do Claude Code
ln -s ~/dev/seedance-claude-skill ~/.claude/skills/seedance
```

### Opção 3 — Download manual

1. Baixe o `SKILL.md` deste repo
2. Crie a pasta `~/.claude/skills/seedance/`
3. Cole o `SKILL.md` lá dentro

---

## Verificar instalação

Abra o Claude Code em qualquer projeto e digite:

```
/seedance
```

Se o skill aparecer na lista, tá funcionando.

---

## Uso rápido

### Modo Vibe — briefing inline (rápido)

```
/seedance vibe 15s, 21:9, thriller noir, detetive fumando em rua chuvosa inspirado em Michael Mann, arco frio→quente
```

→ Gera direto sem perguntar. 5 beats default, Mann, color_arc=frio→quente.

### Modo Vibe — briefing parcial

```
/seedance vibe uma batalha
```

→ Pergunta em bloco os campos CORE faltantes (duração, formato, mood, descrição).

### Modo Director — guiado passo-a-passo

```
/seedance director
```

→ Inicia fluxo de 15 etapas, uma pergunta por vez, com tabelas numeradas:

```
🎬 PASSO 1/15 — DURAÇÃO
┌───┬──────────┬─────────────────────────────────┐
│ # │ Valor    │ Quando usar                     │
├───┼──────────┼─────────────────────────────────┤
│ 1 │ 5s       │ Teaser, hook rápido             │
│ 2 │ 10s ★    │ Padrão — curva narrativa ideal  │
│ 3 │ 15s      │ Narrativa completa, 7 beats     │
│ 4 │ Custom   │ Digite: "custom: 8s"            │
└───┴──────────┴─────────────────────────────────┘
```

Você responde com o número (ou pressiona Enter pro ★). Ao final: summary card de confirmação → gera prompt → salva `.md` → **abre no Cursor**.

### Modo Director — com pré-preenchimento

```
/seedance director 15s thriller
```

→ Detecta que duração e gênero foram dados. Pula esses passos e começa no próximo faltante.

### Escolha de modo

```
/seedance
```

→ Responde com tabela pra você escolher Vibe ou Director.

---

## O que você recebe

**1. JSON chinês** (bloco copiável):

```json
{
  "prompt": {
    "title": "...",
    "style": "... + 2-3 cinematographic terms",
    "aspect_ratio": "21:9",
    "duration": "15s",
    "beats": [
      { "beat": 1, "time": "0s–3s", "action": "前景...中景...背景...导演妙笔...", "sfx": "..." }
    ],
    "negative_prompt": "模糊, 变形, 卡通, CGI感, 鬼影"
  }
}
```

**2. Tabela PT-BR** com composição em camadas e sacada de diretor por beat.

**3. Quatro arcos** narrativos:
- 🎨 Arco cromático (progressão de temperatura)
- 🎵 Arco da trilha (curva musical)
- 📐 Arco de perspectiva (escalas/ângulos)
- 💡 Tese visual (o que o filme está dizendo)

**4. Arquivo salvo** automaticamente em `thoughts/prompts/YYYY-MM-DD_HH-MM_titulo.md` do projeto atual — e aberto no Cursor.

**Nota sobre o JSON dentro do `.md`:** o JSON é salvo **em texto corrido (sem crases markdown)** pra você copiar e colar direto no Runway ou outro prompter. No chat, o skill mostra em bloco de código pra legibilidade.

---

## Onde colar o output

| Plataforma | Como usar |
|------------|-----------|
| **Seedance 2.0 / Jimeng / Doubao** | Cole o JSON chinês direto no campo de prompt |
| **Runway Gen-3** | Cole o JSON e anexe as imagens de referência na ordem dos `@Image1..9` slots |
| **Kling / Veo / Pika** | Não recomendado — este skill é otimizado para Seedance. Use outros prompters. |

---

## Princípios cinematográficos aplicados

O skill segue regras não-negociáveis do padrão Mann/Scott/Lubezki:

1. **Composição em camadas** — cada beat tem foreground + subject + background explícitos
2. **Contraste rítmico** — cada beat contrasta com o anterior (macro↔épico, silêncio↔explosão, frio↔quente)
3. **Sacada de diretor** por beat — algo que eleva a imagem (sombra que chega antes do corpo, reflexo que conta história paralela, frame-within-frame)
4. **2-3 termos cinematográficos** por prompt (anamorphic, dolly-in, rack focus, steadicam, dutch angle) — funciona como escudo de filtro
5. **Tabela de substituição** automática (violência vira linguagem cinematográfica neutra)
6. **Filter shield** — rostos em silhueta/costas em cenas sensíveis
7. **Temporal math** — `duration / beats` = segundos por beat calculados precisamente

---

## Compatibilidade

- ✅ Claude Code (CLI + Desktop + IDE extensions)
- ✅ Cursor com Claude Code habilitado
- ✅ Qualquer modelo Claude Opus 4.x ou Sonnet 4.x
- ⚠️ Haiku: funciona mas qualidade inferior (skill foi calibrado pra Opus)

---

## Customização

Edite `~/.claude/skills/seedance/SKILL.md` direto. Mudanças entram em vigor na próxima vez que você digitar `/seedance`.

Seções úteis pra customizar:
- **Defaults** (duração, formato padrão)
- **Tabela de substituição** (adicionar termos do seu domínio)
- **Estilo de saída** (trocar tabela por outro formato)

---

## Contribuindo

Issues e PRs bem-vindos. Ideias pra próxima versão:
- Mais beat-arcs nomeados (além dos 11 atuais)
- Expansão da lexicon cinematográfica
- Suporte a batch de prompts (múltiplos em uma só execução)
- Export direto pra API do Runway

---

## Licença

MIT — veja [LICENSE](LICENSE).

---

## Créditos

- **Sistema original:** [Redpine Prompt](https://redpine.co) — app de prompts cinematográficos
- **Agente base:** `cine-diretor` V3 da squad Cine
- **Padrão estético:** Michael Mann, Ridley Scott, Emmanuel Lubezki, Dion Beebe, Roger Deakins
- **Format de saída:** Seedance 2.0 / ByteDance Jimeng
