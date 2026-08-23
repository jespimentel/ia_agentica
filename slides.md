---
marp: true

---

# Prompts, skills e agentes de IA para a Promotoria de Justiça

## José Eduardo de Souza Pimentel

2026

---

# Agenda

- Introdução à IA Agêntica
- Engenharia de prompt
- Agentes declarativos
- Skills e MCP
- Agentes de execução
- Conclusão

---

# Introdução à IA Agêntica

---

**Aviso nº 009/2025-CGMP**

Considerações

![bg fit](img/trafico-gpt.png)

---

# Engenharia de prompt

---

## Elementos estruturais de um bom prompt

| # | Elemento | O que define | Exemplo |
|---|----------|--------------|---------|
| 1 | **Papel/contexto** | Persona e cenário da tarefa | *"Você é um promotor de justiça criminal e está sendo intimado da sentença fornecida em PDF."* |
| 2 | **Restrições e tom** | Limites, formalidade, exclusões | *"Máximo de 500 palavras. Linguagem técnico-jurídica. Não cite jurisprudência."* |
| 3 | **Exemplos (few-shot)** | Um ou dois modelos da saída desejada | Peça análoga já aprovada, parágrafo-modelo, estrutura de denúncia anterior |

---

| # | Elemento | O que define | Exemplo |
|---|----------|--------------|---------|
| 4 | **Insumo factual** | O material sobre o qual o modelo trabalhará | Texto legal, peças de um inquérito, acórdão, documento anexado etc. |
| 5 | **Instrução unívoca** | Rol das tarefas que o modelo deve executar ("analise", "compare", "liste", "reescreva" etc.) | *"Resuma o documento acima de forma estruturada, citando as infrações criminais cometidas."* |
| 6 | **Formato de saída** | Estrutura esperada do resultado | *"Responda em Markdown com as seguintes seções: Fatos, Fundamentação e Requerimentos."* |

---

## Marcação com Markdown, XML e Placeholders

**Elementos do Markdown**

| Marcação | Descrição no Prompt | Exemplo no Prompt |
|----------|--------------------|--------------------|
| `#` | Título | `# Analisador de Inquérito Policial` |
| `##` | Subtítulo (bom para dividir por seções lógicas) | `## Instruções` |
| `**Negrito**` | Destaca termos-chave para o LLM | `**Não inclua opiniões**` |
| `- Item` | Lista não ordenada para enumerar instruções ou requisitos | `- Analise os fatos` |
| `---` | Linha horizontal para separar seções | `---` |

---

**XML**

- Delimita blocos de maneira inequívoca (`<instrucao></instrucao>`; `<contexto></contexto>`), para que o modelo não confunda dados com instruções
- Mitiga a ambiguidade semântica do Markdown em prompts longos

**Exemplo:**

```xml
<contexto>
Réu preso em flagrante por tráfico de drogas com 500g de cocaína, conforme fls. 12.
Condenado em 1ª instância; defesa apelou (fls. 123).
Razões da apelação a fls. 210/215, com o seguinte conteúdo:
"Pela r. Sentença de fls. 123, o apelante FULANO DE TAL foi condenado ..."
</contexto>
<instrucao>
Liste os pedidos contidos na apelação defensiva fornecida no contexto.
</instrucao>
```

---

**Placeholders**
- Transformam o prompt em template reutilizável (`{{nome_do_réu}}`, `[dia da semana seguinte]`)
- Facilitam a automação com scripts.

**Exemplo:**

```text
Elabore uma certidão de tempestividade para o recurso interposto por {{NOME_RECORRENTE}},
protocolado em {{DATA_PROTOCOLO}}, considerando o prazo final em {{DATA_LIMITE}}.
```

---

## Técnicas de prompting

| Cenário | Técnica recomendada |
|---|---|
| Tarefa genérica e direta | Zero-shot |
| Saída com formato rígido (petição, ofício) | Few-shot |
| Análise jurídica com múltiplos critérios | Few-shot + CoT |
| Cálculo de prescrição, verificação de antecedentes e reincidência | CoT obrigatório |

---

## Mão na massa


---

# Agentes declarativos

---

Copilot Premium encontra a solução para o problema na peça de um colega...

![](img/copilot-premium.png)

---

# Skills e MCP

- Uma skill é, no mínimo, uma pasta com um arquivo obrigatório: o SKILL.md.
- Frontmatter (YAML): os campos name e description ficam pré-carregados e servem de vitrine para o modelo escolher qual skill acionar.
- `description`: o gatilho. Diz o que a skill faz e quando usá-la, com expressões, tipos de arquivo e tipos de tarefa que sinalizam a correspondência com o pedido.
- Corpo (em Markdown): as instruções de execução. Procedimento passo a passo, regras de domínio e travas de segurança.
- Regra prática: manter o corpo da skill enxuto (abaixo de ~500 linhas), como um índice do método que remete aos arquivos auxiliares.

---

**Exemplo**

```markdown
elaborar-denuncia/            # sobe como .zip (ou botão "salvar") em Customize > Skills
├── SKILL.md                  # frontmatter (name + description/gatilho) + método
└── references/               # disclosure progressivo (lido sob demanda)
    ├── template.md           # estrutura da peça — lido só ao redigir
    └── exemplos.md           # exemplos de saída — só sem modelo do índice/colado

# Fora da skill (não é empacotado):
Conector Google Drive .......... ligado na UI de conectores (sem arquivo)
Google Drive (externo, via MCP)
└── modelos_denuncias/
    ├── indice.md
    └── *.md                   # modelos de peças
```

---

# Agentes de execução

---

# Conclusões


---

# Referências

- [ANTHROPIC. Agent Skills (docs)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

- [____. Claude Suport. Como criar habilidades personalizadas](https://support.claude.com/pt/articles/12512198-como-criar-habilidades-personalizadas)

- [____. Repositório público de skills](https://github.com/anthropics/skills)

- [____. The Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)

- [PIMENTEL, José Eduardo de Souza. A IA Generativa na Promotoria (apostila)](https://github.com/jespimentel/ia_gen_na_promotoria/blob/main/apostila/IA_Gen_Promotoria_Pimentel.pdf)

- [____. Minicurso de Bauru (site)](https://github.com/jespimentel/minicurso_bauru/blob/main/docs/index.md)
