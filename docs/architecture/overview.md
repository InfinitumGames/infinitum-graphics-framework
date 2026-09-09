# Arquitetura — visão geral

O Infinitum Graphics Framework é uma camada híbrida de modernização ao redor da engine legada do OMSI 2. O projeto não tenta fingir que o executável x86 se tornou uma engine moderna; ele mantém no processo do jogo apenas o que precisa permanecer ali e pesquisa como deslocar telemetria, cache, diagnóstico, processamento neural e outros serviços para componentes externos.

## Princípio

**Medir → entender → controlar → otimizar → modernizar.**

Automação só deve ser ativada depois que um controle possuir telemetria, comportamento reproduzível, reversão segura e testes suficientes.

## Arquitetura híbrida

```text
OMSI Runtime x86
├─ simulação
├─ scripts
├─ AI / humanos
├─ mapa / tiles
└─ renderização legada
        ↕
Runtime Bridge / IPC
        ↕
Infinitum Host x64
├─ Telemetry Engine
├─ Performance Controller
├─ Diagnostics
├─ External Cache / Memory Services
├─ Loading & Streaming Services
└─ Neural / Modern Graphics Services
```

## Infinitum Core

Configuração, detecção de ambiente, ativação de módulos, versionamento e compatibilidade.

## Runtime Bridge

Camada de integração entre o processo x86 e serviços externos. O primeiro objetivo é **read-only telemetry**. Escritas em runtime só entram após validação específica de segurança.

## Infinitum Graphics

Efeitos e tecnologias visuais próprias voltadas ao OMSI 2, incluindo pesquisa futura de tonemapping, materiais e shader replacement.

## Infinitum Temporal

Camada para depth, motion vectors, histórico, disocclusion, confidence e motion clarity.

## Infinitum Neural

Abstração para backends neurais. O backend experimental atual é NVIDIA. Execução técnica não equivale a qualidade temporal validada.

## Infinitum Performance Engine

Telemetria, frametime, Performance Controller e orçamentos separados de CPU, GPU, simulação, memória e I/O.

## Simulation Budget

Pesquisa e telemetria para AI agendada/não agendada, veículos estacionados, pedestres, passageiros e custo de scripts. O objetivo é evitar reduzir qualidade gráfica quando o gargalo real estiver na simulação.

## Loading & Streaming Manager

Telemetria de tiles, eventos de loading, cache, memória, assets e stutter. A evolução prevista é observar → prever → influenciar; a terceira etapa ainda não está tecnicamente confirmada.

## External Memory & Cache Manager

Serviços x64 para cache, metadados, telemetria histórica e futuros mecanismos de pré-processamento. Este módulo **não aumenta o limite interno do Omsi.exe**.

## Diagnostics

Verificação automática da cadeia com resultados PASS, WARNING e FAIL, incluindo memória/address space, integridade de texturas e dependências.

## Configurator

Interface para presets, hardware, módulos, backend, instalação, diagnóstico e perfis de desempenho.
