# Escopo fechado — Infinitum Graphics Framework v1.0

Data de congelamento inicial: **11 de setembro de 2026**.

Este documento existe para impedir crescimento infinito do projeto. Novas ideias podem entrar no backlog, mas não passam automaticamente a fazer parte da v1.0.

## Visão do produto

O Infinitum Graphics Framework v1.0 deve modernizar o OMSI 2 por meio de uma arquitetura híbrida, mantendo a engine x86 original e adicionando infraestrutura moderna para gráficos, telemetria, desempenho, diagnóstico, memória externa, instalação e testes.

A meta não é transformar o `Omsi.exe` em uma engine moderna nativa. A meta é **estender e otimizar o que for tecnicamente controlável sem quebrar compatibilidade**.

## Módulos obrigatórios da v1.0

### 1. Infinitum Core

- detectar instalação e versão do OMSI;
- detectar hardware e ambiente;
- gerenciar configuração e versão dos módulos;
- ativar/desativar módulos com rollback seguro;
- produzir informações mínimas para Diagnostics e Configurator.

### 2. Configurator

- interface central para instalação e configuração;
- perfis de desempenho;
- status dos módulos;
- backup/restore e rollback;
- diagnóstico acessível ao usuário;
- gerenciamento de dependências externas conforme licença permitir.

### 3. Memory & Address Space

- detectar status Large Address Aware do `Omsi.exe`;
- estudar/aplicar mecanismo LAA por implementação própria somente se tecnicamente e legalmente adequado;
- monitorar pressão de memória/address space x86;
- separar memória do OMSI da memória usada pelo Host x64;
- alertar sobre condições associadas a falhas de recursos/texturas.

### 4. Graphics Engine

- backend moderno compatível com o pipeline suportado;
- depth e motion data confiáveis;
- motion clarity aceitável;
- integração temporal/neural validada;
- tonemapping e efeitos próprios essenciais;
- integridade de cockpit, espelhos, chuva, noite, HUD e materiais críticos.

### 5. Performance Engine

- Telemetry Engine;
- classificação de gargalos;
- CPU Budget;
- GPU Budget;
- Simulation Budget;
- Memory/I-O Budget;
- Performance Controller com hysteresis/cooldown;
- recomendações e controles adaptativos apenas onde houver validação suficiente.

### 6. Adaptive Performance

- AI/pedestres quando houver controle runtime seguro;
- Adaptive Mirrors se o mecanismo for demonstrado;
- qualidade gráfica adaptativa;
- perfis Performance/Balanced/Quality/Custom.

### 7. Loading & Streaming Manager

A v1.0 precisa obrigatoriamente:

- observar estados de loading/tiles;
- correlacionar loading com stutter;
- produzir diagnóstico de assets e memória;
- implementar cache/pré-processamento externo quando demonstrar benefício.

A v1.0 **não depende** de conseguir substituir ou controlar diretamente o loader interno do OMSI. Se isso não for seguro, a entrega pode permanecer observacional + otimizações externas comprovadas.

### 8. Diagnostics

- OMSI/version check;
- LAA/address-space status;
- dependências;
- assets ausentes e logfile quando tecnicamente viável;
- D3D/backend;
- depth;
- motion vectors;
- host neural;
- memória/VRAM;
- benchmark summary;
- classificação PASS / WARNING / FAIL.

## Critérios gerais de aprovação

Nenhuma otimização entra na v1.0 somente porque "parece mais rápida". Ela precisa de:

1. baseline reproduzível;
2. alteração isolável;
3. no mínimo três repetições no cenário oficial quando aplicável;
4. métricas de frametime além de FPS médio;
5. teste de regressão visual/funcional;
6. rollback;
7. documentação do resultado.

## Fora do escopo da v1.0

- converter `Omsi.exe` para 64-bit;
- reescrever a engine do OMSI;
- tornar toda a simulação multicore;
- DirectX 12 nativo da engine;
- Vulkan nativo da engine;
- substituir completamente o loader interno;
- path tracing;
- ray tracing completo;
- multiplayer;
- editor completo de mapas;
- editor completo de horários;
- mod manager universal completo;
- backend AMD/Intel com paridade total ao NVIDIA;
- prometer 60 FPS em qualquer hardware/mapa.

Esses itens podem virar pesquisa ou versões futuras sem bloquear a v1.0.

## Regra de mudança de escopo

Um item novo só entra na v1.0 quando:

- resolve problema crítico do produto;
- não duplica ferramenta externa sem justificativa;
- possui caminho técnico plausível;
- tem impacto no cronograma analisado;
- substitui ou rebaixa outro item de prioridade quando necessário.

## Objetivo final da v1.0

Entregar uma experiência em que o usuário instala uma ferramenta central, recebe diagnóstico claro do OMSI, aplica um perfil validado, obtém melhor estabilidade/consistência e uma apresentação gráfica modernizada, sem precisar compreender manualmente toda a cadeia de patches e ferramentas experimentais usada durante o desenvolvimento.
