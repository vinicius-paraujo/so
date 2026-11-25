# ADR-0004 — Isolamento da documentação interna

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Contexto

A pasta `docs/` (board, arquitetura e ADRs) é instrumento interno da equipe e não é entregue ao professor.
Diferente do trabalho do Prof. Yuri, porém, este enunciado **exige** um entregável documental:
"um documento explicando as decisões de implementação utilizadas (classes, estruturas de dados
utilizadas, padrões de projeto, se for o caso)", incluindo a descrição da estrutura de controle de
cada bloco, além de um README.

## 2. Decisão

1. **Nenhum arquivo entregue menciona a documentação interna**: nem `docs/`, nem ADRs, nem
   identificadores de cartões. Vale para código, testes, scripts de construção, README e
   mensagens dos programas.
2. **O documento de decisões é um entregável próprio**, autossuficiente, escrito para o professor.
   Ele pode reaproveitar o conteúdo das ADRs e da arquitetura, mas reescrito sem referências a
   eles.
3. **Comentários somente onde o motivo não é óbvio.** Sem bloco de cabeçalho descritivo em cada
   arquivo.
4. **README autossuficiente**, sem detalhes da máquina de nenhum integrante.

## 3. Versionamento

A pasta `docs/` é versionada no repositório para organizar o trabalho da equipe (board,
responsáveis, decisões). Ela é **removida do pacote de entrega** (o `.zip` pedido pelo professor).

## 4. Verificação

Antes da entrega, a busca abaixo, na raiz do projeto, não deve retornar resultados fora de `docs/`:

```bash
grep -rniE "docs/|\badrs?\b|adr-[0-9]|architecture\.md|board\.md|\bA-[0-9]+" --exclude-dir=docs --exclude-dir=.git .
```
