# ADR-0005 — Nomenclatura em português

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Decisão

1. Identificadores do projeto (funções, variáveis, tipos, constantes, arquivos-fonte) em
   **português**, sem acentos. Comentários e mensagens em português.
2. Exceções:
   - **termos acadêmicos do material**, mantidos como o professor os escreve: `FIFO`, `LRU`,
     `Otimo`, `Clock` (Segunda Chance), `NRU`, `LFU`, `MFU`, `frame`, bits `R` e `M`;
   - **a interface exigida pelo enunciado**, que não se traduz: `--algo`, `--frames`, `--trace`,
     `--verbose`; valores `fifo`, `lru`, `opt`, `clock`, `nru`, `lfu`, `mfu`; formato `.trace`;
     rótulos de saída exatamente como no enunciado (`Faltas de página`, `Evicções`,
     `frame_ids`, `page_ids`, `falta`, `acerto`, `remove`);
   - APIs da linguagem e de bibliotecas; diretórios convencionais.
3. Exemplos de nomes do projeto: `Frame`, `Pagina`, `CargaEm`, `UltimoAcesso`,
   `ContadorAcessos`, `BitR`, `BitM`, `EscolheVitimaFIFO`, `EscolheVitimaLRU`, `FaltasDePagina`,
   `Eviccoes`, `EscritasEmDisco`, `PonteiroClock`.
