# Pesquisa — OmsiHook e controle runtime do OMSI 2

> Estado: pesquisa técnica. Esta página analisa o OmsiHook como referência e possível camada de integração para o Infinitum Performance Engine e o Loading & Streaming Manager.

## Conclusão rápida

OmsiHook é mais promissor do que parecia na primeira análise. Ele não expõe apenas veículo e câmera: a biblioteca já mapeia estruturas centrais do mapa, tiles, tráfego, humanos, gerenciadores de textura/material e estados de carregamento. Também possui leitura e escrita de memória em tempo real, além de suporte a métodos remotos por plugin auxiliar.

Isso não significa que todos os controles do Performance Engine estejam prontos para uso. Significa que já existe uma base pública de engenharia reversa que reduz bastante o trabalho inicial de descoberta.

## Arquitetura do OmsiHook

O ponto de entrada é `OmsiHook`, que anexa ao processo `omsi.exe` e expõe:

- `Globals` — objetos globais já reconhecidos;
- `OmsiProcess` — processo do OMSI;
- `RemoteMethods` — ponte para chamadas remotas;
- eventos de estado do mapa e do contexto D3D.

A biblioteca suporta leitura e escrita de memória em runtime. O projeto também mantém um plugin RPC para operações que exigem execução dentro do processo do OMSI.

## Globais já expostos

`OmsiGlobals` atualmente fornece acesso direto a estruturas como:

- veículos rodoviários ativos;
- veículo do jogador;
- clima;
- mapa atual;
- data e hora;
- humanos presentes na cena;
- gerenciador de horários;
- Program Manager;
- câmera principal;
- Material Manager;
- Texture Manager.

### Relevância para o Infinitum

Isso é suficiente para construir um primeiro `RuntimeContext` contendo mapa, veículo, posição, câmera, quantidade de entidades e estado da sessão sem precisar descobrir tudo do zero.

## Estruturas do mapa relevantes para performance

A classe `OmsiMap` expõe vários campos especialmente importantes.

### Dynamic Tile Reduction

Campos mapeados:

- `DynTile_RedTimer`;
- `FDynTileIst`;
- `NextTime_CheckKachelUnloading`;
- `NewNearKachelnExist`;
- `LoadedKacheln`;
- `CenterKachel`;
- `CenterKachelNum`.

Esses campos reforçam que o mecanismo de tiles dinâmicos realmente existe como estado interno acessível.

**Hipótese de pesquisa:** `FDynTileIst` pode representar o estado corrente da redução dinâmica de tiles. Isso precisa ser validado localmente antes de qualquer escrita.

### Loading / Streaming

O mapa contém:

- array `Kacheln`;
- array `KachelInfos`;
- flag global `Loaded`;
- referência a uma estrutura de thread de tile ainda não mapeada no projeto (`ThreadTileLoadAndRefresh`, marcada como TODO).

Isso torna o subsistema de tiles a primeira frente concreta do futuro Loading & Streaming Manager.

## Estrutura de cada tile

`OmsiMapKachel` expõe estados muito interessantes para diagnóstico de carregamento:

- `Loaded`;
- `Load_Request`;
- `Failed`;
- `ThreadLoading`;
- `ThreadLoading_Real`;
- `ThreadLoading_Render`;
- `Prepared_For_Ode`;
- `Splines_Refreshed`;
- `Objects_Refreshed`;
- `Paths_Refreshed`.

Também permite enumerar:

- objetos do arquivo;
- splines do arquivo;
- objetos runtime;
- path groups;
- path segments;
- objetos de superfície;
- spline segments.

### Valor para telemetria

Sem alterar nada, o Infinitum pode potencialmente observar:

```text
TileTelemetry
├─ loaded_tiles
├─ requested_tiles
├─ tiles_loading
├─ tiles_loading_real
├─ tiles_loading_render
├─ failed_tiles
├─ objects_per_tile
├─ splines_per_tile
└─ path_segments_per_tile
```

Isso abre a possibilidade de correlacionar picos de frametime com eventos reais de carregamento de tile em vez de apenas chamar qualquer queda de FPS de "stutter".

## Tráfego e pedestres

`OmsiMap` possui funções e acumuladores relacionados à densidade de tráfego:

- `Func_TrafficDensity_Passenger`;
- `Func_TrafficDensity_Road`;
- `Acct_TrafficDensity_Passenger`;
- `Acct_TrafficDensity_Road`;
- `UnschedVehGroups`.

`OmsiGlobals` também expõe:

- lista de veículos ativos;
- lista de humanos presentes na cena.

Isso melhora bastante a viabilidade do `CPU Budget`: mesmo que o controle global de AI ainda não esteja mapeado, já podemos medir a quantidade real de entidades presentes e estudar como ela se relaciona com frametime.

## Texture Manager e Material Manager

O OmsiHook expõe os gerenciadores centrais de textura e material. O exemplo oficial também enumera itens do Texture Manager e demonstra manipulação de texturas Direct3D.

Isso pode ser útil para:

- telemetria de quantidade de texturas carregadas;
- investigação de cache;
- diagnóstico de assets pesados;
- futura integração do Graphics Engine.

Não devemos assumir ainda que isso permite controlar o orçamento de VRAM do OMSI com segurança.

## D3D e métodos remotos

OmsiHook monitora o estado do contexto Direct3D e pode inicializar hooks D3D através de métodos remotos. O projeto também possui uma classe `D3DTexture` capaz de envolver texturas `IDirect3DTexture9` existentes e atualizar conteúdo.

Para o Infinitum isso é relevante como referência arquitetural, mas nossa stack gráfica atual passa por dgVoodoo2/D3D11/ReShade. Portanto, não devemos adicionar outro caminho gráfico ao runtime sem necessidade.

## Compatibilidade e arquitetura de processo

O projeto Omsi-Extensions é voltado ao OMSI 32-bit e sua documentação de build indica Windows x86_32. Isso combina com a arquitetura do `Omsi.exe`, mas influencia a nossa implementação.

Uma arquitetura possível é:

```text
Omsi.exe (32-bit)
   ↓
Infinitum Runtime Bridge x86
   ├─ telemetria OMSI
   ├─ leitura de estruturas internas
   └─ comandos runtime validados
        ↓ IPC
Infinitum Configurator / Diagnostics x64
```

Assim, a interface principal não precisa ficar presa ao processo x86.

## Dependência ou referência?

Há três opções:

### 1. Usar OmsiHook diretamente

Vantagens:

- acelera o protótipo;
- mapeamentos já prontos;
- leitura/escrita estruturada;
- suporte a RPC.

Riscos:

- dependência externa central;
- necessidade de revisar licença e redistribuição;
- estabilidade ligada aos offsets e à versão do OMSI;
- áreas ainda incompletas/TODO.

### 2. Usar apenas como referência de pesquisa

Criar uma camada própria do Infinitum usando os conceitos e estruturas que forem legalmente utilizáveis e tecnicamente reproduzidos.

Vantagens:

- maior controle arquitetural;
- dependência runtime menor;
- API desenhada especificamente para Performance/Streaming.

Custo:

- mais engenharia reversa e manutenção.

### 3. Abordagem híbrida — recomendação atual

Usar OmsiHook na fase experimental para descobrir e validar estruturas. Depois decidir, campo por campo, quais mecanismos entram numa camada própria `Infinitum Runtime Bridge`.

## Prioridade de testes quando houver acesso ao PC

1. anexar ao OMSI apenas em modo leitura;
2. confirmar mapa, câmera e veículo;
3. registrar `LoadedKacheln` e `Kacheln.Length`;
4. dirigir atravessando limites de tiles;
5. registrar `Load_Request`, `ThreadLoading*` e `Loaded` por tile;
6. correlacionar esses eventos com frametime;
7. contar veículos ativos e humanos;
8. correlacionar densidade de entidades com frametime;
9. observar `FDynTileIst` durante quedas de FPS;
10. somente depois iniciar testes de escrita em campos isolados e reversíveis.

## Primeira consequência arquitetural

A pesquisa sugere adicionar ao Telemetry Engine um canal específico chamado provisoriamente `OmsiRuntimeTelemetry`:

```text
OmsiRuntimeTelemetry
├─ map_loaded
├─ loaded_tiles
├─ loading_tiles
├─ failed_tiles
├─ active_road_vehicles
├─ active_humans
├─ player_tile
├─ center_tile
├─ dynamic_tile_state
└─ d3d_context_ready
```

Esse canal não substitui métricas de CPU/GPU/frametime. Ele explica **o que a engine estava fazendo** quando o desempenho mudou.

## Estado de viabilidade atualizado

| Área | Antes | Após análise do OmsiHook |
|---|---|---|
| Telemetria runtime | Alta | Muito alta |
| Contagem de AI/humanos | Incerta | Alta |
| Estado de tiles | Média | Muito alta |
| Diagnóstico de loading | Baixa | Alta |
| Dynamic Tile research | Alta | Muito alta |
| Tráfego adaptativo | Incerta | Média, exige localização do controle correto |
| Streaming customizado | Baixa | Média/experimental |
| Texture diagnostics | Média | Alta para observação |

## Próxima investigação

A próxima pesquisa deve mirar dois pontos:

1. localizar no código/mapeamentos públicos os objetos que representam opções globais e limites de AI/performance;
2. estudar como o OMSI executa o ciclo de carregamento/descarregamento de tiles e quais métodos internos já foram identificados pelo OmsiHook.

A regra continua a mesma: **observar primeiro, escrever depois**.
