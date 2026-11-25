# ADR-0005 — Nomenclatura em português

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Decisão

1. Identificadores do projeto (funções, variáveis, tipos, constantes, arquivos-fonte) em
   **português**, sem acentos. Comentários e mensagens em português.
2. Exceções:
   - **termos acadêmicos do material**, mantidos como o professor os escreve: `FirstFit`,
     `BestFit`, `WorstFit`, `NextFit`;
   - **a interface exigida pelo enunciado**, que é contrato com o professor e não se traduz:
     comandos `init`, `alloc`, `freeid`, `freeaddr`, `show`, `stats`, `defrag`; argumentos
     `first`, `best`, `worst`, `next`, `init`, `end`, `opt`; mensagens e rótulos de saída
     exatamente como no enunciado;
   - APIs da linguagem e de bibliotecas;
   - diretórios convencionais (`src/`, `tests/`, `docs/`).
3. Correspondência sugerida com a tabela "Métodos a Implementar" do enunciado:

   | Enunciado | Projeto |
   |---|---|
   | `init(int tamanho)` | `Inicializa(tamanho)` |
   | `alloc(int tamanho, FitAlg alg)` | `Aloca(tamanho, algoritmo)` |
   | `free_id(int id)` | `LiberaPorId(id)` |
   | `free_addr(int endereco)` | `LiberaPorEndereco(endereco)` |
   | `show()` | `MostraMapa()` |
   | `stats()` | `MostraEstatisticas()` |
   | `defrag(Option option)` | `Desfragmenta(opcao)` |

   A forma exata (maiúsculas, prefixos) segue o padrão da linguagem escolhida na ADR-0002.

## 2. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Manter os nomes em inglês da tabela do enunciado | Mistura idiomas com o restante do código; a tabela acima preserva a correspondência. |
| Traduzir também os comandos da CLI | Quebraria o contrato do enunciado; roteiros do professor não funcionariam. |
