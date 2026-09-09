# External Memory & Cache Manager

## Objetivo

Usar o Infinitum Host x64 para armazenar e processar dados que não precisam consumir o address space do OMSI 2 x86.

Este módulo não é um "RAM booster" e não promete fazer o `Omsi.exe` usar 8, 16 ou 32 GB diretamente.

## Candidatos ao host x64

- catálogo e metadados de assets;
- índices e hashes de arquivos;
- telemetria histórica;
- logs e traces;
- resultados de análise de mapas/rotas;
- dados para predição de tiles;
- cache próprio do framework;
- processamento neural e auxiliar.

## Telemetria de memória

O módulo Diagnostics deve acompanhar separadamente:

- memória privada/working set do OMSI;
- pressão de address space x86;
- memória do Host x64;
- VRAM quando disponível;
- eventos de falha de textura/material;
- stutters e loading próximos a picos de memória.

## Shared memory

Shared memory é candidata para troca de dados de alta frequência entre Runtime Bridge e Host x64. Ela deve transportar somente dados necessários e possuir versionamento de estrutura, timestamps e mecanismos que evitem bloquear o thread do OMSI.

## Critério de sucesso

O sucesso não é "RAM usada". É reduzir pressão dentro do processo legado, diminuir trabalho redundante, evitar falhas de recursos e melhorar consistência de frametime sem aumentar instabilidade.
