# Estratégia híbrida x86/x64

## Problema

O OMSI 2 permanece um processo x86. Um wrapper gráfico ou host externo não converte sua simulação, scripts, mapa ou gerenciamento interno em uma engine 64-bit.

## Estratégia

Manter no processo do OMSI somente o trabalho que precisa permanecer dentro da engine e deslocar serviços independentes para um host x64.

```text
Windows x64
├─ OMSI.exe x86
│  ├─ simulação
│  ├─ scripts
│  ├─ AI
│  ├─ mapa/tiles ativos
│  └─ render submission
└─ Infinitum Host x64
   ├─ telemetria
   ├─ diagnóstico
   ├─ cache externo
   ├─ banco de dados de assets
   ├─ processamento neural
   └─ Performance Controller
```

## O que essa arquitetura pode melhorar

- disponibilidade de RAM para serviços próprios fora do OMSI;
- telemetria e logs sem ocupar address space do jogo;
- processamento neural e tarefas auxiliares em 64-bit;
- separação entre simulação legada e lógica moderna do framework;
- possibilidade de usar threads/cores adicionais para trabalho que seja realmente independente da engine.

## O que ela não faz

- não transforma `Omsi.exe` em 64-bit;
- não aumenta diretamente seu address space;
- não torna scripts e AI automaticamente multicore;
- não remove gargalos do thread principal por si só;
- não garante aumento de FPS.

## Regra de arquitetura

Sempre perguntar: **este trabalho precisa ocorrer dentro do processo do OMSI?**

Se não precisar, ele é candidato ao Host x64. Se precisar, deve ser medido e otimizado dentro das limitações reais da engine.
