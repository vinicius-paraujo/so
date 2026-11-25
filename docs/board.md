# Board Kanban — Atividade Prática: Alocação de Memória (Prof. Fernando Trinta)

Fluxo: **Backlog → A fazer → Em andamento → Revisão → Concluído**.
Para mover um cartão, recorte a linha da tabela de origem e cole na tabela de destino.
Um cartão só entra em **A fazer** quando todas as suas dependências estão em **Concluído** e as
ADRs de que ele depende estão aceitas.

**Pendências:** a lacuna H03 (`architecture.md` §1.4) fica em aberto até a resposta do professor e só bloqueia o `show` (A-08); as demais hipóteses são confirmadas nos cartões que as implementam. Os critérios de interface (RNF01) bloqueiam a simulação visual (A-10).

## Backlog

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|
| A-03 | Modelo da memória e `init` | A-02, ADR-0006 | — | `MEMORIA`/`BLOCO` implementados; invariantes verificados em teste. |
| A-04 | First Fit, Best Fit e Worst Fit | A-03 | — | Testes de escolha, desempate, ids e erro sem espaço passando. |
| A-05 | Next Fit | A-03 | — | Tabela do Next Fit do enunciado reproduzida passo a passo, incluindo ponteiro. |
| A-06 | Liberação (`freeid`, `freeaddr`) e coalescência | A-03 | — | Erros não alteram a memória; nunca dois livres adjacentes. |
| A-07 | `defrag` INIT, END e OPT | A-04, A-06 | — | Tabela de `defrag` do enunciado reproduzida; ponteiro do Next Fit zerado. |
| A-08 | `show` e `stats` | A-04, A-06 | — | Saída idêntica à sessão de exemplo do enunciado. |
| A-09 | CLI e interpretador de comandos | A-05, A-07, A-08, ADR-0007 | — | Sessão interativa e execução de roteiro produzem a saída esperada; erros de comando tratados. |
| A-10 | Simulação visual | A-09, ADR-0008 | — | Todos os itens de RF18 e RF19 atendidos com o roteiro `exemplo-nextfit.cmd`; Voltar restaura ids e ponteiro. |
| A-11 | Roteiros de demonstração | A-09 | — | Roteiros do enunciado e um roteiro comparativo dos quatro algoritmos. |
| A-12 | README | A-10 | — | Qualquer pessoa compila, executa a CLI, carrega um roteiro e abre a simulação visual seguindo só o README. |
| A-13 | Documento de decisões de implementação | A-10 | — | Estruturas de dados (incluindo o bloco), classes/módulos e padrões descritos, sem referências a `docs/`. |
| A-14 | Revisão aprofundada do código | A-12, A-13 | — | Código revisado linha a linha pela equipe; verificação da ADR-0004 sem resultados. |
| A-15 | Roteiro da apresentação | A-14 | — | Falas divididas; cada decisão explicável sem consultar `docs/`. |

## A fazer

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|
| A-02 | Testes dos oráculos do enunciado | A-01 | — | Todos os casos de `architecture.md` §4 escritos como testes e falhando. |

## Em andamento

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|

## Revisão

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|

## Concluído

| ID | Título | Dependências | Responsável | Critério de pronto |
|---|---|---|---|---|
| A-00 | Documentação inicial (board, arquitetura, ADRs) | — | Marcos | Documentos em `docs/`. |
| A-01 | Estrutura do projeto, construção e testes | ADR-0002 | Marcos | Um comando compila; um comando executa a suíte de testes (vazia); código em `src/main/java`, testes em `src/test/java`, `docs/` à parte. |
