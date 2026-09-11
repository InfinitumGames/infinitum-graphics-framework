# OMSI Modernization Tool Survey

Objetivo: mapear ferramentas existentes, entender o problema que resolvem, aprender com seus mecanismos, verificar licença/distribuição e decidir se o Infinitum deve integrar, substituir, reproduzir independentemente ou apenas usar como benchmark.

## Regra de pesquisa

Para cada ferramenta responder:

1. qual problema resolve;
2. como resolve em alto nível;
3. o que podemos medir/aprender;
4. qual sobreposição existe com o escopo v1.0;
5. qual é a licença/modelo de distribuição;
6. qual decisão do Infinitum: integrar, depender, substituir, comparar ou descartar.

## 4GB Patch / Large Address Aware

### Problema

O OMSI 2 é x86 e sofre com espaço de endereçamento limitado. O 4GB Patch é usado pela comunidade para marcar o executável como Large Address Aware em Windows x64.

### Pesquisa Infinitum

- detectar se `Omsi.exe` possui LAA;
- medir uso/pressão de address space;
- testar backup e rollback;
- testar comportamento após Steam verify/update;
- estudar implementação própria do flag PE/LAA em vez de empacotar ferramenta de terceiros.

### Direção

Criar **Memory Unlock / LAA Manager** próprio somente se implementação e distribuição forem tecnicamente e legalmente adequadas.

## AUXI Expansion 1.50

A versão 1.50, publicada em agosto de 2026, introduziu um **AI Performance Scaler** que ajusta automaticamente ônibus AI, carros e pedestres conforme frame rate e uso de memória. Também oferece um No Sleep Patch opcional.

### Importância

É uma referência direta para nosso Simulation Budget e Performance Controller.

### Testes

- scaler OFF/ON;
- AI em 25/50/75/100%;
- frametime, memória e número de entidades;
- comportamento durante queda de FPS;
- tempo e forma de recuperação;
- possíveis oscilações;
- impacto funcional na simulação.

### Direção

Benchmarkar comportamento. Não copiar implementação proprietária. Desenvolver arquitetura independente e mais observável.

## OmsiHook / Omsi-Extensions

Projeto open source LGPL-3.0 para acessar memória e estruturas internas do OMSI em tempo real. O repositório inclui bibliotecas, exemplos, plugin nativo e RPC. Versões recentes melhoraram leitura/escrita/alocação e métodos remotos.

### Uso no projeto

- laboratório para telemetria;
- referência de mapeamento de estruturas;
- possível provedor experimental do Runtime Bridge;
- validar mapa, tiles, veículos, humanos, câmera, D3D e outros globals.

### Decisão futura

Não tornar dependência obrigatória da v1.0 antes de decidir se:

- continuamos como consumidor da biblioteca;
- isolamos OmsiHook atrás de uma interface;
- ou reproduzimos somente mecanismos validados em um Runtime Bridge próprio.

## Blue Sky Tool

Ferramenta para localizar objetos, splines, veículos AI, carros estacionados, humanos e drivers ausentes.

### O que aprender

- cobertura de dependências de mapas;
- UX de diagnóstico;
- tipos de assets que mais geram erro;
- estrutura de relatório.

### Direção

O Infinitum Diagnostics deve incorporar um **Asset Integrity Scanner** próprio, sem assumir que código de terceiros pode ser reutilizado sem auditoria de licença.

## OMSI 2 Map Integrity Checker

Implementação em Rust que verifica referências em arquivos `.map` e relata splines/objetos/attachments ausentes.

### Direção

Referência de desempenho e cobertura para nosso parser/Asset Integrity Scanner. Auditar licença antes de qualquer uso de código.

## OMSI-Tools

Suite open source de modding OMSI, em Qt, GPL-2.0, com base de código extensa.

### Direção

Fazer inventário funcional e comparar com nosso escopo. GPL-2.0 exige cuidado caso qualquer código seja reutilizado; pesquisa de formatos/conceitos é separada de incorporar código.

## OMSI2 Mod Manager

Projeto MIT baseado em biblioteca separada + symlinks, com backup de arquivos substituídos, detecção de conflitos, load order e HOF Manager.

### O que aprender

- instalação não destrutiva;
- rollback;
- conflict detection;
- separação entre biblioteca e pasta do jogo.

### Direção

Levar esses princípios ao Configurator, mas não transformar a v1.0 em um mod manager universal.

## DXVK

Traduz Direct3D para Vulkan e é usado pela comunidade do OMSI como alternativa de estabilidade/desempenho.

### Direção

Não assumir que dgVoodoo é sempre superior. Manter benchmark oficial:

`DX9 original × dgVoodoo2 × DXVK`

Avaliar FPS, P95/P99, stutter, compatibilidade, reflexos, texturas e integração com nosso stack.

## dgVoodoo2

Backend experimental atual do projeto para DX9 → D3D11 e base do caminho FeedKit.

### Direção

- fixar versão/configuração de referência;
- medir overhead isolado;
- documentar VRAM virtual e opções relevantes;
- testar reflexos/compatibilidade;
- revisar termos de distribuição antes de release.

## ReShade + LumeniteFX

Base experimental atual para depth, efeitos, normais e motion vectors reconstruídos.

### Direção

Continuar como laboratório e baseline temporal enquanto pesquisamos o que precisa virar módulo próprio. Qualidade temporal deve ser validada antes de adicionar mais efeitos.

## FeedKit + DLSS5-Feeder + RenoDX / NGX

A cadeia atual permite reproduzir instalação x86, transportar frame/depth/MV para host64 e executar Neural Rendering experimental.

### Direção

- manter como backend NVIDIA de referência;
- medir overhead e qualidade temporal;
- abstrair contratos para não acoplar o projeto a um único backend;
- não redistribuir componentes proprietários sem autorização/licença.

## Lossless Scaling

Ferramenta externa de scaling/frame generation.

### Direção

Somente comparador avançado, depois de o frametime base do OMSI estar estabilizado. Não faz parte do core da v1.0.

## Power Toolkit

DLC comercial com ferramentas de timetable e Map Checker.

### Direção

Não competir com editor de horários na v1.0. Usar Map Checker como referência comparativa de diagnóstico quando disponível.

## Decisões iniciais

| Ferramenta | Decisão atual |
|---|---|
| 4GB Patch/LAA | reproduzir mecanismo de forma própria se validado |
| AUXI AI Scaler | benchmarkar e desenvolver solução independente |
| OmsiHook | usar como laboratório/provedor experimental |
| Blue Sky | comparar e criar Diagnostics próprio |
| Map Integrity Checker | comparar parser/cobertura |
| OMSI-Tools | auditar funcionalidades/licença |
| OMSI2 Mod Manager | absorver princípios de instalação/rollback |
| DXVK | benchmark backend |
| dgVoodoo2 | baseline atual, ainda não decisão final |
| ReShade/Lumenite | laboratório gráfico/temporal |
| FeedKit/Feeder/RenoDX/NGX | backend NVIDIA experimental |
| Lossless Scaling | comparador externo |
| Power Toolkit | referência de Map Checker |

## Regra final

O Infinitum não deve virar um pacote de ferramentas alheias. O valor do projeto é transformar conhecimento disperso em **integração, diagnóstico, medição, automação segura e módulos próprios**, usando dependências externas somente quando fizer sentido técnico, legal e de manutenção.
