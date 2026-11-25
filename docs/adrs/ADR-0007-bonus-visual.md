# ADR-0007 — Bônus: simulação visual

- **Status:** Aceita
- **Data:** 2026-11-16
- **Relacionada a:** [ADR-0002](ADR-0002-linguagem-e-ferramentas.md), [ADR-0006](ADR-0006-modelo-da-simulacao.md)

## 1. Contexto

A visualização passo a passo é opcional, com pontuação "a critério do professor". Deve mostrar, a
cada referência, o estado de todos os frames; destacar acerto ou falta e, em falta, qual página
entrou e qual saiu; permitir avançar (e, de preferência, voltar) um passo por vez e executar tudo
de uma vez; funcionar com qualquer algoritmo, número de frames e `.trace` informados pelo usuário.

## 2. Decisão

1. **O bônus será feito**, em Swing (ADR-0002).
2. A visual **não simula**: chama o mesmo `Simulador` da linha de comando e percorre a lista de
   `Passo` que ele devolve. Voltar é recuar um índice nessa lista; executar tudo é ir ao último.
3. A interface deve ser **superior à do Trabalho 01** (requisito RNF01 de `architecture.md`).
4. Uso previsto na apresentação: mostrar a anomalia de Belady lado a lado (FIFO com 3 e com 4
   frames no `belady.trace`).

## 3. Consequências

- Custo moderado: a lista de passos já existe por causa do `--verbose`.
- A visual e a CLI nunca divergem, pois usam o mesmo núcleo.
