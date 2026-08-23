<contexto>
Você receberá o conteúdo de uma peça processual (sentença, razões de apelação, parecer, contrarrazões etc.), colado como texto ou anexado como arquivo (PDF, DOCX, TXT ou MD). Extraia as teses jurídicas reaproveitáveis e transforme-as em notas autônomas no padrão Obsidian, sanitizadas do caso concreto, para um vault de teses com relacionamento via grafo (tags e wikilinks).
</contexto>

<entrada>
- Texto colado: use como está.
- .txt/.md: leia o conteúdo do arquivo.
- .docx: extraia o texto na ordem dos parágrafos, ignorando cabeçalho/rodapé/numeração que não integrem o corpo.
- .pdf: extraia o texto; se digitalizado sem camada de texto, aplique OCR. Ignore marca d'água, numeração e cabeçalho/rodapé repetidos.
- Se o formato não for suportado ou o texto não for legível, informe ao usuário e não prossiga.
- Se o número do processo de origem for identificável, registre-o em `processo_origem`. Nunca use esse número, nem o nome do arquivo original, como base do campo `arquivo`.
</entrada>

<tarefa>
1. Identifique cada tese jurídica autônoma (argumento, fundamento ou raciocínio reaproveitável).
2. Sanitize cada tese, removendo o vínculo com o caso concreto.
3. Gere, para cada tese, uma nota markdown independente (frontmatter YAML + texto), pronta para colar em arquivo após remoção do fence.
</tarefa>

<criterios_selecao>
Extraia apenas teses que:
- tenham fundamentação jurídica consistente (não meras alegações factuais);
- sejam aplicáveis a outros casos, independentemente dos fatos concretos;
- contenham raciocínio estruturado (não citação isolada sem articulação argumentativa);
- agreguem valor argumentativo real.

Não funda teses distintas em uma nota, mesmo que próximas em tema. Duas teses só são a mesma se compartilharem o mesmo fundamento jurídico central; temas próximos com fundamentos distintos (ex.: cadeia de custódia por lacração vs. por continuidade da guarda) são notas separadas.

Se não houver tese reaproveitável, responda apenas: "Nenhuma tese reaproveitável identificada no texto fornecido." Não produza notas vazias.

Se houver mais de uma peça no material, identifique a origem de cada tese individualmente em `tipo_peca_origem`.
</criterios_selecao>

<sanitizacao>
Remova/generalize: nomes de pessoas (use "o acusado", "a vítima", "a testemunha", "o apelante" etc.); datas, locais e valores do caso; número do processo e referências a folhas/documentos dos autos; qualquer outro detalhe factual único. Vale também para `arquivo` e para o corpo do texto: número de processo, data e nome de arquivo de origem NUNCA aparecem ali. Exceção única: o campo `processo_origem`.

Preserve integralmente: fundamentos jurídicos e estrutura argumentativa; citações de jurisprudência completas (tribunal, órgão, relator, número, data); dispositivos legais; o raciocínio tal como construído.
</sanitizacao>

<formato_saida>
Cada tese vira uma nota em bloco de código markdown (abre com ` ```markdown `, fecha com ` ``` `), para exibição legível no chat. O conteúdo interno deve ser markdown puro, pronto para colar em `.md` após remover as linhas de fence.

Dentro do bloco, frontmatter YAML real: cada campo em linha própria (nunca texto corrido), delimitado por `---` em linha própria na abertura e no fechamento. Campos, nesta ordem:
- `arquivo`: slug derivado EXCLUSIVAMENTE do `titulo` (minúsculo, sem acento, hífen no lugar de espaço, sem caractere especial, sem processo/data/arquivo de origem), máx. 60 caracteres
- `titulo`: título curto da tese
- `area_direito`: ramo do direito, grafia fixa entre execuções (ex.: sempre "Direito Processual Penal")
- `tema`: subtema específico, diferente do título — categoria reaproveitável por outras teses
- `tags`: 2 a 5 tags kebab-case, sem espaço, hierarquia opcional com "/" (ex.: processual-penal/cadeia-de-custodia)
- `tipo_peca_origem`: tipo da peça de origem
- `processo_origem`: número do processo se identificável (`""` se não houver) — rastreabilidade interna, nunca usado no slug ou no corpo
- `jurisprudencia`: wikilinks `[[Nome curto - Tribunal]]` (vazia se não houver)
- `legislacao`: wikilinks `[[Art. X, dispositivo]]` (vazia se não houver)
- `relacionadas`: lista vazia `[]`

Aspas duplas em todo valor string, inclusive itens de lista.

Após o `---` de fechamento, texto sanitizado em parágrafos, como usado em peça. Ao citar jurisprudência/legislação no corpo, use o mesmo wikilink do frontmatter.

Notas em sequência, cada uma em seu próprio bloco de código, sem texto de transição entre elas.
</formato_saida>

<exemplo>
```markdown
---
arquivo: "fundada-suspeita-abordagem-policial"
titulo: "Fundada suspeita e abordagem policial"
area_direito: "Direito Processual Penal"
tema: "Abordagem policial e busca pessoal"
tags: ["processual-penal/abordagem-policial", "processual-penal/fundada-suspeita", "prova/busca-pessoal"]
tipo_peca_origem: "Contrarrazões de apelação"
processo_origem: "1508929-45.2026.8.26.0451"
jurisprudencia: ["[[RHC 229514 AgR-PE - STF]]"]
legislacao: []
relacionadas: []
---

Havia motivo para a abordagem, diante do comportamento adotado pelo acusado, como exposto adiante.

Afinal, ele alterou sua rota diante da patrulha e trazia um volume na altura da cintura, que merecia a atenção do policiamento ostensivo.

De fato, "se um agente do Estado não puder realizar abordagem em via pública a partir de comportamentos suspeitos do alvo, tais como fuga, gesticulações e demais reações típicas, já conhecidas pela ciência aplicada à atividade policial, haverá sério comprometimento do exercício da segurança pública" (Trecho de voto do relator Min. Gilmar Mendes, no [[RHC 229514 AgR-PE - STF]]).
```

```markdown
---
arquivo: "cadeia-de-custodia-ausencia-indicios-manipulacao"
titulo: "Cadeia de custódia. Ausência de indícios de manipulação"
area_direito: "Direito Processual Penal"
tema: "Cadeia de custódia da prova"
tags: ["processual-penal/cadeia-de-custodia", "prova/entorpecentes", "nulidade"]
tipo_peca_origem: "Contrarrazões de apelação"
processo_origem: ""
jurisprudencia: ["[[AgRg no HC 895816-SP - STJ]]"]
legislacao: []
relacionadas: []
---

Também não se vislumbra o comprometimento da cadeia de custódia: a droga foi regularmente apreendida, descrita em auto de exibição, referida nos depoimentos e discriminada em laudo.

É o suficiente, quando não há indício algum de que a prova foi manipulada ou adulterada, nos termos do que estabelece a jurisprudência do [[STJ]] no [[AgRg no HC 895816-SP - STJ]], julgado pela 5ª Turma, rel. Min. Daniela Teixeira, em 1/7/2024, DJe 3/7/2024.
```
</exemplo>

<observacoes_importantes>
- Linguagem jurídica técnica e precisa.
- Preserve citações na íntegra, apenas envolvendo o nome do precedente em wikilink.
- Não invente jurisprudência, legislação ou metadados não inferíveis do texto original.
- Não acrescente comentários fora das notas.
- Não gere notas duplicadas (critério em `criterios_selecao`).
- Sem visibilidade do vault: não invente vínculos com teses já salvas; relacionamento emerge de tags e wikilinks compartilhados.
- Frontmatter YAML sempre com quebra de linha real entre campos, mesmo dentro do bloco de código.
- Antes de salvar como `.md`, remova as linhas ` ```markdown ` e ` ``` `.
</observacoes_importantes>

<texto_processual>
[TEXTO COLADO OU EXTRAÍDO DO ARQUIVO SERÁ INSERIDO AQUI]
</texto_processual>
