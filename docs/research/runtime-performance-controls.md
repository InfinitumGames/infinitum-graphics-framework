# Pesquisa — controles de performance em runtime no OMSI 2

> Estado: pesquisa técnica. Objetivo: localizar quais parâmetros e estados internos já possuem mapeamento público e separar telemetria, controle potencial e pontos ainda não encontrados.

## Resumo

A pesquisa encontrou uma diferença importante entre **limites configuráveis no `options.cfg`** e **estados internos realmente expostos em runtime**.

Até aqui, não foram encontrados no OmsiHook campos públicos com os mesmos nomes das opções globais do `options.cfg`, como `AIMaxCountRandom`, `AIMaxCountScheduled`, `performance_maxObjDist`, `performance_tiledistmax`, `maxFPS` ou equivalentes diretos.

Por outro lado, existem diversos estados internos relacionados a tráfego, passageiros, tiles e FPS que podem servir como base para telemetria e, possivelmente, para controles indiretos.

## 1. Tráfego não agendado

A classe `OmsiMap` expõe `UnschedVehGroups`, uma lista de grupos de veículos não agendados.

A estrutura de cada grupo contém campos como:

- `aiList`;
- `aiList_index`;
- `defaultDensity`;
- `densityFactor`;
- `act_trafficDensity`;
- funções de densidade para dias úteis, sábado e domingo.

Isso é relevante porque sugere que a densidade real de tráfego não agendado não existe apenas como um valor global do menu: ela também é representada dentro do estado carregado do mapa.

### Hipótese

`densityFactor` e/ou `act_trafficDensity` podem ser candidatos para controle de densidade em runtime.

Essa hipótese ainda **não está validada**. É possível que esses campos sejam derivados de outras configurações, sejam recalculados pela engine ou representem apenas o estado acumulado da densidade atual.

### Teste futuro

Quando houver acesso ao OMSI:

1. registrar os valores de `defaultDensity`, `densityFactor` e `act_trafficDensity`;
2. alterar o fator de tráfego pelo menu;
3. observar qual campo muda;
4. dirigir por alguns minutos e observar se a engine sobrescreve o valor;
5. somente depois testar escrita isolada e reversível.

## 2. Densidade de passageiros

`OmsiMap` também expõe:

- `Func_TrafficDensity_Passenger`;
- `Func_TrafficDensity_Road`;
- `Acct_TrafficDensity_Passenger`;
- `Acct_TrafficDensity_Road`.

Esses campos reforçam que o mapa mantém estado de densidade tanto para tráfego rodoviário quanto para passageiros.

Os nomes sugerem funções temporais e acumuladores, mas não devemos assumir que os acumuladores sejam os limites máximos configurados pelo usuário.

## 3. Contagem real de veículos e humanos

`OmsiGlobals` expõe diretamente:

- a lista de veículos rodoviários ativos;
- a lista de humanos presentes na cena.

Isso torna possível medir carga real de entidades, mesmo antes de encontrarmos os limites globais configuráveis.

Para o Telemetry Engine, os primeiros campos úteis seriam:

```text
active_road_vehicles
active_humans
unscheduled_vehicle_groups
road_density_state
passenger_density_state
```

Essa telemetria poderá ser correlacionada com CPU, frametime e stutter.

## 4. Tiles: o controle mais promissor

`OmsiMap` já expõe:

- `DynTile_RedTimer`;
- `FDynTileIst`;
- `LoadedKacheln`;
- `CenterKachel`;
- `CenterKachelNum`;
- `NextTime_CheckKachelUnloading`;
- `NewNearKachelnExist`.

Cada `OmsiMapKachel` também expõe:

- `Loaded`;
- `Load_Request`;
- `ThreadLoading`;
- `ThreadLoading_Real`;
- `ThreadLoading_Render`;
- `Failed`.

O principal ponto ainda ausente é localizar o **limite configurado de tiles** equivalente a `[performance_tiledistmax]`.

Por enquanto temos muito acesso ao estado atual, mas não ao parâmetro global que define o máximo.

### Classificação atual

- observação de tiles: **muito alta viabilidade**;
- detecção de loading/stutter: **muito alta viabilidade**;
- alteração direta do limite máximo: **a investigar**;
- manipulação do estado interno de redução dinâmica: **experimental e de risco**.

## 5. FPS interno

`OmsiProgMan` expõe dois campos relevantes:

- `FPS_Time_All`;
- `FPS_Time_BelowLimit`.

Também existe `FPS_ShowedAllReady`.

Esses nomes indicam que o OMSI mantém contadores internos relacionados a tempo total e tempo abaixo de um limite de FPS.

Isso não substitui `Timegap`, mas pode ajudar a estudar a própria lógica interna que decide quando ativar comportamentos adaptativos.

### Hipótese

`FPS_Time_BelowLimit` pode participar de algum mecanismo de persistência temporal, evitando que uma única queda de FPS dispare imediatamente uma mudança de qualidade.

Essa relação ainda precisa ser validada.

## 6. Reflexos e espelhos

A pesquisa pública desta rodada não encontrou no OmsiHook um objeto de opções globais com um campo diretamente equivalente a `[performance_realreflexions]` ou aos limites de reflexos econômicos.

Há estruturas de materiais com flags relacionadas a reflexos em tempo real, como `useRealTimeReflx`, mas isso descreve propriedades de materiais e não deve ser confundido com o modo global de reflexos do OMSI.

Portanto:

- telemetria de materiais refletivos: possível;
- controle do modo global de reflexos: ainda não localizado;
- controle de espelhos individuais: requer pesquisa específica em estruturas de veículo/câmera/renderização.

## 7. Distância de visão e complexidade

Não foram encontrados nesta rodada campos públicos equivalentes a:

- `performance_maxObjDist`;
- `performance_minObjSize`;
- `maxcomplexity`;
- `maxcomplexity_map`.

Esses controles continuam como candidatos para descoberta por comparação de memória quando estivermos no PC.

A estratégia será usar valores fáceis de distinguir, por exemplo 500 m, 700 m ou 1000 m, e observar quais endereços mudam quando a opção é alterada pelo menu.

## 8. Limites globais de AI e pedestres

Também não apareceram diretamente no código público os equivalentes runtime de:

- `AIMaxCountRandom`;
- `AIMaxCountScheduled`;
- `AIMaxCountParked`;
- `AIPassFactor`;
- `AIPriorityScheduled`.

Isso não significa que não existam na memória. Significa apenas que o OmsiHook atual não os mapeia com esses nomes.

### Consequência

O primeiro protótipo do CPU Budget deve começar pela **observação da carga real** em vez de tentar controlar limites ainda desconhecidos.

## 9. Nova classificação de controles

| Recurso | Estado observável | Controle runtime encontrado | Situação |
|---|---:|---:|---|
| FPS/frametime | Sim | N/A | Muito alta viabilidade |
| Veículos ativos | Sim | Não | Telemetria pronta |
| Humanos ativos | Sim | Não | Telemetria pronta |
| Grupos de tráfego não agendado | Sim | Possível via `densityFactor` | Promissor, validar |
| Densidade rodoviária | Sim | Incerto | Promissor |
| Densidade de passageiros | Sim | Incerto | Promissor |
| Tiles carregados | Sim | Estado interno gravável | Alta, mas cuidado |
| Limite máximo de tiles | Indireto | Não localizado | Pesquisar memória |
| Reflexos globais | Não | Não localizado | Pesquisa específica |
| Distância de visão | Não | Não localizado | Pesquisa por memória |
| Complexidade | Não | Não localizado | Pesquisa por memória |
| AI agendada | Parcial pela lista de veículos | Não localizado | Pesquisa específica |
| Pedestres máximos | Parcial pela lista de humanos | Não localizado | Pesquisa específica |

## 10. Impacto na arquitetura do Performance Engine

A partir desta pesquisa, o Adaptive Quality Manager deve trabalhar com três estados por controle:

```text
ControlCapability
├─ ObserveOnly
├─ ExperimentalWritable
└─ RuntimeSafe
```

Nenhum parâmetro deve nascer como `RuntimeSafe` apenas porque um endereço pode ser escrito.

Exemplo:

```text
traffic_density
state = ExperimentalWritable
source = OmsiMap.UnschedVehGroups[*].densityFactor
validation = pending
rollback = required
```

## 11. Próxima pesquisa

A próxima rodada deve se concentrar em **espelhos/reflexos e estruturas de renderização do veículo**.

Objetivos:

1. localizar no OmsiHook classes relacionadas a câmeras de espelho e render targets;
2. identificar se cada espelho possui estado, textura ou frequência de atualização acessível;
3. verificar se existe alguma ponte entre a lógica global de reflexos e as câmeras dos veículos;
4. separar reflexos de ambiente, reflexos de material e espelhos reais do OMSI.

Se essa frente for viável, `Adaptive Mirrors` pode se tornar um dos primeiros controles GPU/CPU realmente específicos do Infinitum Performance Engine.
