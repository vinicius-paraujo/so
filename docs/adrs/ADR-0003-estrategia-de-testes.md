# ADR-0003 — Estratégia de testes

- **Status:** Aceita
- **Data:** 2026-11-16
- **Relacionada a:** [ADR-0001](ADR-0001-fidelidade-ao-material-teorico.md)

## 1. Contexto

O enunciado fornece valores exatos de faltas para sete algoritmos em dois traços e dois tamanhos
de memória, a tabela completa do `escritas.trace`, o resumo de uma execução (LRU, 3 frames) e as 10
primeiras linhas do modo verbose. São oráculos objetivos.

## 2. Decisão

1. **Oráculos do enunciado primeiro**: todos os valores de referência viram testes automatizados
   antes da implementação de cada algoritmo.
2. **Oráculos derivados**, marcados como tal: os valores que o enunciado **não** dá (por exemplo,
   Clock, NRU, LFU e MFU no `belady.trace`; Ótimo, LFU e MFU no `escritas.trace`) foram calculados
   pela equipe aplicando as regras do enunciado e estão em `architecture.md` §4.2. Servem de
   regressão, mas um desacordo com eles deve ser investigado nas regras antes de se mudar o código.
3. **Testes de regras isoladas**: desempate por carga mais antiga em todos os algoritmos; Ótimo
   priorizando páginas sem acesso futuro; ponteiro do Clock que só se move em evicções; zeragem do
   R do NRU a cada 4 referências; contador do LFU/MFU zerado na evicção; escrita em disco só na
   evicção de página com M = 1.
4. **Testes de interface**: argumentos inválidos (`--frames 0`, algoritmo desconhecido, arquivo
   inexistente), linhas vazias e comentários no `.trace`, formato `7`, `0R`, `1W`.
5. **Testes de saída**: resumo e verbose comparados ao texto do enunciado.
6. Diretório de testes separado do código entregue, executado por um único comando; a ferramenta
   segue a linguagem da ADR-0002.

## 3. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Testar apenas o total de faltas | Não detecta erros de desempate que se compensam; o conjunto residente final e o verbose detectam. |
