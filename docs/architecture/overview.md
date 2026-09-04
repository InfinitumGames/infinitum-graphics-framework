# Arquitetura — visão geral

O framework é dividido em módulos para evitar que o projeto se torne apenas um conjunto de ferramentas externas.

## Infinitum Core
Configuração, detecção de ambiente, ativação de módulos e compatibilidade.

## Infinitum Graphics
Efeitos e tecnologias visuais próprias voltadas ao OMSI 2.

## Infinitum Temporal
Camada conceitual para depth, motion vectors, histórico e confiança.

## Infinitum Neural
Abstração para backends neurais. O backend experimental atual é NVIDIA.

## Infinitum Performance Engine
Telemetria, frametime, políticas adaptativas e orçamento de CPU/GPU.

## Loading & Streaming Manager
Pesquisa de carregamento, cache, memória e stutter.

## Diagnostics
Verificação automática da cadeia com resultados PASS, WARNING e FAIL.

## Configurator
Interface para presets, hardware, módulos, backend, instalação e diagnóstico.
