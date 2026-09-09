# Problemas e limitações conhecidas

Esta página separa falhas observadas de limitações ainda em pesquisa. Uma ocorrência não deve ser tratada como causa confirmada sem teste reproduzível.

## ISSUE-001 — Temporal blur durante movimento no cockpit

**Estado:** em investigação — crítico.

O Neural Rendering executa tecnicamente no host64, porém foi observada perda de nitidez/blur durante movimento. Depth e motion vectors passaram em validações isoladas, portanto o próximo trabalho é investigar motion clarity, history, disocclusion, ghosting e estabilidade temporal com A/B reproduzível.

## ISSUE-002 — Pedestres com textura/material branco ou ausente

**Estado:** causa não confirmada — crítico.

Pessoas sem textura ou com aparência branca foram observadas. As hipóteses incluem pressão de memória/address space, falha de recurso/textura ou interação com o pipeline gráfico. Não atribuir a causa ao limite de memória nem ao ReShade/dgVoodoo antes da correlação com telemetria e logs.

## ISSUE-003 — Objetos/cenário com material ou textura ausente

**Estado:** causa não confirmada — crítico.

Deve ser investigado junto ao ISSUE-002 para descobrir se existe um mecanismo comum de falha de recursos.

## ISSUE-004 — Avisos/erros de shaders e recursos no stack experimental

**Estado:** em acompanhamento.

O stack ReShade/Lumenite/Feeder pode produzir avisos de compilação ou descoberta de recursos. Cada mensagem precisa ser classificada entre inofensiva, regressão visual e falha funcional.

## ISSUE-005 — Técnica Lumenite duplicada

**Estado:** aberto.

Entradas duplicadas de `LUMENITE: Kernel 2.0` já foram observadas. O baseline deve manter apenas uma instância ativa e investigar shader/search paths duplicados.

## ISSUE-006 — Pressão de memória/address space x86 ainda não instrumentada

**Estado:** pesquisa prioritária.

O host x64 permite deslocar serviços externos, mas não remove o limite do processo x86 do OMSI. Ainda falta telemetria própria para correlacionar memória, falhas de textura, loading e stutter.

## Limitações gerais ainda abertas

- Cockpit, espelhos, chuva e noite precisam de testes sistemáticos.
- O protótipo depende atualmente de vários componentes externos.
- Alterações dinâmicas em parâmetros do OMSI precisam ser validadas individualmente antes de automação.
- As possibilidades reais de intervenção no loader interno ainda não estão determinadas.
- Não há benchmark final demonstrando ganho de desempenho do framework completo.
