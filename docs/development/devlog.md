# Devlog

## Devlog #001 — Primeiro pipeline funcional

O primeiro objetivo técnico foi descobrir se o OMSI 2, uma aplicação DirectX 9 e 32-bit, poderia alimentar um pipeline gráfico moderno externo.

A cadeia experimental passou por tradução DX9→D3D11, acesso ao depth via ReShade, reconstrução de motion vectors com LumeniteFX, transporte x86→x64 pelo DLSS5-Feeder e execução neural no host64.

Depois, o FeedKit foi adotado como base reproduzível de instalação experimental.

Com a prova técnica estabelecida, o projeto foi ampliado para tratar gráficos e desempenho como pilares equivalentes.


## Devlog final — Encerramento do projeto — 1º de outubro de 2026

O desenvolvimento ativo do Infinitum Graphics Framework foi encerrado definitivamente. O projeto termina na fase de pesquisa e provas de conceito, sem release pública integrada.

O repositório e a documentação permanecem públicos como arquivo técnico. Os resultados confirmados, limitações, investigações de memória/texturas e arquitetura proposta estão consolidados em [Encerramento e retrospectiva](../project-retrospective.md).
