# ADR-0008 — Tecnologia da simulação visual

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Contexto

A simulação visual é obrigatória neste trabalho. A forma é livre ("aplicação gráfica, página web
ou terminal interativo"), desde que mostre mapa com escala de endereços e cor por bloco, mudanças
do passo, buracos examinados, ponteiro do Next Fit, passo atual ("5 de 7"), estatísticas e erros;
e ofereça Avançar, Voltar, Reproduzir/Pausar com intervalo decimal, Reiniciar e escolha do arquivo
de roteiro.

## 2. Alternativas (por linguagem)

| Linguagem (ADR-0002) | Tecnologia | Observação |
|---|---|---|
| Java | Swing, como no Trabalho 01 | Sem dependências; seletor de arquivo e temporizador nativos (`JFileChooser`, `javax.swing.Timer`). |
| C | Terminal interativo (`ncurses`) | Cores e teclas como controles; seletor de arquivo vira argumento ou campo de texto; exige instalar a biblioteca de desenvolvimento. |
| Python | Tkinter ou página web | Tkinter vem com o Python; web exige servidor ou exportação de estados. |

## 3. Decisão

**Aceita em 2026-11-16:** Swing, como no Trabalho 01. Seletor de arquivo com `JFileChooser`,
reprodução automática com `javax.swing.Timer`, mapa desenhado com `Graphics2D`. A visual não
contém regras de alocação.
