# Arquitetura — Atividade Prática: Alocação de Memória (Prof. Fernando Trinta)

> Documento vivo. Itens marcados **(proposta)** dependem de ADR ainda não aceita; itens marcados
> **(hipótese)** são lacunas do enunciado que precisam de confirmação antes da implementação.
> Princípio norteador: [ADR-0001 — Fidelidade ao material teórico](adrs/ADR-0001-fidelidade-ao-material-teorico.md).
> Fonte normativa: `Atividade Prática - Alocação de Memória.pdf`; vocabulário: slides "Alocação de
> Memória".

## 1. Requisitos

### 1.1 Requisitos funcionais

| ID | Requisito | Fonte |
|---|---|---|
| RF01 | `init <tamanho>` cria a memória com um único bloco livre. Deve ser o primeiro comando; repetido, reinicia tudo, inclusive o contador de ids e o ponteiro do Next Fit. | Enunciado |
| RF02 | `alloc <tamanho> first` usa o primeiro buraco, em ordem crescente de endereço, em que o bloco caiba. | Enunciado; slide First-fit |
| RF03 | `alloc <tamanho> best` usa o menor buraco em que o bloco caiba. | Enunciado; slide Best-Fit |
| RF04 | `alloc <tamanho> worst` usa o maior buraco. | Enunciado; slide Worst-Fit |
| RF05 | `alloc <tamanho> next`: ponteiro com o endereço logo após o último bloco alocado (0 após `init`/`defrag`). A busca começa no primeiro buraco cujo fim seja maior que o ponteiro (esse buraco é examinado inteiro), segue em ordem crescente, volta ao endereço 0 ao chegar ao fim e para ao esgotar os buracos. Usa o primeiro que comporte; o ponteiro passa para o fim do novo bloco. Liberações e falhas não movem o ponteiro. | Enunciado; slide Next-Fit |
| RF06 | Em empates (Best, Worst), vale o buraco de menor endereço. O bloco é colocado no **início** do buraco; a sobra continua livre. | Enunciado |
| RF07 | Ids sequenciais a partir de 1, nunca reaproveitados; alocação que falha não consome id. | Enunciado |
| RF08 | Sem buraco que comporte: imprime `Erro: sem espaço contíguo para <N> bytes (livre total: X, maior buraco: Y)` e não altera a memória. | Enunciado |
| RF09 | `freeid <id>` e `freeaddr <endereço>` liberam pelo id ou pelo endereço inicial. Id inexistente, ou endereço que não seja início de bloco alocado, é erro e não altera a memória. | Enunciado |
| RF10 | Após liberar, buracos adjacentes são unidos (coalescência). | Enunciado; slides |
| RF11 | `show`: mapa em duas linhas (uso `#`/`.` e ids dos blocos) e a linha `Blocos ativos`. | Enunciado |
| RF12 | `stats`: tamanho total, ocupado, livre, buracos (nº de blocos livres), maior buraco livre, uso efetivo = ocupado ÷ total. Sem alinhamento nem fragmentação interna: usado = tamanho do bloco. | Enunciado |
| RF13 | `defrag init`: blocos colados no fim, espaço livre no início. | Enunciado |
| RF14 | `defrag end`: blocos colados no início, espaço livre no fim. | Enunciado |
| RF15 | `defrag opt`: um único buraco em qualquer posição; os k primeiros blocos colados no início e os demais no fim, para o k de menor custo (soma dos tamanhos dos blocos cujo endereço inicial mudou); empate → maior k. | Enunciado |
| RF16 | `defrag` mantém ordem relativa e ids, informa blocos movidos e memória deslocada, e zera o ponteiro do Next Fit. | Enunciado |
| RF17 | Roteiro: arquivo de texto, um comando por linha na sintaxe da CLI; linhas vazias e iniciadas por `#` ignoradas. O mesmo roteiro roda na CLI. | Enunciado |
| RF18 | Simulação visual: mapa com escala de endereços, cor distinta por bloco, buracos claramente diferentes; o que mudou no passo; no `alloc`, buraco escolhido e (de preferência) os examinados antes; marca do ponteiro no Next Fit; comando atual, "passo X de Y", estatísticas e mensagem de erro. `show`/`stats` do roteiro são ignorados. | Enunciado |
| RF19 | Controles: Avançar; Voltar (restaura exatamente o estado anterior, com ids e ponteiro); Reproduzir/Pausar com intervalo de x segundos escolhido pelo usuário, aceitando decimais como `0,5`, parando no fim do roteiro e pausando se o usuário avançar ou voltar; Reiniciar (estado anterior ao primeiro comando); escolha do arquivo de roteiro. | Enunciado |

### 1.2 Requisitos não funcionais

| ID | Requisito | Fonte |
|---|---|---|
| RNF01 | A interface gráfica deve ser **superior à do Trabalho 01** (escalonamento, mesmo professor). Critérios concretos a definir pela equipe. | Equipe |

### 1.2.1 Requisitos de entrega

| ID | Requisito | Fonte |
|---|---|---|
| RE01 | Código comentado. | Enunciado |
| RE02 | Documento de decisões de implementação: classes, estruturas de dados, padrões de projeto; descrição da estrutura de controle de cada bloco (id, tamanho...). | Enunciado |
| RE03 | Simulador de linha de comando e simulação visual, no mesmo programa ou separados. | Enunciado |
| RE04 | README explicando como executar ambos e como carregar um roteiro. | Enunciado |
| RE05 | Linguagem livre. | Enunciado |

### 1.3 Lacunas do enunciado (hipóteses a confirmar)

| ID | Lacuna | Hipótese provisória |
|---|---|---|
| H01 | No exemplo, a sessão termina em `show` e a saída inclui o bloco `== Estatísticas ==`. | **Resolvida (equipe, 2026-11-16):** `show` imprime mapa, blocos ativos **e** estatísticas, exatamente como a saída esperada; `stats` imprime só as estatísticas. Na dúvida, exibir mais informação não contraria o exemplo. |
| H02 | O mapa usa um caractere por byte. Para memórias grandes (por exemplo, `init 1000`), a linha fica longa. | Um caractere por byte, sem escala, como no exemplo. |
| H03 | A linha de ids usa um caractere por byte. Ids com dois ou mais dígitos (10, 11...) não cabem; o id cresce a cada alocação bem-sucedida, mesmo com liberações. | **Em aberto: a equipe vai perguntar ao professor.** Opções discutidas: decimal (até 9), hexadecimal (até 15), base 36 (até 35), com símbolo fixo depois do limite. Afeta só a CLI; a visual escreve o id por extenso. |
| H04 | Formato das mensagens de erro de `freeid`/`freeaddr`, de comando antes de `init`, de comando desconhecido e de argumentos inválidos. | Mensagens no mesmo estilo de RF08 (`Erro: ...`), sem alterar a memória. |
| H05 | Formato da saída do `defrag` (blocos movidos e memória deslocada). | `Blocos movidos: X | Memória deslocada: Y B`, seguindo os nomes das colunas da tabela do enunciado. |
| H06 | Separador decimal do uso efetivo. | Ponto, como no exemplo (`25.00%`). |
| H07 | `alloc 0`, tamanhos negativos e `init` com tamanho não positivo. | Erro, sem alterar a memória. |

## 2. Visão geral (ADR-0007)

```
             roteiro .cmd / teclado
                     │
        ┌────────────▼────────────┐
        │ Interpretador de        │  linha → operação; ignora vazias e '#'
        │ comandos                │
        └──────┬───────────┬──────┘
               │           │
      ┌────────▼───┐   ┌───▼──────────────────────┐
      │    CLI     │   │   Simulação visual        │
      │ imprime    │   │ histórico de estados      │
      │ show/stats │   │ (Avançar/Voltar/Reiniciar)│
      └────────┬───┘   │ reprodução temporizada    │
               │       └───┬──────────────────────┘
               │           │ consome RELATO de cada passo
        ┌──────▼───────────▼──────┐
        │ Núcleo de simulação     │  sem entrada/saída
        │ MEMORIA: lista de BLOCO │
        │ FirstFit BestFit        │
        │ WorstFit NextFit        │
        │ Libera + coalescência   │
        │ Desfragmenta INIT/END/OPT│
        │ Estatisticas            │
        └─────────────────────────┘
```

## 3. Componentes

### 3.1 Estruturas de dados (ADR-0006)

```
BLOCO   { Inicio, Tamanho, Livre, Id }                  // Id = 0 em blocos livres
MEMORIA { Tamanho, Blocos[] ordenados por Inicio, ProximoId, PonteiroNextFit }
RELATO  { Comando, Sucesso, MensagemErro, BlocoAfetado, BuracosExaminados[],
          BuracoEscolhido, BuracosUnidos[], BlocosMovidos[], BytesMovidos }
```

Invariantes: blocos cobrem `[0, Tamanho)` sem sobreposição; nunca dois livres adjacentes;
`ProximoId` só cresce; `PonteiroNextFit` só muda em alocação bem-sucedida, `init` e `defrag`.

### 3.2 Algoritmos de alocação

Cada algoritmo é uma função própria que percorre os buracos e registra os examinados no relato.

| Algoritmo | Percurso | Critério | Desempate |
|---|---|---|---|
| First Fit | Do endereço 0 em diante | Primeiro que comporta | — (o primeiro já é o de menor endereço) |
| Best Fit | Todos | Menor que comporta | Menor endereço |
| Worst Fit | Todos | Maior (se comportar) | Menor endereço |
| Next Fit | Do primeiro buraco com fim > ponteiro, circular | Primeiro que comporta | — |

Alocar no buraco escolhido: o bloco ocupa o início; se sobrar espaço, um novo bloco livre é
inserido logo depois.

### 3.3 Liberação e coalescência

Localiza o bloco (por id ou por início), marca livre e une-o ao vizinho anterior e/ou posterior se
estiverem livres. O relato registra os buracos unidos.

### 3.4 Desfragmentação

Com os blocos alocados `b1..bn` em ordem de endereço:

- **END:** `b1` em 0, `b2` logo após, etc.
- **INIT:** `bn` termina no fim da memória, `b(n-1)` logo antes, etc.
- **OPT:** para cada k de 0 a n, `b1..bk` colados no início e `b(k+1)..bn` colados no fim; custo =
  soma dos tamanhos dos blocos cujo início mudou; escolhe o menor custo e, em empate, o maior k.
  Com k = n o resultado coincide com END; com k = 0, com INIT.

Em todos os modos, o relato traz os blocos movidos e a memória deslocada, e o ponteiro do Next Fit
volta a 0.

### 3.5 Estatísticas

Ocupado = soma dos blocos alocados; livre = tamanho − ocupado; buracos = nº de blocos livres; maior
buraco = maior bloco livre (0 se não houver); uso efetivo = ocupado ÷ tamanho × 100, com duas casas.

### 3.6 Simulação visual (ADR-0007 e ADR-0008)

- Carrega o roteiro, filtra linhas ignoradas, e conta os passos (Y).
- Antes de cada passo, guarda uma cópia da memória; Voltar e Reiniciar restauram cópias.
- Reprodução automática por temporizador; Avançar/Voltar manuais pausam a reprodução; ao fim do
  roteiro, pausa.
- Exibe, a partir do relato: comando, "passo X de Y", mapa com escala e cores, destaques do passo,
  marca do ponteiro (no Next Fit), estatísticas e erro.

## 4. Casos de teste obrigatórios (oráculos do enunciado)

| Caso | Entrada | Resultado esperado |
|---|---|---|
| Sessão interativa | `init 64; alloc 10 first; alloc 8 first; freeid 2; alloc 6 best; show` | Mapa `#`×16 + `.`×48; ids `1`×10 + `3`×6; `[id=1] @0 +10B (usado=10B) \| [id=3] @10 +6B (usado=6B)`; ocupado 16, livre 48, buracos 1, maior 48, uso 25.00% |
| `defrag init` | Memória 64; id=1 @0 +10B, id=3 @18 +6B | 2 blocos movidos, 16 B; id=1 @48, id=3 @58 |
| `defrag end` | Idem | 1 bloco movido, 6 B; id=1 @0, id=3 @10 |
| `defrag opt` | Idem | 1 bloco movido, 6 B; k = 2 (empate com k = 1) |
| Next Fit | `init 32; alloc 8 next ×3; freeid 1; alloc 4 next; alloc 6 next` | Ponteiros 0, 8, 16, 24, 24, 28, 6; final id=5 @0 +6B, id=2 @8, id=3 @16, id=4 @24 +4B; ocupado 26, livre 6, buracos 2, maior 4, uso 81.25% |
| Erro sem espaço | Pedido maior que o maior buraco | Mensagem de RF08; memória, ids e ponteiro inalterados |

## 5. Estrutura de diretórios

```
trabalho-alocacao/
├── build.gradle, settings.gradle, gradlew, gradlew.bat, gradle/wrapper/   Gradle Wrapper (ADR-0002)
├── src/main/java/br/ufc/so/alocacao/
│   ├── nucleo/      Memoria, Bloco, algoritmos, desfragmentação, estatísticas, Relato (ADR-0006)
│   ├── comandos/    InterpretadorComandos (ADR-0007)
│   ├── cli/         Principal: simulador de linha de comando
│   └── visual/      PrincipalVisual e telas Swing (ADR-0008)
├── src/test/java/br/ufc/so/alocacao/   testes JUnit 5 (ADR-0003)
├── roteiros/        roteiros .cmd de exemplo
├── README.md
└── docs/            não versionado nem entregue (ADR-0004)
```

Comandos: `./gradlew build` (compila e testa), `./gradlew test`, `./gradlew run` (CLI),
`./gradlew runVisual` (simulação visual), `./gradlew installDist` (scripts em `build/install/`).

## 6. Riscos

| Risco | Mitigação |
|---|---|
| Interpretação divergente de H01–H07 | Confirmar com o professor ou decidir em equipe antes da implementação; registrar em ADR. |
| Visual reimplementar regras e divergir da CLI | Núcleo único (ADR-0007); teste de roteiro compara CLI e motor da visual. |
| Voltar não restaurar ids/ponteiro | Cópia completa do estado; teste específico. |
