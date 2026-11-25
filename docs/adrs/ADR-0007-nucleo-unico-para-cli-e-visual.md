# ADR-0007 — Núcleo único para CLI e simulação visual, com histórico de estados

- **Status:** Aceita
- **Data:** 2026-11-16
- **Relacionada a:** [ADR-0006](ADR-0006-modelo-da-memoria.md), [ADR-0008](ADR-0008-tecnologia-da-simulacao-visual.md)

## 1. Problema

O enunciado pede **dois programas que precisam se comportar igual**:

- o simulador de linha de comando, com comandos digitados ou lidos de um roteiro;
- a simulação visual, que executa o mesmo roteiro passo a passo ("o mesmo roteiro pode ser
  executado na CLI").

Se cada um tivesse sua própria lógica de alocação, uma correção em um poderia ser esquecida no
outro, e os resultados divergiriam diante do professor. Além disso:

- o botão **Voltar** deve "restaurar exatamente o estado anterior, incluindo ids e o ponteiro do
  Next Fit", e o enunciado sugere "guardar uma cópia do estado a cada passo";
- a tela deve mostrar **o que mudou** no passo: bloco alocado ou liberado, buracos unidos, blocos
  movidos pelo `defrag`, buraco escolhido e, de preferência, os buracos examinados antes dele.

## 2. Decisão

### 2.1 Três camadas

```
  CLI (terminal)            Simulação visual (Swing)
        │                            │
        └──────────┬─────────────────┘
                   ▼
        InterpretadorComandos        "alloc 8 next" → memoria.aloca(8, NEXT_FIT)
                   ▼
        Núcleo: Memoria, Bloco,      única implementação das regras;
        algoritmos, desfragmentação  não lê teclado nem desenha
```

| Camada | Responsabilidade | Quem a chama |
|---|---|---|
| Núcleo (`Memoria`, `Bloco`, algoritmos) | Todas as regras do enunciado. | Apenas o interpretador. |
| `InterpretadorComandos` | Transforma uma linha de texto em uma chamada ao núcleo; detecta comandos inválidos; diz se a linha é ignorável (vazia, `#`, e — na visual — `show`/`stats`). | A CLI e a visual. |
| CLI | Lê do teclado ou de um arquivo de roteiro e imprime as saídas textuais. | O usuário, pelo terminal. |
| Simulação visual | Carrega o roteiro, executa um passo por vez e desenha. | O usuário, pela janela. |

### 2.2 Relato de cada passo

Só o algoritmo sabe, no momento em que roda, quais buracos examinou. Por isso toda operação do
núcleo devolve um objeto `Relato`:

```java
class Relato {
    String comando;
    boolean sucesso;
    String mensagemErro;
    Bloco blocoAfetado;               // alocado ou liberado
    List<Bloco> buracosExaminados;    // na ordem em que o algoritmo os olhou
    Bloco buracoEscolhido;
    List<Bloco> buracosUnidos;        // coalescência
    List<Bloco> blocosMovidos;        // defrag
    int bytesMovidos;
}
```

A visual usa o relato para destacar as mudanças; a CLI o ignora (exceto erros e o resultado do
`defrag`). Exemplo, passo 7 da tabela do Next Fit (`alloc 6 next`, ponteiro em 28): o relato diz
"examinei o buraco @28 +4B, não coube; voltei ao início; escolhi @0 +8B". A tela pinta @28 como
examinado e rejeitado e @0 como escolhido, sem refazer a busca.

### 2.3 Histórico de estados (Voltar e Reiniciar)

Antes de executar cada passo, a visual guarda uma **cópia completa** da `Memoria` numa lista
`historico`. No roteiro `exemplo-nextfit.cmd` (7 passos), depois do passo 6 o histórico tem 7
cópias (estado inicial + uma por passo executado).

| Botão | Efeito |
|---|---|
| Voltar | Restaura a última cópia e a remove do histórico; `proximoId` e `ponteiroNextFit` voltam junto, pois estão dentro da cópia. |
| Reiniciar | Restaura a cópia 0 (estado anterior ao primeiro comando). |
| Avançar | Guarda a cópia atual e executa a próxima linha. |

No documento de decisões, este mecanismo pode ser apresentado como o padrão de projeto
*Memento* (o estado é salvo e restaurado sem que a tela conheça os detalhes internos da memória).

## 3. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Visual com lógica própria de alocação | Duplica regras; CLI e visual poderiam divergir. |
| Voltar por operação inversa (desalocar o que foi alocado) | Não devolve o id consumido nem o ponteiro anterior; `defrag` e coalescência não têm inversa simples. |
| Reexecutar o roteiro do início até o passo anterior | Correto, mas o custo cresce a cada passo e mistura reprodução com estado. |

## 4. Consequências

- A CLI e a visual produzem o mesmo estado final para o mesmo roteiro (verificado em teste).
- `Memoria` precisa de uma operação de cópia profunda (copiar também cada `Bloco`).
- Com memórias pequenas e roteiros curtos, o custo de memória do histórico é desprezível.
