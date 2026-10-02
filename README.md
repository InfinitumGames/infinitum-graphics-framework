# Infinitum Graphics Framework for OMSI 2

> Projeto experimental de pesquisa sobre modernização híbrida de gráficos, desempenho e infraestrutura para OMSI 2.

## Status do projeto

🛑 **Desenvolvimento encerrado em 1º de outubro de 2026.**

O Infinitum Graphics Framework foi encerrado definitivamente como projeto de desenvolvimento ativo. O repositório permanece disponível como **arquivo técnico e histórico de pesquisa**.

Não existe release pública do Infinitum Graphics Framework e não há previsão de retomada, versão 1.0 ou suporte ativo.

## O que foi demonstrado

Durante a pesquisa e os protótipos experimentais, foram demonstrados:

- tradução do pipeline DirectX 9 → DirectX 11;
- acesso funcional e coerente ao depth buffer;
- reconstrução de motion vectors com LumeniteFX;
- comunicação entre o processo 32-bit do OMSI 2 e um host 64-bit;
- inicialização e execução experimental de NVIDIA Neural Rendering;
- pipeline reproduzível usando FeedKit;
- validação de Large Address Aware no executável testado;
- investigação controlada de falhas de texturas e `D3DERR_OUTOFVIDEOMEMORY`;
- pesquisa de telemetria runtime, mapa, tiles, veículos, humanos, memória e controles adaptativos;
- desenho conceitual de Performance Engine, Runtime Bridge, Loading & Streaming Manager e outros módulos.

Esses resultados representam **pesquisa e provas de conceito**, não um produto integrado.

## O que não foi concluído

Ao encerramento do projeto:

- não existia executável próprio integrado pronto para distribuição;
- não existia release Alpha/Beta/v1.0;
- não havia benchmark A/B final demonstrando ganho consistente de FPS;
- Neural Rendering ainda apresentava questões de qualidade temporal;
- Performance Engine, Adaptive Quality, Adaptive Mirrors, Runtime Bridge próprio, Configurator, Loading & Streaming Manager e Memory & Resource Manager permaneceram em pesquisa, arquitetura ou planejamento;
- não foi implementada uma conversão do OMSI para 64-bit, multicore nativo, DirectX 12 nativo ou Vulkan nativo.

## Arquitetura pesquisada

A direção arquitetural estudada foi uma camada híbrida que mantivesse o OMSI 2 x86 e deslocasse serviços modernos para componentes externos:

```text
OMSI Runtime x86
        ↕
Runtime Bridge / IPC
        ↕
Host x64
 ├─ Performance / Telemetry
 ├─ Diagnostics
 ├─ External Cache / Memory Services
 └─ Neural / Modern Graphics Services
```

A arquitetura acima é documentação de pesquisa. Ela não chegou a ser implementada integralmente como software Infinitum.

## Pipeline gráfico experimental

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

A execução técnica desse pipeline foi experimental e não deve ser interpretada como validação de qualidade visual, estabilidade ou desempenho final.

## Documentação preservada

A pasta [`docs/`](docs/) preserva arquitetura, pesquisas, testes, limitações e o roadmap histórico.

- [Encerramento e retrospectiva](docs/project-retrospective.md)
- [Estado final](docs/current-status.md)
- [Roadmap histórico](docs/roadmap.md)
- [Arquitetura](docs/architecture/overview.md)
- [Pipeline gráfico](docs/architecture/graphics-pipeline.md)
- [Devlog](docs/development/devlog.md)
- [Dependências de terceiros](docs/legal/third-party.md)

## Dependências e autoria

FeedKit, ReShade, dgVoodoo2, LumeniteFX, RenoDX, DLSS5-Feeder e componentes NVIDIA não são de autoria da Infinitum Games. Este repositório não deve ser interpretado como redistribuição, endosso ou reivindicação de autoria sobre esses projetos.

## Aviso

Este projeto não é afiliado, patrocinado ou endossado pelos desenvolvedores do OMSI 2, NVIDIA ou pelos autores das dependências externas citadas.

---

**Infinitum Games**  
Período de pesquisa e desenvolvimento: **setembro de 2026 — 1º de outubro de 2026**  
Estado final: **encerrado / arquivo técnico**
