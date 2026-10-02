# Escopo v1.0 — não concluído

> **Documento histórico.** Este escopo foi congelado originalmente em 11 de setembro de 2026. O projeto foi encerrado em **1º de outubro de 2026**, antes da implementação da v1.0. Nenhum item abaixo deve ser interpretado como compromisso futuro.

## Visão planejada

A v1.0 pretendia modernizar o OMSI 2 por meio de uma arquitetura híbrida, mantendo a engine x86 original e adicionando infraestrutura moderna para gráficos, telemetria, desempenho, diagnóstico, memória externa, instalação e testes.

A meta nunca foi transformar o `Omsi.exe` em uma engine moderna nativa, mas estender e otimizar o que fosse tecnicamente controlável sem quebrar compatibilidade.

## Módulos que estavam previstos

- Infinitum Core;
- Configurator;
- Memory & Address Space diagnostics;
- Graphics Engine;
- Performance Engine;
- Adaptive Performance;
- Loading & Streaming Manager;
- Diagnostics.

Nenhum conjunto completo desses módulos chegou a constituir uma v1.0 integrada.

## Critérios de engenharia planejados

O projeto havia definido que uma otimização somente seria aceita após baseline reproduzível, alteração isolável, repetição, métricas de frametime, teste de regressão, rollback e documentação. Esses critérios permanecem registrados como metodologia de pesquisa.

## Fora do escopo planejado

Permaneciam fora da v1.0: conversão do `Omsi.exe` para 64-bit, reescrita da engine, multicore completo, DirectX 12/Vulkan nativos da engine, substituição completa do loader, path tracing, ray tracing completo, multiplayer, editores completos e promessa de 60 FPS em qualquer hardware/mapa.

## Resultado

A v1.0 **não foi lançada**. O projeto foi encerrado durante a fase de pesquisa e provas de conceito. Consulte [Encerramento e retrospectiva](../project-retrospective.md).
