# ADR-0001 — Fidelidade ao material teórico do professor

- **Status:** Aceita
- **Data:** 2026-11-16
- **Contexto da disciplina:** CK0234 — Sistemas Operacionais (UFC, 2026.2) — Atividade Prática:
  Alocação de Memória, Prof. Fernando Antônio Mota Trinta

## 1. Contexto

O trabalho pede um simulador de gerência de memória com os algoritmos First Fit, Best Fit, Worst
Fit e Next Fit, desfragmentação (`defrag`) em três modos e uma simulação visual passo a passo.
Existem implementações de mercado mais compactas (alocadores reais, bibliotecas de interface), mas
o objetivo avaliativo é demonstrar o funcionamento interno descrito em aula, de forma que o
professor reconheça cada conceito no código e a equipe consiga defendê-lo oralmente.

| Material | Autor | O que estabelece |
|---|---|---|
| `Atividade Prática - Alocação de Memória.pdf` | Fernando | Especificação normativa: comandos, regras de desempate, Next Fit, `defrag`, saída esperada, requisitos da simulação visual, entregáveis. |
| Slides "Alocação de Memória" | Fernando | Vocabulário: alocador, áreas livres/ocupadas, buraco, fragmentação externa e interna, First/Best/Worst/Next-Fit, compactação, coalescência; registro de áreas livres em listas, árvores ou mapas de bits. |
| Slides "Gerenciamento de Memória — Endereços Lógicos e Físicos", "Organização de Processos em Memória", "Tópicos Extras de Gestão de Memória" | Fernando | Contexto de memória física, partições, alocação contígua. |
| Livro-texto (Maziero, *SO: Conceitos e Mecanismos*) | — | Citado no enunciado como referência das operações do alocador. |

## 2. Decisão

Ordem de prioridade para toda decisão técnica:

1. **O enunciado** é a especificação normativa. Suas regras de desempate, formatos de saída e
   exemplos numéricos são reproduzidos exatamente; nada que ele exija é omitido.
2. **Os slides do professor** fornecem o vocabulário e a justificativa teórica. Termos acadêmicos
   do material (First Fit, Best Fit, Worst Fit, Next Fit, buraco, coalescência, fragmentação
   externa) são usados como aparecem nele.
3. **O livro de Maziero** complementa os slides quando o enunciado o cita.
4. **Práticas de mercado** só são adotadas quando não conflitam com os itens anteriores e
   tornam o código mais fácil de explicar.

Consequências imediatas:

- Cada algoritmo de alocação mantém sua identidade no código (uma função por algoritmo), sem um
  mecanismo genérico que esconda a regra de escolha do buraco.
- Os exemplos numéricos do enunciado (sessão interativa, tabela de `defrag`, tabela do Next Fit)
  tornam-se casos de teste obrigatórios.
- Nenhum requisito ausente é inventado. Lacunas viram hipóteses registradas e são levadas ao
  responsável antes de serem implementadas.

## 3. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Implementar com um alocador real (por exemplo, `malloc` instrumentado) | Esconde a lista de buracos e a escolha do algoritmo, que são o objeto da avaliação. |
| Estrutura genérica de "estratégia" com algoritmos parametrizados | Reduz código, mas dificulta apontar no código a regra de cada algoritmo durante a arguição. |

## 4. Consequências

**Positivas:** rastreabilidade entre enunciado, slides, código e testes; defesa oral facilitada.

**Negativas:** mais código do que uma solução de mercado; documentação a manter.
