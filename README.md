# Infinitum Graphics Framework for OMSI 2

> Projeto experimental de modernização gráfica, desempenho e infraestrutura para OMSI 2.

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
- início da arquitetura do Infinitum Performance Engine.

A execução técnica do pipeline **não significa** que qualidade visual, estabilidade ou ganho de desempenho final estejam validados. Esses pontos continuam em testes.

## Visão

O Infinitum Graphics Framework não pretende ser apenas um pacote de shaders. A proposta é criar uma camada de modernização para o OMSI 2, organizada em pilares independentes:

- **Graphics Engine** — modernização visual, iluminação, pós-processamento e integração temporal/neural;
- **Performance Engine** — frametime, qualidade adaptativa, CPU/GPU budget, AI, espelhos e estabilidade;
- **Loading & Streaming Manager** — pesquisa e desenvolvimento de técnicas para reduzir loading e stutter;
- **Diagnostics** — diagnóstico automatizado da instalação e do pipeline;
- **Configurator** — interface moderna para configurar o framework;
- **Backends** — NVIDIA inicialmente, com estudo futuro de alternativas para AMD e Intel.

## Pipeline experimental atual

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
