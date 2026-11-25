# ADR-0002 — Linguagem e ferramentas

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Contexto

O enunciado deixa a linguagem livre. Há dois entregáveis executáveis: o simulador de linha de
comando e a simulação visual passo a passo, "no mesmo programa ou em programas separados". A
simulação visual tem requisitos de interface relevantes: mapa com cores por bloco, escala de
endereços, botões Avançar, Voltar, Reproduzir, Pausar e Reiniciar, intervalo decimal configurável
e escolha do arquivo de roteiro.

A equipe já entregou ao mesmo professor o Trabalho 01 (escalonamento) em Java 21 com Gradle
Wrapper, JUnit 5 e interface Swing. O trabalho do Prof. Yuri foi feito em C, mas lá a linguagem era
imposta pelo enunciado.

## 2. Alternativas

| Alternativa | Vantagens | Custos e riscos |
|---|---|---|
| **Java 21 + Gradle Wrapper + JUnit 5 + Swing** | Mesma pilha do Trabalho 01 com este professor; interface gráfica sem dependências externas; tipagem explícita para blocos e estados; testes automatizados maduros. | Mais código estrutural. |
| **C + `make` + terminal interativo** | Continuidade com o Trabalho do Prof. Yuri; próximo do "funcionamento interno de um sistema operacional". | A simulação visual com cores, botões e reprodução temporizada exige terminal interativo (por exemplo `ncurses`) ou outra tecnologia; maior esforço na parte visual. |
| **Python + interface web ou Tkinter** | Implementação concisa; interface web atende bem ao requisito visual. | Tipagem dinâmica deixa estruturas menos explícitas; duas cadeias de ferramentas se a visual for web. |

## 3. Decisão

**Aceita em 2026-11-16 pela equipe:** Java 21, construção pelo Gradle Wrapper, testes com JUnit 5 e
interface gráfica em Swing, a mesma pilha do Trabalho 01 com este professor.

1. Nenhuma dependência além do JDK 21; o Gradle Wrapper é versionado no projeto.
2. Testes com JUnit 5, em `src/test/java`, executados por `./gradlew test`.
3. Fluxo imperativo e legível: cada algoritmo com sua regra de escolha visível em um método
   próprio; *streams* e padrões de projeto só quando melhorarem a compreensão.

## 4. Critérios de conformidade (após a decisão)

- O projeto compila e executa por um único comando documentado no README.
- A suíte de testes executa por um único comando.
- Nenhuma dependência exige instalação além do que o README lista.
