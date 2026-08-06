# Dataset de avaliação — 90 perguntas e respostas (`qa_dataset_90.json`)

Conjunto ampliado de perguntas e respostas para avaliação de métricas (RAGAS) dos projetos RAG
irmãos deste repositório: `context-rag`, `graph-rag`, `hybrid-rag`, `knowledge-enhanced-rag`,
`memory-augmented-rag` e `self-rag`.

## Objetivo

Todos os 6 projetos acima usam, hoje, o mesmo conjunto fixo de **10 perguntas** (`test_queries` +
`ground_truths`, duas listas paralelas hardcoded em cada `main.py`), separadas por comentários de
categoria (`# FÁCEIS`, `# MÉDIAS`, `# DIFÍCEIS` — 4/3/3 perguntas). Dez perguntas produzem métricas
(`faithfulness`, `answer_relevancy`, `context_precision`, `context_recall`) com alta variância
estatística de uma execução para outra.

Este dataset expande esse conjunto para **90 perguntas (9x mais)**, mantendo as mesmas 3
categorias já usadas nos projetos, para permitir avaliações futuras mais conclusivas e
definitivas. Ele é um **artefato de dados independente**: não altera nenhum arquivo dos 6
projetos nem do `metrics-rags` (ver seção "Compatibilidade" abaixo).

## Origem do conteúdo

As 90 perguntas foram geradas a partir da leitura direta dos **7 livros em PDF** que compõem o
corpus de `context-rag/docs/` — corpus que é idêntico, byte a byte (verificado por checksum), ao
de `graph-rag/docs/`, e que cobre o mesmo domínio ("lógica de programação e algoritmos") presente,
em corpora menores, em `knowledge-enhanced-rag/data/apostilas/` e em `memory-augmented-rag/docs/`.

Por que só os livros de `context-rag`: é o corpus mais completo e didaticamente organizado do
domínio (7 títulos cobrindo do básico ao avançado), e é compartilhado com `graph-rag`. Duas notas
sobre os demais projetos, para contexto:
- `hybrid-rag/docs/` contém apenas um PDF de currículo acadêmico ("PPC Sistemas de Informação"),
  descrito no próprio README do projeto como "1 PDF de exemplo" — não é um livro de lógica de
  programação, por isso não foi usado como fonte.
- `self-rag/docs/` tem 10 PDFs, incluindo edições diferentes de 5 títulos também presentes em
  `context-rag` (mesmos autores, arquivos distintos) mais 5 livros exclusivos (CLRS, estruturas de
  dados). Não foi usado nesta rodada para manter uma fonte única e rastreável; pode ser incorporado
  em uma expansão futura do dataset.

**Nota sobre auditoria de tema**: dos 7 livros do corpus, 1 (*Conceitos de Linguagens de
Programação*, de Robert W. Sebesta) foi **removido como fonte** após uma auditoria de tema: embora
esteja fisicamente na pasta `docs/`, seu conteúdo trata de *design/implementação de linguagens de
programação* (vinculação de tipos estática/dinâmica, resolução de escopo estático/dinâmico) — uma
disciplina distinta de "lógica de programação", que é o raciocínio algorítmico de resolver
problemas com sequência, decisão, repetição e estruturas de dados. As 5 perguntas inicialmente
baseadas em Sebesta foram substituídas por perguntas equivalentes em dificuldade, mas ancoradas em
tabelas hash e registros (estruturas de dados heterogêneas) — tópicos de algoritmos/estruturas de
dados que os demais livros do corpus cobrem diretamente.

Livros efetivamente usados como fonte (nome usado no campo `source_book`) e quantidade de
perguntas geradas a partir de cada um:

| Livro (`source_book`) | Perguntas |
|---|---|
| Lógica de programação (André Luiz Villar Forbellone) | 51 |
| Entendendo Algoritmos (Aditya Y. Bhargava) | 21 |
| Fundamentos da programação de computadores (Ana Fernanda Gomes Ascencio et al.) | 9 |
| Introdução à programação com Python (Nilo Ney Coutinho Menezes) | 4 |
| Algoritmos Lógica para desenvolvimento de programação de computadores | 3 |
| Algoritmos e Programação de Computadores (Dilermando Junior, Gilberto Nakamiti) | 2 |
| **Total** | **90** |

A distribuição não é uniforme entre os 6 livros porque cada um cobre o domínio com profundidade
diferente: os livros introdutórios de lógica de programação (Forbellone, Ascencio et al.,
Nakamiti) concentram os temas de nível FACIL e MEDIA, enquanto os temas DIFICIL (recursividade,
busca/ordenação, complexidade, pilha/fila, tabelas hash, registros) vêm majoritariamente de
Bhargava ("Entendendo Algoritmos") e do livro "Algoritmos Lógica para desenvolvimento...", os dois
títulos do corpus que tratam desses tópicos com profundidade suficiente.

## Metodologia de criação

1. Leitura direta de capítulos/seções relevantes de cada PDF-fonte (não o livro inteiro), focando
   nos trechos que efetivamente cobrem os tópicos de cada categoria de dificuldade.
2. Redação das perguntas no mesmo tom e estilo das 10 perguntas originais dos projetos
   (`main.py:53-69` em `context-rag`): português coloquial-técnico, direto, com aberturas variadas
   ("De um jeito bem direto...", "Pra que serve...", "Qual é a diferença entre...").
3. Redação das respostas (`ground_truth`) **parafraseadas** a partir do conteúdo lido — nunca
   copiadas literalmente/verbatim dos livros — mantendo fidelidade factual ao que o livro afirma,
   com 2 a 5 frases por resposta (respostas de perguntas DIFICIL tendem a ser um pouco mais longas,
   por exigirem mais contexto).
4. Atribuição do campo `source_book` a cada par, permitindo rastrear a proveniência de cada
   pergunta/resposta até o livro de origem.
5. Verificação de que cada pergunta é respondível **somente** com o conteúdo dos livros-fonte
   (sem introduzir conhecimento externo ao corpus).
6. Checagem de unicidade: nenhuma pergunta se repete ou é uma reformulação trivial de outra dentro
   do dataset final.

## Critérios de categorização

As categorias reproduzem exatamente os nomes já usados nos 6 projetos (`FACIL`, `MEDIA`,
`DIFICIL` — sem acentuação no JSON, para evitar problemas de encoding), com o critério de
dificuldade inferido a partir do padrão implícito nas 10 perguntas originais:

- **FACIL** (30 perguntas): definição direta de um único conceito isolado — o que é um algoritmo,
  o que é uma variável, para que serve um comando específico, etc. Não exige relacionar dois ou
  mais conceitos entre si.
- **MEDIA** (30 perguntas): perguntas que envolvem a relação entre 2+ conceitos ou o uso concreto
  de uma estrutura de controle/dados — estruturas condicionais e de repetição, contadores e
  acumuladores, vetores e matrizes, manipulação de strings, aninhamento de laços.
- **DIFICIL** (30 perguntas): perguntas que exigem síntese, comparação entre abordagens diferentes,
  ou entendimento de um fluxo/algoritmo completo — subprogramas e passagem de parâmetros,
  recursividade, algoritmos de busca e ordenação, complexidade (Big-O), estruturas de dados (pilha,
  fila, tabela hash e registros).

A divisão entre categorias é de **30/30/30** (partes iguais), diferente da proporção 4/3/3 das 10
perguntas originais, para garantir volume estatístico equivalente nas três categorias ao calcular
métricas agregadas por nível de dificuldade.

## Schema do JSON

`qa_dataset_90.json` é um array de 90 objetos. Cada objeto tem exatamente estes campos:

| Campo | Tipo | Descrição |
|---|---|---|
| `id` | string | Identificador sequencial `Q001`–`Q090`, agrupado por categoria (FACIL primeiro, depois MEDIA, depois DIFICIL) — facilita leitura e fatiamento em lotes. |
| `category` | string | Uma de `FACIL`, `MEDIA`, `DIFICIL`. |
| `question` | string | A pergunta em português, no estilo das perguntas originais dos projetos. |
| `ground_truth` | string | Resposta de referência, parafraseada a partir do livro-fonte — usada como `ground_truth` em avaliações RAGAS (`context_recall`, `context_precision`) ou por similaridade semântica. |
| `source_book` | string | Nome do livro-fonte de onde o conteúdo foi extraído (ver tabela acima). |

Exemplo de item:

```json
{
  "id": "Q001",
  "category": "FACIL",
  "question": "O que significa 'lógica de programação' em palavras simples?",
  "ground_truth": "É o uso correto das leis do pensamento e de processos organizados de raciocínio aplicados à criação de programas de computador, com o objetivo de produzir soluções válidas e coerentes que resolvam com qualidade os problemas propostos.",
  "source_book": "Lógica de programação (André Luiz Villar Forbellone)"
}
```

## Compatibilidade e próximos passos

Este JSON é um **entregável de dados**, deliberadamente não integrado a nenhum código existente:

- Nenhum `main.py` dos 6 projetos RAG foi alterado — eles continuam usando suas 10 perguntas
  hardcoded originais.
- `metrics-rags/generate_rag_plots.py::validate_results()` hoje valida contagens fixas (300 linhas
  totais = 5 execuções × 10 perguntas × 6 projetos; 50 linhas por projeto; 10 linhas por execução;
  cada pergunta aparecendo exatamente 30 vezes). Passar a usar as 90 perguntas em qualquer um dos 6
  projetos, sem também atualizar essas constantes de validação no `metrics-rags`, quebra essa
  validação.

Uma futura adoção completa deste dataset (substituindo as 10 perguntas atuais nos 6 `main.py`)
exigiria, como trabalho separado:
1. Atualizar `test_queries`/`ground_truths` (ou substituí-los por uma leitura deste JSON) em cada
   `main.py`.
2. Recalcular as constantes de validação do `metrics-rags` (300 → 5 × 90 × 6 = 2700 linhas totais;
   50 → 450 linhas por projeto; 10 → 90 linhas por execução; 30 → 30 ocorrências por pergunta,
   inalterado).

Essa integração está fora do escopo deste entregável e é uma decisão a ser tomada separadamente.
