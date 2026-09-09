# Loading & Streaming Manager

O Loading & Streaming Manager é o pilar dedicado a tempos de carregamento, stutter, tiles, assets, memória e I/O. O objetivo não é assumir controle do loader interno antes de compreender seu comportamento.

## Estratégia em três níveis

### 1. Observar

Primeiro registrar o que o OMSI está fazendo e quando:

- estados de tiles;
- eventos de loading;
- picos de frametime;
- veículos e humanos ativos;
- pressão de memória/address space;
- RAM/VRAM do stack;
- atividade de I/O quando disponível.

A pesquisa com estruturas runtime encontrou estados relevantes de tiles como `Loaded`, `Load_Request`, `ThreadLoading`, `ThreadLoading_Real` e `ThreadLoading_Render`. Eles são candidatos para o primeiro protótipo de Tile Loading Telemetry e precisam ser validados localmente antes de serem tratados como API estável.

### 2. Prever

Depois de existir telemetria confiável, pesquisar:

- direção/velocidade do ônibus;
- próximos tiles prováveis;
- assets recorrentes;
- histórico de loading por rota;
- classificação de hotspots de stutter.

O objetivo é descobrir se é possível preparar trabalho externo antes de o OMSI entrar em uma região pesada.

### 3. Influenciar

Somente depois das fases anteriores:

- pré-carregamento seletivo;
- cache externo;
- priorização de assets;
- políticas de memória;
- eventuais chamadas ou controles runtime do loader.

Ainda **não está confirmado** que o framework poderá alterar com segurança o loader interno do OMSI.

## External Cache

O host x64 poderá usar memória fora do processo x86 para dados que não precisam residir dentro do `Omsi.exe`, por exemplo:

- catálogo e metadados de assets;
- hashes e índices de arquivos;
- telemetria histórica;
- resultados de pré-processamento;
- cache próprio do framework.

Isso não converte o OMSI em 64-bit e não remove seu limite interno de address space.

## Métricas

- loading total;
- P95/P99 de frametime;
- duração e frequência de stutters;
- tempo por estado de tile;
- RAM do OMSI e do host;
- VRAM;
- falhas de textura/recurso;
- eventos de I/O correlacionados.

## Primeiro MVP

O primeiro MVP deve ser somente observacional: registrar timestamp, Timegap/frametime, estados de tiles e memória em uma rota fixa e produzir um log/CSV/JSON que permita correlacionar loading com travadas perceptíveis.
