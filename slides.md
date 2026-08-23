---
marp: true

---

# Prompts, Skills e Agentes de IA para a Promotoria de Justiça

## José Eduardo de Souza Pimentel

[Blog](https://jespimentel.blogspot.com/) | [GitHub](https://github.com/jespimentel) | [YouTube](https://www.youtube.com/@jespimentel)

v. 2026-09

---

![bg right](img/qrcode.png)
# Agenda

- Introdução à IA Agêntica
- Engenharia de prompt
- Agentes declarativos do Copilot
- Skills e MCP (conectores e plugins)
- Agentes de execução e Harness
- Dicas e conclusões

---

# Introdução à IA Agêntica

---

![bg right](img/attention.jpg)

## Evolução dos LLM

- **2017** -> Transformer + _self-attention_ (paralelização massiva)
- **2018 a 2021** -> Escala + modelos de fundação (GPT e Bert) -> Capacidades emergentes
- **2022 a 2023** -> Multimodalidade + expansão da tecnologia
- **2024 ~** -> Raciocínio + Agentes (+ Eficiência)

---

**Acesso a Ferramentas por LLM (Tool Use)**

- Acesso a dados atualizados, realização de cálculos ou interação com outros sistemas
- Tecnologia agnóstica (JSON Schema)
- Fluxo de comunicação:
    - **1. Definição**: Lista de ferramentas declaradas em `JSON Schema`
    - **2. Decisão**: O LLM reconhece a necessidade do uso e responde com um pedido de execução da ferramenta (`tool_calls` com argumentos)
    - **3. Execução Externa**: O runtime/harness intercepta o JSON e executa a função real (API, banco, script)
    - **4. Síntese**: O resultado é inserido no contexto para que a LLM gere a resposta

---

![bg fit](img/agentes.jpg)

---

## Aviso nº 009/2025-CGMP

- Visão geral sobre a regulamentação do uso da IA
- O que devemos restringir?
- Opinião sincera sobre o tema

---

![bg right fit](img/trafico-gpt.png)

> Em processo sob nossa análise, a ré usou o ChatGPT para gerar o "cardápio" com os tipos de drogas que comercializava

---

# Engenharia de prompt

---

## Elementos estruturais do prompt

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

## Markdown, XML e Placeholders

**Markdown**

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

- [Prompt para MPU](prompts/mpu.md)
- [Prompt para extrair teses (Ex. de few-shot prompting)](prompts/extrator.md)
- CoT e *Tool Use* em ação

---

# Agentes declarativos do Copilot

---
**Conhecimento**

> Recuperação probabilística por RAG

- **No prompt**: o que se aplica sempre (regras, template, restrições)
- **No conhecimento**: referência estável, consultada conforme o caso (ex.: catálogo de modelos, manual de regras).

---

**Copilot Premium**

![](img/copilot-premium.png)

---

## Mão na massa

- [Contrarrazões com base de conhecimento](prompts/contrarrazoes.md)
- [Criando a Valentina](prompts/valentina.md)

---

# Skills e MCP (conectores e plugins)

---

**Skills**

- Quando usar?
- Tecnologia agnóstica (Padrão aberto)
- O que é?
    - No mínimo: uma pasta com o arquivo SKILL.md
    - _Frontamatter_ (`YAML`) pré-carregado:
        - `name`: nome da Skill
        - `description`: o que faz + gatilho
    - Corpo: instruções de execução
- Regra prática: < 500 linhas

---

**MCP (Model Context Protocol)**

- Quando usar?
    - Para conectar IAs a dados, ferramentas e APIs externas de forma padronizada
- Tecnologia agnóstica (Padrão aberto)
- O que é?
    - Arquitetura Cliente-Servidor via JSON-RPC 2.0 
    - 3 Primitivas principais expostas pelo servidor:
        - `Tools`: Funções executáveis pela IA
        - `Resources`: Dados e arquivos para contexto
        - `Prompts`: Templates de interação reutilizáveis
- Regra prática: 1 Servidor MCP = 1 Responsabilidade

---

**Exemplo:** ***Progressive Disclosure***

```markdown
elaborar-denuncia/            # sobe como .zip (ou botão "salvar") em Customize > Skills
├── SKILL.md                  # frontmatter (name + description/gatilho) + método
└── references/               # descoberta progressiva (lido sob demanda)
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

## Mão na massa

- [Examinando uma Skill](prompts/elaborar-denuncia.md)
- Criando uma Skill com MCP
- Compartilhamento de Skills

---

# Agentes de Execução e Harness

---

**Agente de Execução e Harness**

- Quando usar?
    - Para tarefas autônomas que exigem execução de código e controle de estado
- O que é?
    - **Agente**: O modelo (LLM) responsável pelo raciocínio, planejamento e tomada de decisão
    - **Harness**: O ambiente/runtime que envolve o agente para gerenciar a execução
- Regra prática: A IA decide o *quê* fazer; o Harness possibilita a execução do plano


---

## Mão na massa

- Pipeline de Alegações Finais
- Pipeline de OCR
- Extrair teses em lote

**Bônus**
- [Criando uma "Rotina" para concurseiros](prompts/concurso.md)

---

# Dicas e conclusões
- Estrutura de prompt não é estética, é semântica
- Divida tarefas complexas em subtarefas (agentes especializados). Isso vale para Skills também
- O que conta é o Harness (onde estão os nossos dados?)
- Teste no VS Code (extensões do Claude ou "Continue")

---

![bg fit](img/economia-tempo.jpg)
![bg fit](img/memory-caching.jpg)

---

# Obrigado!

## Referências

- [ANTHROPIC. Agent Skills (docs)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

- [____. Claude Suport. Como criar habilidades personalizadas](https://support.claude.com/pt/articles/12512198-como-criar-habilidades-personalizadas)

- [____. Repositório público de skills](https://github.com/anthropics/skills)

- [____. The Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)

- [PIMENTEL, José Eduardo de Souza. A IA Generativa na Promotoria (apostila)](https://github.com/jespimentel/ia_gen_na_promotoria/blob/main/apostila/IA_Gen_Promotoria_Pimentel.pdf)

- [____. Minicurso de Bauru (site)](https://github.com/jespimentel/minicurso_bauru/blob/main/docs/index.md)
