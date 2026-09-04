# Pipeline gráfico experimental

```text
OMSI 2
DirectX 9 / x86
   ↓
dgVoodoo2
DX9 → D3D11
   ↓
ReShade x86
   ↓
Depth + informações temporais
   ↓
LumeniteFX Motion Vectors
   ↓
DLSS5-Feeder x86
   ↓
host64
   ↓
RenoDX / NGX
   ↓
NVIDIA Neural Rendering
```

## Por que existe um host 64-bit?

O OMSI 2 é um processo 32-bit. O protótipo usa uma ponte para um host 64-bit onde a parte neural pode ser executada.

## Limites

Este documento descreve a cadeia experimental validada até o momento. O pipeline final ainda está em desenvolvimento.
