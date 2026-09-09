# Roadmap público

## Fase 0 — estabilizar o baseline atual

Prioridade imediata: investigar motion blur/perda de nitidez durante movimento e texturas/materiais brancos ou ausentes. Não adicionar uma pilha de novos efeitos antes de existir baseline visual confiável.

## Graphics Engine — em desenvolvimento

Pipeline visual, depth, motion vectors, motion clarity, efeitos próprios e integração temporal/neural. Pesquisa futura inclui tonemapping moderno, shader replacement e modernização de materiais.

## Runtime Bridge — em desenvolvimento

Primeiro protótipo read-only para conectar o OMSI x86 à infraestrutura própria e registrar Timegap/frametime, mapa, tiles, veículos, humanos e memória.

## Performance Engine — em desenvolvimento

Target FPS/frametime, Telemetry Engine, Performance Controller, CPU/GPU/Simulation Budget, distância adaptativa, tráfego/pedestres e espelhos adaptativos.

## Loading & Streaming — pesquisa ativa

Evolução planejada: **observar → prever → influenciar**. A primeira etapa prioriza Tile Loading Telemetry e correlação de estados de loading com stutter.

## External Memory & Cache — pesquisa

Serviços x64 para cache, metadados, telemetria e processamento externo, sem prometer ampliar o limite interno do processo x86.

## Diagnostics — planejado

Verificação automatizada do pipeline, dependências, depth, motion vectors, host, backend neural, memória/address space e integridade de texturas.

## Configurator — planejado

Interface moderna para presets, recursos, diagnóstico e perfis de hardware.

## Compatibilidade ampliada — futuro

Backend NVIDIA é a frente atual; alternativas AMD e Intel serão estudadas posteriormente.

## Distribuição pública — futuro

Alpha somente após critérios mínimos de estabilidade, documentação, licenciamento, instalação reproduzível e um benchmark A/B confiável.

## Sequência de engenharia

**Medir → entender → controlar → otimizar → modernizar.**
