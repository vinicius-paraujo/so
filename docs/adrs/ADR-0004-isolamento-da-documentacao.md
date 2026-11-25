# ADR-0004 — Isolamento da documentação interna

- **Status:** Aceita
- **Data:** 2026-11-16

## 1. Contexto

A pasta `docs/` (board, arquitetura e ADRs) é instrumento interno da equipe e não é entregue ao professor. O
enunciado, porém, **exige** dois entregáveis documentais: um README com compilação e execução, e
um documento explicando as decisões de implementação (classes, estruturas de dados, padrões de
projeto), que também deve "executar todos os algoritmos nos dois traços, indicar quais apresentam
a anomalia [de Belady] e explicar por quê".

## 2. Decisão

1. **Nenhum arquivo entregue menciona a documentação interna** (`docs/`, ADRs, cartões).
2. **O documento de decisões é um entregável próprio e autossuficiente**, escrito para o
   professor, incluindo a seção de análise da anomalia de Belady. Pode reaproveitar o conteúdo das
   ADRs e da arquitetura, reescrito sem referências a eles.
3. **Comentários somente onde o motivo não é óbvio**, sem bloco de cabeçalho descritivo em cada
   arquivo.
4. **README autossuficiente**, sem detalhes da máquina de nenhum integrante.

## 3. Versionamento

A pasta `docs/` é versionada no repositório para organizar o trabalho da equipe (board,
responsáveis, decisões). Ela é **removida do pacote de entrega** (o `.zip` pedido pelo professor).

## 4. Verificação

```bash
grep -rniE "docs/|\badrs?\b|adr-[0-9]|architecture\.md|board\.md|\bP-[0-9]+" --exclude-dir=docs --exclude-dir=.git .
```

Não deve retornar resultados fora de `docs/`.
