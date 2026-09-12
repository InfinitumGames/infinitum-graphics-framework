# Problemas e limitações conhecidas

Esta página separa falhas observadas de limitações ainda em pesquisa. Uma ocorrência não deve ser tratada como causa confirmada sem teste reproduzível.

## ISSUE-001 — Temporal blur durante movimento no cockpit

**Estado:** em investigação — crítico.

O Neural Rendering executa tecnicamente no host64, porém foi observada perda de nitidez/blur durante movimento. Depth e motion vectors passaram em validações isoladas, portanto o próximo trabalho é investigar motion clarity, history, disocclusion, ghosting e estabilidade temporal com A/B reproduzível.

## ISSUE-002 — Texture allocation exhaustion / D3DERR_OUTOFVIDEOMEMORY

**Estado:** reproduzido e confirmado no log — crítico.

No cenário BusBrasilFest 2025, o OMSI registra repetidamente `Texturladen - Direct9 Error: D3DERR_OUTOFVIDEOMEMORY (-2005532292)` seguido por falhas de carregamento de texturas. A ocorrência atinge múltiplas classes de recursos: humanos, veículos, scenery objects, splines e texturas do mapa.

Sintomas visuais correlacionados observados durante a mesma sessão:

- pedestres brancos/sem textura;
- veículos brancos ou sem textura;
- materiais anormalmente cromados/reflexivos;
- veículos parcialmente transparentes;
- objetos/texturas que aparecem com atraso.

O log também registra `Direct3D-Device lost` e `Direct3D-Device resetted` antes da sequência de falhas de alocação. Isso confirma a falha de criação/carregamento de recursos Direct3D, mas ainda não identifica sozinho qual orçamento/limite foi esgotado.

**Não interpretar `D3DERR_OUTOFVIDEOMEMORY` automaticamente como falta de VRAM física da GPU.** Precisamos separar: address space do processo x86, estado Large Address Aware do executável, `texmemlimit` do OMSI, orçamento reportado pelo wrapper D3D9→D3D11, comportamento do texture manager e pressão real de recursos.

### Próximas verificações

1. verificar se o `Omsi.exe` atual possui a flag PE `IMAGE_FILE_LARGE_ADDRESS_AWARE` (`0x20`);
2. registrar versão/hash do executável testado;
3. correlacionar memória privada/virtual/working set do processo com o primeiro `D3DERR_OUTOFVIDEOMEMORY`;
4. registrar `texmemlimit` e configurações de textura do OMSI;
5. manter dgVoodoo VRAM em 1024 MB durante o baseline inicial e alterar apenas uma variável por teste;
6. repetir o cenário com configuração controlada e registrar o momento da primeira falha;
7. separar texturas realmente ausentes/referências quebradas das falhas de alocação por memória.

Este caso passa a ser requisito prioritário do futuro Diagnostics/Memory & Resource Manager: detectar pressão de memória, identificar falhas de alocação e impedir conclusões erradas baseadas apenas no hardware físico disponível.

## ISSUE-003 — Assets realmente ausentes ou referências de textura inválidas

**Estado:** confirmado como problema separado.

O mesmo logfile contém referências de mods/cenário para texturas que não existem ou não aparecem no mesh correspondente. Esses erros devem ser tratados separadamente do `D3DERR_OUTOFVIDEOMEMORY`: corrigir memória não cria um asset que está ausente, e um asset ausente isolado não explica a avalanche de falhas de alocação observada.

## ISSUE-004 — Avisos/erros de shaders e recursos no stack experimental

**Estado:** em acompanhamento.

O stack ReShade/Lumenite/Feeder pode produzir avisos de compilação ou descoberta de recursos. Cada mensagem precisa ser classificada entre inofensiva, regressão visual e falha funcional.

## ISSUE-005 — Técnica Lumenite duplicada

**Estado:** aberto.

Entradas duplicadas de `LUMENITE: Kernel 2.0` já foram observadas. O baseline deve manter apenas uma instância ativa e investigar shader/search paths duplicados.

## ISSUE-006 — Pressão de memória/address space x86 ainda não instrumentada

**Estado:** pesquisa prioritária — relacionado ao ISSUE-002.

O host x64 permite deslocar serviços externos, mas não remove o limite do processo x86 do OMSI. O ISSUE-002 torna prioritária a telemetria própria para correlacionar memória, falhas de textura, loading e stutter.

## Limitações gerais ainda abertas

- Cockpit, espelhos, chuva e noite precisam de testes sistemáticos.
- O protótipo depende atualmente de vários componentes externos.
- Alterações dinâmicas em parâmetros do OMSI precisam ser validadas individualmente antes de automação.
- As possibilidades reais de intervenção no loader interno ainda não estão determinadas.
- Não há benchmark final demonstrando ganho de desempenho do framework completo.
