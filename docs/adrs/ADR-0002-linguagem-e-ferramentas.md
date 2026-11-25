# ADR-0002 — Linguagem e ferramentas

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Contexto

A linguagem é livre. A interface é de linha de comando
(`./pager --algo <ALGO> --frames <N> --trace <arquivo> [--verbose]`), e o enunciado aceita
equivalentes (`python3 pager.py ...`, `java -jar pager.jar ...`, `node pager.js ...`) com os mesmos
argumentos. A visualização gráfica é **bônus opcional** (ADR-0007). A equipe já entregou ao mesmo
professor o Trabalho 01 em Java 21 com Gradle Wrapper, JUnit 5 e Swing; o trabalho do Prof. Yuri
foi feito em C. Os dois trabalhos práticos de memória podem, mas não precisam, usar a mesma
linguagem.

## 2. Alternativas

| Alternativa | Vantagens | Custos e riscos |
|---|---|---|
| **C + `make`** | Executável literalmente chamado `./pager`; proximidade com o tema de SO; convenções do trabalho anterior (macro de testes própria). | Leitura de argumentos e do arquivo mais trabalhosa; bônus visual mais caro (terminal). |
| **Java 21 + Gradle Wrapper + JUnit 5** | Mesma pilha do Trabalho 01 com este professor; bônus visual em Swing sem dependências; tipagem explícita. | Execução por `java -jar pager.jar` ou script; mais código estrutural. |
| **Python** | Mais conciso; execução direta (`python3 pager.py`). | Tipagem dinâmica deixa estruturas menos explícitas. |
| **Mesma linguagem do trabalho de Alocação** | Uma só cadeia de ferramentas e um só estilo para a equipe. | Depende da escolha feita lá. |

## 3. Decisão

**Aceita em 2026-11-16 pela equipe:** Java 21, construção pelo Gradle Wrapper, testes com JUnit 5 e
interface gráfica em Swing, a mesma pilha do Trabalho 01 com este professor.

1. Nenhuma dependência além do JDK 21; o Gradle Wrapper é versionado no projeto.
2. Testes com JUnit 5, em `src/test/java`, executados por `./gradlew test`.
3. Fluxo imperativo e legível: cada algoritmo com sua regra de escolha visível em um método
   próprio; *streams* e padrões de projeto só quando melhorarem a compreensão.
