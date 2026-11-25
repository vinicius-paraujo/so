# ADR-0006 — Modelo da simulação: laço comum e uma classe por algoritmo de substituição

- **Status:** Aceita
- **Data:** 2026-11-16
- **Relacionada a:** [ADR-0001](ADR-0001-fidelidade-ao-material-teorico.md), [ADR-0002](ADR-0002-linguagem-e-ferramentas.md)

## 1. Problema

Os sete algoritmos fazem quase todo o trabalho da mesma forma. Para cada referência do `.trace`:

1. verificar se a página já está em algum frame (acerto) ou não (falta de página);
2. na falta, ocupar o menor frame livre ou, sem frame livre, expulsar uma página (evicção);
3. contar faltas, evicções e escritas em disco;
4. produzir a linha do `--verbose`.

O que muda entre eles é **apenas a escolha da vítima** e quais informações ela consulta. Se cada
algoritmo fosse um simulador completo, a contagem de métricas, o desempate comum e o verbose
estariam escritos sete vezes, com sete chances de divergir.

## 2. Fontes consultadas

| Fonte | O que diz |
|---|---|
| Enunciado, "Regras de Simulação" | Regras de cada algoritmo, desempate comum por carga mais antiga, menor frame livre, métricas. |
| Slides "Paginação" | FIFO, Ótimo, LRU; aproximações do LRU (relógio, NRU com bits R e M, envelhecimento); anomalia de Belady. |
| Maziero, §17.3 (pp. 211–217) | FIFO, Ótimo, LRU, com a mesma cadeia de Silberschatz (FIFO 15, Ótimo 9, LRU 12 faltas com 3 quadros). |
| Maziero, §17.4.1 (p. 218) | Segunda chance implementada como "lista circular" com ponteiro que zera bits R ao avançar; a nova página entra com R = 1. |
| Maziero, §17.4.2 (pp. 218–219) | NRU com quatro níveis por (R, M): 00, 01, 10, 11; substitui primeiro o nível mais baixo. Equivale à classe 2·R + M do enunciado. |
| Maziero, §14.7.2 | Bit de modificação (*dirty*): página modificada precisa ser gravada na área de troca antes de ser substituída — origem da métrica "escritas em disco". |
| LFU e MFU | Não aparecem no Maziero nem nos slides; vêm de Silberschatz et al., referência citada no enunciado. Valem as regras do enunciado. |

## 3. Decisão

### 3.1 Estruturas

```java
class Referencia {
    int pagina;
    boolean escrita;           // true para "W"
}

class Frame {
    Integer pagina;            // null = vazio
    int cargaEm;               // número da referência em que a página entrou
    int ultimoAcesso;          // número da última referência à página
    int contadorAcessos;       // acessos desde a carga (zera na evicção)
    boolean bitR;              // referência
    boolean bitM;              // modificação
}

interface AlgoritmoSubstituicao {
    int escolheVitima(Frame[] frames, int passoAtual);   // devolve o índice do frame
}

class FIFO  implements AlgoritmoSubstituicao { ... }   // menor cargaEm
class LRU   implements AlgoritmoSubstituicao { ... }   // menor ultimoAcesso
class Otimo implements AlgoritmoSubstituicao { ... }   // próximo uso mais distante; sem uso futuro primeiro
class Clock implements AlgoritmoSubstituicao { ... }   // ponteiro circular, zera R até achar R = 0
class NRU   implements AlgoritmoSubstituicao { ... }   // menor classe 2·R + M
class LFU   implements AlgoritmoSubstituicao { ... }   // menor contadorAcessos
class MFU   implements AlgoritmoSubstituicao { ... }   // maior contadorAcessos
```

O tempo é **lógico** (número da referência), não o relógio do computador.

### 3.2 Laço comum (`Simulador.simula`)

Para cada referência *i*:

1. **Acerto:** atualiza `ultimoAcesso = i`, `contadorAcessos++`, `bitR = true` e, se escrita,
   `bitM = true`.
2. **Falta:** ocupa o menor frame livre; sem frame livre, chama `algoritmo.escolheVitima`, conta
   uma evicção e, se `bitM` da vítima for verdadeiro, uma escrita em disco. Carrega a nova página
   com `cargaEm = ultimoAcesso = i`, `contadorAcessos = 1`, `bitR = true`, `bitM = escrita`.
3. **NRU:** se *i* for múltiplo de 4, zera o `bitR` de todos os frames.
4. Registra um `Passo` (usado pelo verbose e pelo bônus visual).

### 3.3 Exemplo: passo 4 do verbose do enunciado (LRU, 3 frames)

Frames `[7 0 1]`, chega a página 2: falta sem frame livre. O laço chama `LRU.escolheVitima`. Os
`ultimoAcesso` são 7→1, 0→2, 1→3; o menor é o da página 7, que sai:
`4 ref=2 [2 0 1] falta (remove 7)`. Com `FIFO`, a pergunta seria a mesma, mas a resposta olharia
`cargaEm`.

### 3.4 Por que os atributos ficam no frame

O enunciado diz que o contador do LFU/MFU "volta a zero quando ela é removida". No
`silberschatz2001.trace`, a página 7 sai no passo 4 e volta no passo 18: entra em um frame com os
atributos reiniciados, sem herdar o histórico anterior. Guardar os atributos no frame torna essa
regra automática.

### 3.5 Padrão de projeto

O par `Simulador` + `AlgoritmoSubstituicao` corresponde ao padrão *Strategy*: o laço é fixo e a
regra de escolha é trocada pelo algoritmo informado em `--algo`. Cada regra fica isolada numa
classe pequena, que pode ser apontada na arguição.

## 4. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Um simulador completo por algoritmo | Duplica métricas, desempate e verbose em sete lugares. |
| Estruturas clássicas por algoritmo (fila no FIFO, pilha no LRU, lista circular no Clock) | Fiéis às descrições do livro, mas dificultam aplicar o mesmo desempate por carga mais antiga em todos; com poucos frames, percorrer o vetor é suficiente e mais simples de explicar. O Clock mantém o conceito de lista circular por meio do ponteiro sobre o vetor de frames. |
| Tabela de páginas global indexada pelo número da página | Exigiria limpar os atributos manualmente na evicção; os números de página não têm limite definido no enunciado. |
