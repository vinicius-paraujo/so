# ADR-0006 — Modelo da memória: lista de blocos ordenada por endereço

- **Status:** Aceita
- **Data:** 2026-11-16
- **Relacionada a:** [ADR-0001](ADR-0001-fidelidade-ao-material-teorico.md), [ADR-0002](ADR-0002-linguagem-e-ferramentas.md)

## 1. Problema

Entre um comando e outro, o simulador precisa lembrar o estado da memória: onde começa e termina
cada bloco alocado, qual o id de cada um e onde estão os buracos. Todos os comandos dependem
desse estado: o `alloc` procura um buraco, o `freeid`/`freeaddr` localiza um bloco, o `show`
desenha, o `stats` soma e o `defrag` move blocos.

## 2. Fontes consultadas

| Fonte | O que diz |
|---|---|
| Enunciado | "Sugere-se que cada bloco possua alguma estrutura que mapeie informações para controle, como id e tamanho. Essa estrutura deve estar descrita no documento." |
| Slide "Alocação Básica" | Manter o registro das áreas livres/ocupadas em "Listas, Árvores e Mapas de Bits". |
| Slide "Alocação com múltiplas partições" | O SO mantém informações sobre partições alocadas e partições livres (buracos). |
| Maziero, §16.2 (p. 198) | As áreas ocupadas e livres "são geralmente mantidas em uma ou mais listas duplamente encadeadas (ou árvores) de áreas de memória". |
| Maziero, §16.3 (p. 198) | Quanto mais fragmentada a memória, "mais longas serão as listas encadeadas de área de memória livres". |

O livro e o slide convergem: a representação de referência é a **lista de áreas**.

## 3. Decisão

A memória é uma **lista única de blocos, em ordem crescente de endereço**, que cobre toda a
memória. Cada elemento é um bloco alocado ou um buraco:

```java
class Bloco {
    int inicio;      // endereço do primeiro byte
    int tamanho;
    boolean livre;   // true = buraco
    int id;          // 0 nos buracos; 1, 2, 3... nos alocados
}

class Memoria {
    int tamanho;
    List<Bloco> blocos;      // ordenada por inicio, sem lacunas nem sobreposição
    int proximoId;           // começa em 1 e nunca diminui
    int ponteiroNextFit;     // endereço logo após o último bloco alocado
}
```

A lista guarda livres e ocupados juntos, e não em duas listas separadas, porque a coalescência
precisa saber quem é o vizinho imediato de um bloco liberado, seja ele livre ou ocupado.

### 3.1 Exemplo: a sessão interativa do enunciado

| Comando | `blocos` depois do comando |
|---|---|
| `init 64` | [buraco @0 +64] |
| `alloc 10 first` | [id=1 @0 +10] [buraco @10 +54] |
| `alloc 8 first` | [id=1 @0 +10] [id=2 @10 +8] [buraco @18 +46] |
| `freeid 2` | [id=1 @0 +10] [buraco @10 +8] [buraco @18 +46] → coalescência → [id=1 @0 +10] [buraco @10 +54] |
| `alloc 6 best` | [id=1 @0 +10] [id=3 @10 +6] [buraco @16 +48] |
| `show` | percorre a lista: 16 `#` e 48 `.`; na segunda linha, 10 `1`, 6 `3` e 48 `.` |

### 3.2 Quem usa a estrutura e quando

| Quem | Quando | O que faz com a lista |
|---|---|---|
| Algoritmos de alocação | Em cada `alloc` | Percorrem os buracos em ordem de endereço e escolhem um; dividem o buraco em bloco alocado + sobra livre. |
| Liberação | Em cada `freeid`/`freeaddr` | Localiza o bloco, marca como livre e une com o vizinho anterior e o posterior se forem livres. |
| Desfragmentação | Em cada `defrag` | Recalcula o `inicio` dos blocos alocados e refaz o(s) buraco(s). |
| `show`, `stats`, simulação visual | Ao exibir | Só leem a lista; o mapa de caracteres é gerado na hora, não armazenado. |

### 3.3 Invariantes (verificados nos testes)

1. Os blocos cobrem `[0, tamanho)` exatamente, sem lacunas nem sobreposição.
2. Nunca há dois buracos adjacentes (a coalescência sempre é aplicada).
3. `proximoId` só cresce; uma alocação que falha não o altera.
4. `ponteiroNextFit` só muda em alocação bem-sucedida, `init` e `defrag`.

### 3.4 Divergência registrada entre livro e enunciado

Maziero (§16.3.1, p. 199) e o slide "Estratégias de Alocação Clássicas" descrevem o Next-Fit
partindo da "última área alocada **ou liberada**". O enunciado é explícito: "Liberar blocos e
alocações que falham não movem o ponteiro". **Prevalece o enunciado** (ADR-0001); a diferença será
mencionada no documento de decisões.

## 4. Alternativas consideradas

| Alternativa | Motivo da rejeição |
|---|---|
| Mapa de bits / vetor com o id de cada byte (`[1,1,...,3,3,...,0,...]`) | O `show` fica trivial, mas cada `alloc` precisa varrer todos os bytes para reconstruir os buracos; o bloco deixa de existir como objeto, que é justamente a estrutura que o enunciado pede descrever. |
| Duas listas (livres e ocupados) | A busca de buracos percorre só livres, mas a coalescência e o `show` precisam intercalar as duas listas; mais estado para manter consistente. |
| Árvore de buracos por tamanho | Acelera Best/Worst Fit, mas o desempate por endereço exige critério secundário e a complexidade não se justifica com memórias de dezenas ou centenas de bytes. |
