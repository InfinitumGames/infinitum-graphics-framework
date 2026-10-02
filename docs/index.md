# Infinitum Graphics Framework

Bem-vindo ao arquivo técnico do **Infinitum Graphics Framework for OMSI 2**.

!!! warning "Projeto encerrado"
    O desenvolvimento foi encerrado definitivamente em **1º de outubro de 2026**. Não existe release pública nem roadmap ativo. Esta documentação permanece disponível como registro histórico e técnico.

O projeto pesquisou uma camada híbrida de modernização para um simulador originalmente baseado em DirectX 9 e processo 32-bit, abrangendo gráficos, desempenho, telemetria, memória, loading e diagnóstico.

## Principais resultados

- pipeline experimental DX9 → D3D11;
- depth buffer funcional;
- motion vectors reconstruídos;
- comunicação experimental x86 → host x64;
- execução experimental de Neural Rendering;
- investigação de memória/texturas e `D3DERR_OUTOFVIDEOMEMORY`;
- pesquisa de telemetria runtime e arquitetura híbrida.

Esses resultados foram provas de conceito e pesquisa. O projeto foi encerrado antes de existir um software Infinitum integrado para distribuição.

Leia [Encerramento e retrospectiva](project-retrospective.md), [Estado final](current-status.md) e [Roadmap histórico](roadmap.md).
