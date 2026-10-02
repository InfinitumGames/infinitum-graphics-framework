# Encerramento e retrospectiva

## Registro

O desenvolvimento do **Infinitum Graphics Framework for OMSI 2** foi encerrado definitivamente em **1º de outubro de 2026**.

O projeto nasceu como uma tentativa de modernizar a apresentação gráfica e o desempenho do OMSI 2, mas a pesquisa revelou um problema de engenharia muito maior: integrar uma aplicação DirectX 9 e 32-bit com recursos gráficos modernos, telemetria, gerenciamento de recursos e serviços externos sem acesso ao código-fonte da engine.

A decisão de encerramento ocorreu antes da construção de uma versão integrada distribuível. Este repositório permanece público para preservar os experimentos, as conclusões e as linhas de pesquisa.

## O que foi realizado

### Pipeline gráfico

Foi montada e validada experimentalmente uma cadeia capaz de levar o OMSI 2 de DirectX 9 para um ambiente D3D11 por meio do dgVoodoo2, acessar depth via ReShade, reconstruir motion vectors com LumeniteFX e transportar dados do processo x86 para um host x64 através do DLSS5-Feeder/FeedKit.

O host neural conseguiu inicializar NGX e executar avaliações experimentais de NVIDIA Neural Rendering.

### Dados temporais

O depth buffer foi observado de forma coerente em 1920×1080 e os motion vectors reconstruídos responderam ao movimento de câmera, ônibus e objetos. A execução neural, porém, ainda apresentava perda de nitidez/blur em movimento e não chegou à validação de qualidade final.

### Arquitetura x86/x64

A pesquisa levou à proposta de manter o OMSI no processo x86 e usar um Runtime Bridge pequeno para comunicação com um Host x64 externo. O objetivo seria deslocar telemetria, diagnóstico, cache, processamento neural e outros serviços para fora do processo legado.

Essa arquitetura permaneceu como direção técnica; não foi concluída como implementação própria do Infinitum.

### Performance e runtime

Foram estudados os mecanismos nativos do OMSI relacionados a reflexos econômicos, redução dinâmica de tiles, AI e outras opções de custo. O OmsiHook foi pesquisado como evidência de que mapa, tiles, veículos, humanos, câmera, materiais e outras estruturas poderiam ser observados em runtime.

Dessa pesquisa surgiram os conceitos de Telemetry Engine, Performance Controller, Adaptive Quality Manager, Simulation Budget e Adaptive Mirrors.

### Memória e texturas

Um dos experimentos mais relevantes reproduziu falhas visuais de texturas em conteúdo pesado e registrou `D3DERR_OUTOFVIDEOMEMORY` no logfile.

Os testes mostraram que:

- Large Address Aware estava ativo no executável testado;
- a RTX ainda possuía VRAM física disponível durante uma ocorrência;
- alterar apenas o orçamento virtual do dgVoodoo modificava significativamente o ponto e a intensidade da falha;
- reduzir `texmemlimit` não funcionou como um simples hard cap da memória observada;
- Berlin-Spandau serviu como controle saudável com carga de texturas muito menor e sem reproduzir a avalanche de falhas.

A conclusão prática foi que conteúdo pesado pode pressionar o caminho de gerenciamento de recursos/texturas do OMSI e da camada D3D, e que `D3DERR_OUTOFVIDEOMEMORY` não deve ser interpretado automaticamente como esgotamento da VRAM física.

Essa pesquisa originou os conceitos de Memory & Resource Manager, Texture Budget Manager e Asset & Texture Profiler, que não chegaram a ser implementados.

## O que não foi alcançado

O projeto foi encerrado antes de possuir:

- um executável próprio integrado;
- release pública;
- Runtime Bridge próprio completo;
- Performance Engine funcional;
- Configurator;
- gerenciamento dinâmico de memória/streaming;
- Adaptive Mirrors funcional;
- benchmark A/B final que demonstrasse ganho consistente de desempenho;
- validação visual completa do Neural Rendering.

Também nunca foi objetivo tecnicamente confirmado converter o `Omsi.exe` para 64-bit, tornar toda a simulação multicore ou reescrever a engine.

## Componentes de terceiros

Grande parte das provas de conceito utilizou ferramentas e projetos externos, entre eles FeedKit, ReShade, dgVoodoo2, LumeniteFX, DLSS5-Feeder, RenoDX e componentes NVIDIA.

Os resultados obtidos com essas ferramentas não devem ser apresentados como implementações próprias da Infinitum Games. O trabalho do projeto consistiu em pesquisa, integração experimental, testes, diagnóstico, documentação e desenho arquitetural.

## Legado

Embora não tenha se tornado um produto final, o projeto deixou:

- documentação pública;
- metodologia de testes;
- resultados experimentais;
- diagnóstico de limitações do OMSI;
- arquitetura proposta para integração x86/x64;
- estudos de performance, runtime, reflexos, loading e memória;
- um registro técnico que pode servir de referência para pesquisas futuras.

## Estado final

**Desenvolvimento encerrado. Sem roadmap ativo, sem previsão de release e sem compromisso de retomada.**

O conteúdo permanece disponível como **arquivo técnico e histórico de pesquisa**.
