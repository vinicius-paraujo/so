# ADR-0003 — Estratégia de testes

- **Status:** Aceita
- **Data:** 2026-11-16
- **Relacionada a:** [ADR-0001](ADR-0001-fidelidade-ao-material-teorico.md), [ADR-0002](ADR-0002-linguagem-e-ferramentas.md)

## 1. Contexto

O enunciado traz exemplos com resultados exatos: a sessão interativa com mapa e estatísticas, a
tabela de `defrag` (INIT, END, OPT) e a tabela passo a passo do Next Fit. Esses exemplos são o
critério mais objetivo de que a interpretação das regras está correta. A simulação visual depende
de um estado reversível (Voltar, Reiniciar), que também precisa ser verificado.

## 2. Decisão

1. **Oráculos do enunciado primeiro.** Antes de implementar cada algoritmo, os exemplos do
   enunciado são escritos como testes automatizados e devem falhar. Só então o algoritmo é
   implementado.
2. **Diretório de testes separado** do código entregue, executado por um único comando.
3. **Níveis:**

   | Nível | Escopo | Exemplos de critério |
   |---|---|---|
   | Unitário | Cada algoritmo de alocação, liberação, coalescência, `defrag`, estatísticas | Buraco escolhido em cada algoritmo; desempate pelo menor endereço; ids nunca reaproveitados; alocação que falha não consome id nem move o ponteiro |
   | Saída | Formato exato de `show`, `stats` e mensagens de erro | Texto idêntico ao do enunciado (mapa `#`/`.`, linha de ids, "Uso efetivo: 25.00%") |
   | Roteiro | Execução de arquivo `.cmd` pela CLI e pelo motor da simulação visual | Mesmo roteiro produz o mesmo estado final nos dois; Voltar restaura exatamente o estado anterior |

4. A ferramenta de testes segue a linguagem escolhida na ADR-0002 (por exemplo, JUnit 5 em Java,
   ou a macro própria `VERIFICA` em C, como no trabalho anterior).
5. A parte gráfica da simulação visual não é testada por pixels; testa-se o **estado** que ela
   exibe (passo atual, buracos examinados, blocos movidos, posição do ponteiro).

## 3. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Só testes manuais pela CLI | Não detectam regressões nas regras de desempate e de `defrag`. |
| Testar a interface por capturas de tela | Frágil e caro; o estado exibido já é testável sem a interface. |
