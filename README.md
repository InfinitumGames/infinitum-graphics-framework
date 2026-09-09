# Infinitum Graphics Framework for OMSI 2

> Projeto experimental de modernização híbrida de gráficos, desempenho e infraestrutura para OMSI 2.

## Estado do projeto

🚧 **Em desenvolvimento — experimental**

O projeto ainda não possui uma versão pública para download. O objetivo atual é construir e validar uma base técnica reproduzível antes de qualquer release.

O protótipo já demonstrou tecnicamente:

- tradução do pipeline DirectX 9 → DirectX 11;
- acesso funcional ao depth buffer;
- reconstrução de motion vectors;
- comunicação entre o processo 32-bit do OMSI 2 e um host 64-bit;
- inicialização e execução experimental de NVIDIA Neural Rendering;
- pipeline reproduzível usando FeedKit;
- início da arquitetura do Infinitum Performance Engine;
- pesquisa de telemetria runtime, tiles, AI, memória e controles adaptativos.

A execução técnica do pipeline **não significa** que qualidade visual, estabilidade ou ganho de desempenho final estejam validados. Motion clarity, texturas/materiais, cockpit, espelhos, loading e benchmarks continuam em validação.

## Visão

O Infinitum Graphics Framework não pretende ser apenas um pacote de shaders. A proposta é criar uma **camada híbrida de modernização da engine**, preservando a compatibilidade com o OMSI 2 x86 e deslocando serviços modernos para componentes externos quando tecnicamente seguro.

O projeto é organizado em pilares independentes:

- **Graphics Engine** — modernização visual, materiais, iluminação, pós-processamento e integração temporal/neural;
- **Performance Engine** — telemetria, frametime, CPU/GPU/Simulation Budget, qualidade adaptativa, AI e espelhos;
- **Loading & Streaming Manager** — telemetria de tiles, memória, cache, assets, loading e redução de stutter;
- **Runtime Bridge** — integração segura entre o OMSI x86 e serviços modernos do framework;
- **Host x64** — processamento neural, telemetria histórica, cache externo, diagnóstico e futuros serviços auxiliares;
- **Diagnostics** — diagnóstico automatizado da instalação, memória e pipeline;
- **Configurator** — interface moderna para configurar o framework;
- **Backends** — NVIDIA inicialmente, com estudo futuro de alternativas para AMD e Intel.

## Arquitetura híbrida alvo

```text
OMSI Runtime x86
  ├─ simulação / scripts / AI
  ├─ mapa / tiles / veículos / humanos
  └─ renderização legada
          ↕ Runtime Bridge / IPC
Infinitum Host x64
  ├─ Performance Controller
  ├─ Telemetry & Diagnostics
  ├─ External Cache / Memory Services
  ├─ Loading & Streaming Services
  └─ Neural / Modern Graphics Services
```

O objetivo não é transformar o `Omsi.exe` em 64-bit nem prometer uso automático de todos os núcleos ou de toda a RAM do PC. A estratégia é **estender a engine ao redor de suas limitações** e reduzir trabalho desnecessário no processo legado sempre que houver um caminho validado.

## Pipeline gráfico experimental atual

```text
OMSI 2 (DirectX 9, 32-bit)
        ↓
dgVoodoo2 (DX9 → D3D11)
        ↓
ReShade x86
        ↓
Depth + LumeniteFX Motion Vectors
        ↓
DLSS5-Feeder x86
        ↓
host64
        ↓
RenoDX / NGX / NVIDIA Neural Rendering
```

Essa pilha representa a base experimental atual, não uma promessa de arquitetura final.

## Princípio de desenvolvimento

**Medir → entender → controlar → otimizar → modernizar.**

Controles runtime só devem ser automatizados depois de telemetria reproduzível, mecanismo reversível e testes que demonstrem segurança.

## Documentação

A documentação pública está sendo construída na pasta [`docs/`](docs/).

- [Estado atual](docs/current-status.md)
- [Roadmap](docs/roadmap.md)
- [Arquitetura](docs/architecture/overview.md)
- [Pipeline gráfico](docs/architecture/graphics-pipeline.md)
- [Performance Engine](docs/architecture/performance-engine.md)
- [Devlog](docs/development/devlog.md)
- [Dependências de terceiros](docs/legal/third-party.md)

## Transparência técnica

O projeto separa explicitamente componentes próprios, integrações externas e componentes de terceiros. Dependências externas não serão redistribuídas sem autorização ou licença compatível.

FeedKit, ReShade, dgVoodoo2, LumeniteFX, RenoDX, DLSS5-Feeder e componentes NVIDIA não são de autoria da Infinitum Games.

## Aviso

Este projeto não é afiliado, patrocinado ou endossado pelos desenvolvedores do OMSI 2, NVIDIA ou pelos autores das dependências externas citadas.

---

**Infinitum Games** — desenvolvimento experimental para modernização do OMSI 2.
