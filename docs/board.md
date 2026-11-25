# Board Kanban — Atividade Prática: Paginação (Prof. Fernando Trinta)

Fluxo: **Backlog → A fazer → Em andamento → Revisão → Concluído**.
Para mover um cartão, recorte a linha da tabela de origem e cole na tabela de destino.
Um cartão só entra em **A fazer** quando todas as suas dependências estão em **Concluído** e as
ADRs de que ele depende estão aceitas.

**Pendências:** as hipóteses de `architecture.md` §1.6 são confirmadas nos cartões que as implementam. Os critérios de interface (RNF01) bloqueiam apenas o bônus visual (P-10).

## Backlog

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|
| P-04 | Laço comum, métricas e FIFO | P-02, P-03, ADR-0006 | — | FIFO reproduz os oráculos; evicções = faltas − frames; escritas contadas só com M = 1. |
| P-05 | LRU e Ótimo | P-04 | — | Oráculos de LRU e Ótimo passando, incluindo o conjunto residente final do LRU. |
| P-06 | Clock | P-04 | — | Oráculos de Clock passando; ponteiro só se move em evicções. |
| P-07 | NRU | P-04 | — | Oráculos de NRU passando nos três traços; zeragem do R a cada 4 referências. |
| P-08 | LFU e MFU | P-04 | — | Oráculos de LFU e MFU passando; contador zerado na evicção. |
| P-09 | Saída: resumo e verbose | P-05, P-06, P-07, P-08 | — | Resumo e 10 primeiras linhas do verbose idênticos ao enunciado. |
| P-10 | Bônus: simulação visual | P-09, ADR-0007 | — | Itens do bônus do enunciado atendidos com qualquer algoritmo, frames e traço. |
| P-11 | README | P-09 | — | Qualquer pessoa compila e executa os três traços seguindo só o README. |
| P-12 | Documento de decisões e análise de Belady | P-09 | — | Estruturas, módulos e padrões descritos; tabela de todos os algoritmos nos dois traços; algoritmos com anomalia identificados e explicados; sem referências a `docs/`. |
| P-13 | Revisão aprofundada do código | P-11, P-12 | — | Código revisado linha a linha pela equipe; verificação da ADR-0004 sem resultados. |
| P-14 | Roteiro da apresentação | P-13 | — | Falas divididas; cada decisão explicável sem consultar `docs/`. |

## A fazer

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|
| P-02 | Traços e testes dos oráculos | P-01 | — | Três `.trace` do enunciado criados; casos de `architecture.md` §4.1 e §4.2 escritos como testes e falhando. |
| P-03 | Leitura de argumentos e do `.trace` | P-01 | — | Formatos `7`, `0R`, `1W`, comentários e linhas vazias; erros de argumento e de linha tratados. |

## Em andamento

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|

## Revisão

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|

## Concluído

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|
| P-00 | Documentação inicial (board, arquitetura, ADRs) | — | Marcos | Documentos em `docs/`. |
| P-01 | Estrutura do projeto, construção e testes | ADR-0002 | Marcos | Um comando compila; um comando executa a suíte (vazia); código em `src/main/java`, testes em `src/test/java`, `traces/` e `docs/` à parte. |
