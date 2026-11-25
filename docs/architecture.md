# Arquitetura — Atividade Prática: Paginação (Prof. Fernando Trinta)

> Documento vivo. Itens marcados **(proposta)** dependem de ADR ainda não aceita; itens marcados
> **(hipótese)** são lacunas do enunciado que precisam de confirmação antes da implementação.
> Princípio norteador: [ADR-0001 — Fidelidade ao material teórico](adrs/ADR-0001-fidelidade-ao-material-teorico.md).
> Fonte normativa: `Atividade Prática - Paginação.pdf`; vocabulário: slides "Paginação".

## 1. Requisitos

### 1.1 Interface e entrada

| ID | Requisito | Fonte |
|---|---|---|
| RF01 | `./pager --algo <ALGO> --frames <N> --trace <arquivo> [--verbose]` (ou equivalente da linguagem com os mesmos argumentos). | Enunciado |
| RF02 | `--algo` ∈ {`fifo`, `lru`, `opt`, `clock`, `nru`, `lfu`, `mfu`}; `--frames` inteiro ≥ 1; `--trace` caminho do arquivo. Nada fixo no código. | Enunciado |
| RF03 | `.trace`: uma referência por linha; número da página com sufixo opcional `R` ou `W`; sem letra = leitura; linhas vazias e iniciadas por `#` ignoradas. | Enunciado |

### 1.2 Regras de simulação (comuns)

| ID | Regra | Fonte |
|---|---|---|
| RS01 | Página nova ocupa o **menor frame livre**; com todos ocupados, o frame da vítima. | Enunciado |
| RS02 | Só há evicção com todos os frames ocupados: evicções = faltas − frames (quando faltas > frames). | Enunciado |
| RS03 | **Desempate em todos os algoritmos:** remove-se a página residente há mais tempo (carga mais antiga). | Enunciado |
| RS04 | Escrita em disco: cada evicção de página com M = 1. Páginas modificadas ainda residentes no fim não contam. | Enunciado |
| RS05 | Conjunto residente final listado por frame. | Enunciado |

### 1.3 Regras por algoritmo

| Algoritmo | Vítima | Estado usado | Fonte |
|---|---|---|---|
| FIFO | Residente há mais tempo | `CargaEm` | Enunciado; slide FIFO |
| LRU | Acesso mais antigo | `UltimoAcesso` | Enunciado; slide LRU |
| Ótimo (`opt`) | Próximo acesso mais distante; páginas sem acesso futuro têm prioridade | Cadeia futura | Enunciado; slide Ótimo |
| Clock | Fila circular; ponteiro começa no frame 0 e só se move em evicções. R = 1 na carga e em cada acesso. Na evicção, zera os R = 1 que encontra e avança até achar R = 0; a página nova ocupa esse frame e o ponteiro avança uma posição. | `BitR`, `PonteiroClock` | Enunciado; slide relógio |
| NRU | R = 1 em todo acesso; M = 1 em acesso `W`. R de todas as páginas zerado após cada 4 referências (4ª, 8ª, 12ª...). Classe = 2·R + M; vítima = menor classe. | `BitR`, `BitM` | Enunciado; slide NRU |
| LFU | Menor contador de acessos desde a carga (zera na evicção) | `ContadorAcessos` | Enunciado |
| MFU | Maior contador | `ContadorAcessos` | Enunciado |

### 1.4 Saídas e métricas

| ID | Requisito | Fonte |
|---|---|---|
| RO01 | Resumo: `Algoritmo`, `Frames`, `Referências`, `Faltas de página`, `Taxa de faltas` (duas casas e `%`), `Evicções`, `Escritas em disco`, `Conjunto residente final` com `frame_ids` e `page_ids`. | Enunciado |
| RO02 | `--verbose`: uma linha por referência antes do resumo: passo, `ref=`, frames entre colchetes (`.` = vazio), `falta`/`acerto`; em evicção, `(remove P)` e se houve escrita em disco. | Enunciado |

### 1.5 Requisitos não funcionais

| ID | Requisito | Fonte |
|---|---|---|
| RNF01 | A interface gráfica do bônus deve ser **superior à do Trabalho 01** (escalonamento, mesmo professor). Critérios concretos a definir pela equipe. | Equipe |

### 1.5.1 Entregáveis

| ID | Requisito | Fonte |
|---|---|---|
| RE01 | Código comentado e README com compilação e execução. | Enunciado |
| RE02 | Documento de decisões (classes, estruturas, padrões), incluindo a execução de todos os algoritmos nos dois traços e a análise da anomalia de Belady. | Enunciado |
| RE03 | Traços `silberschatz2001.trace`, `belady.trace` e `escritas.trace` incluídos. | Enunciado |
| RE04 | Bônus opcional: visualização passo a passo (ADR-0007). | Enunciado |

### 1.6 Lacunas do enunciado (hipóteses a confirmar)

| ID | Lacuna | Hipótese provisória |
|---|---|---|
| H01 | Texto exato do verbose quando a evicção causa escrita em disco (o exemplo só tem leituras). | **Resolvida (equipe, 2026-11-16):** acrescenta-se `, escrita em disco` **só** quando a página removida tem M = 1: `falta (remove 1, escrita em disco)`. Sem escrita, a linha fica idêntica ao exemplo (`falta (remove 7)`). Mesmo termo da métrica do enunciado; Maziero §17.4.2 e §14.7.2 (página modificada é gravada na área de troca antes de sair). |
| H08 | O exemplo do verbose mostra `ref=7`, sem o tipo de acesso; com traços que têm escritas, a letra explica por que houve escrita em disco. | **Resolvida (equipe, 2026-11-16):** o verbose mostra o tipo de acesso em todas as linhas (`ref=7R`, `ref=1W`). Diverge do exemplo oficial apenas nesse sufixo; o teste do verbose compara o restante da linha com o enunciado. |
| H02 | Conjunto residente final quando sobram frames vazios (traço com menos páginas distintas que frames). | Listar todos os frames, com `.` nos vazios, como no verbose. |
| H03 | Linhas inválidas no `.trace` (letra diferente de R/W, número negativo, texto). | Erro com o número da linha e código de saída diferente de 0. |
| H04 | Mensagens e código de saída para argumentos ausentes ou inválidos. | Mensagem de uso em `stderr` e código de saída diferente de 0. |
| H05 | Maiúsculas no `--algo` (`LRU`) e na letra do acesso (`r`/`w`). | Aceitar sem distinção de maiúsculas. |
| H06 | Nome exibido em `Algoritmo:` (o exemplo mostra `LRU`). | Nome em maiúsculas; Ótimo como `OTIMO` ou `OPT`. **Precisa de decisão.** |
| H07 | Momento da zeragem do R no NRU em relação à referência atual. | Após processar a 4ª, 8ª... referência (inclusive a página recém-carregada). É a interpretação que reproduz os valores de referência. |

## 2. Visão geral (ADR-0006)

```
 argumentos ──► Leitura de argumentos ──┐
 arquivo .trace ─► Leitor de traço ─────┼──► Simula(algoritmo, frames, referências)
                                        │        laço comum: acerto / falta / evicção
                                        │        └─► EscolheVitima<Algoritmo>
                                        │
                                        └──◄ RESULTADO { métricas, frames finais, PASSOS[] }
                                                   │                 │
                                           Formatação do       (bônus) Visual
                                           resumo e verbose     percorre PASSOS[]
```

## 3. Componentes

### 3.1 Estruturas de dados (ADR-0006)

```
REFERENCIA { Pagina, Escrita }
FRAME      { Pagina (ou vazio), CargaEm, UltimoAcesso, ContadorAcessos, BitR, BitM }
PASSO      { Numero, Pagina, Acerto, FramesDepois[], PaginaRemovida, EscreveuEmDisco }
RESULTADO  { Algoritmo, NumeroFrames, Referencias, FaltasDePagina, Eviccoes,
             EscritasEmDisco, FramesFinais[], Passos[] }
```

`CargaEm` e `UltimoAcesso` usam o número da referência (tempo lógico), não o relógio.

### 3.2 Laço comum

Para cada referência i:
1. Se a página está em algum frame: acerto; `UltimoAcesso = i`, `ContadorAcessos++`, `BitR = 1`,
   `BitM = 1` se escrita.
2. Senão: falta. Menor frame livre ou `EscolheVitima`; em evicção, conta evicção e, se `BitM = 1`,
   escrita. Carrega a página: `CargaEm = UltimoAcesso = i`, `ContadorAcessos = 1`, `BitR = 1`,
   `BitM = escrita`.
3. Se NRU e `i` múltiplo de 4: zera `BitR` de todos os frames.
4. Registra o `PASSO`.

### 3.3 Escolha de vítima

Uma função por algoritmo (tabela 1.3). Todas usam `CargaEm` mais antiga como desempate. O Clock é
a única que altera estado durante a escolha (zera bits R e move o ponteiro).

## 4. Casos de teste

### 4.1 Oráculos do enunciado

**Faltas de página, `silberschatz2001.trace`:**

| Frames | FIFO | LRU | Ótimo | Clock | NRU | LFU | MFU |
|---|---|---|---|---|---|---|---|
| 3 | 15 | 12 | 9 | 14 | 10 | 13 | 15 |
| 4 | 10 | 8 | 8 | 9 | 9 | 9 | 12 |

**Faltas de página, `belady.trace`:** FIFO 9 (3 frames) e 10 (4 frames); LRU 10 e 8; Ótimo 7 e 6.

**`escritas.trace`, 3 frames (faltas / evicções / escritas):** FIFO 6/3/3; LRU 9/6/3; Clock 8/5/3;
NRU 9/6/2.

**Resumo LRU, 3 frames, `silberschatz2001.trace`:** 20 referências, 12 faltas, 60.00%, 9 evicções,
0 escritas, `frame_ids: 0 1 2`, `page_ids: 1 0 7`.

**Verbose:** as 10 primeiras linhas do exemplo do enunciado.

### 4.2 Oráculos derivados (calculados pela equipe com as regras do enunciado)

Um simulador de rascunho, fora do projeto, aplicando as regras de §1.2 e §1.3, reproduziu **todos**
os valores de §4.1 e forneceu os que faltam:

**`belady.trace`:**

| Frames | FIFO | LRU | Ótimo | Clock | NRU | LFU | MFU |
|---|---|---|---|---|---|---|---|
| 3 | 9 | 10 | 7 | 9 | 10 | 10 | 9 |
| 4 | 10 | 8 | 6 | 10 | 6 | 8 | 10 |

**`escritas.trace`, 3 frames (faltas / evicções / escritas):** Ótimo 6/3/3; LFU 8/5/2; MFU 6/3/3.

### 4.3 Anomalia de Belady (insumo para o documento de decisões)

No `belady.trace`, **FIFO, Clock e MFU** aumentam as faltas de 9 para 10 ao passar de 3 para 4
frames. **LRU e Ótimo** não apresentam a anomalia em nenhuma cadeia: são algoritmos de pilha (o
conjunto residente com n frames está sempre contido no conjunto com n + 1). **NRU e LFU** não a
apresentaram neste traço, mas não são algoritmos de pilha, então isso não prova que sejam imunes.
O Clock, sendo uma variação do FIFO, herda a suscetibilidade.

## 5. Estrutura de diretórios

```
trabalho-paginacao/
├── build.gradle, settings.gradle, gradlew, gradlew.bat, gradle/wrapper/   Gradle Wrapper (ADR-0002)
├── src/main/java/br/ufc/so/paginacao/
│   ├── Principal    ponto de entrada de ./pager
│   ├── entrada/     leitura de argumentos e do .trace
│   ├── nucleo/      Simulador, Frame, Referencia, Passo, Resultado (ADR-0006)
│   ├── algoritmos/  AlgoritmoSubstituicao e uma classe por algoritmo (ADR-0006)
│   ├── saida/       formatação do resumo e do verbose
│   └── visual/      PrincipalVisual e telas Swing (ADR-0007)
├── src/test/java/br/ufc/so/paginacao/  testes JUnit 5 (ADR-0003)
├── traces/          silberschatz2001.trace, belady.trace, escritas.trace
├── README.md
└── docs/            não versionado nem entregue (ADR-0004)
```

Comandos: `./gradlew build`, `./gradlew test`, `./gradlew installDist` (gera
`build/install/pager/bin/pager`, o equivalente ao `./pager` do enunciado), `./gradlew runVisual`.

## 6. Riscos

| Risco | Mitigação |
|---|---|
| Desempates sutis (Ótimo, LFU/MFU, NRU) | Testes de regras isoladas e conjunto residente final, não só o total de faltas. |
| Professor executar traços diferentes | Nada fixo no código; testes com traços próprios (comentários, linhas vazias, `R`/`W`). |
| Oráculos derivados errados | Calculados com as regras escritas e já validados contra todos os valores do enunciado; divergências investigadas antes de mudar o código. |
