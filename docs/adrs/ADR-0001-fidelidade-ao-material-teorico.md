# ADR-0001 — Fidelidade ao material teórico do professor

- **Status:** Aceita
- **Data:** 2026-11-16
- **Contexto da disciplina:** CK0234 — Sistemas Operacionais (UFC, 2026.2) — Atividade Prática:
  Paginação (substituição de páginas), Prof. Fernando Antônio Mota Trinta

## 1. Contexto

O trabalho pede um simulador dos algoritmos clássicos de substituição de páginas (FIFO, LRU, Ótimo,
Segunda Chance/Clock, NRU, LFU e MFU), com métricas de faltas de página, evicções e escritas em
disco, e a análise da anomalia de Belady. O enunciado define regras de simulação detalhadas "para
que os resultados sejam comparáveis entre as equipes" e fornece valores de referência.

| Material | Autor | O que estabelece |
|---|---|---|
| `Atividade Prática - Paginação.pdf` | Fernando | Especificação normativa: interface `./pager`, formato `.trace`, regras de cada algoritmo, desempate, métricas, saída, modo verbose, traços e resultados esperados, bônus visual. |
| Slides "Paginação" | Fernando | Vocabulário e teoria: falta de página, cadeia de referências (a mesma de Silberschatz), FIFO, Ótimo, LRU, aproximações (relógio/segunda chance, NRU com bits R e M, envelhecimento, conjunto de trabalho, WSClock), anomalia de Belady, *thrashing*. |
| Slides "Gerenciamento de Memória", "Tópicos Extras de Gestão de Memória" | Fernando | Contexto de memória virtual, páginas e quadros. |

## 2. Decisão

1. **O enunciado** é a especificação normativa. As "Regras de Simulação" são seguidas literalmente,
   inclusive quando diferem de variantes de livros (por exemplo, a zeragem do bit R do NRU a cada 4
   referências e o contador do LFU/MFU que volta a zero na evicção).
2. **Os slides** fornecem o vocabulário e a justificativa teórica, usada nos comentários e no
   documento de decisões.
3. **Práticas de mercado** só quando não conflitam com os itens anteriores e tornam o código mais
   fácil de explicar.

Consequências imediatas:

- Cada algoritmo mantém sua regra de escolha da vítima em uma função própria, identificável no
  código.
- Todos os valores de referência do enunciado tornam-se testes obrigatórios.
- Nenhum traço fica fixo no código: o professor executará outros arquivos `.trace`.

## 3. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Uma estrutura genérica de "política" com pesos configuráveis | Esconde a regra de cada algoritmo; dificulta a arguição. |
| Usar variantes clássicas dos livros onde o enunciado é diferente | Os resultados deixariam de ser comparáveis aos de referência. |
