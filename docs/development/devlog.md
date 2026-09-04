# Devlog

## Devlog #001 — Primeiro pipeline funcional

O primeiro objetivo técnico foi descobrir se o OMSI 2, uma aplicação DirectX 9 e 32-bit, poderia alimentar um pipeline gráfico moderno externo.

A cadeia experimental passou por tradução DX9→D3D11, acesso ao depth via ReShade, reconstrução de motion vectors com LumeniteFX, transporte x86→x64 pelo DLSS5-Feeder e execução neural no host64.

Depois, o FeedKit foi adotado como base reproduzível de instalação experimental.

Com a prova técnica estabelecida, o projeto foi ampliado para tratar gráficos e desempenho como pilares equivalentes.
