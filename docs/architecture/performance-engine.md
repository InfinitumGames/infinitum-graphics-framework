# Infinitum Performance Engine — Architecture v0.1

O **Infinitum Performance Engine** é o pilar responsável por desempenho, estabilidade e adaptação de carga do Infinitum Graphics Framework. O objetivo não é apenas aumentar o FPS médio: a prioridade é reduzir picos de frametime, identificar gargalos e manter uma experiência consistente no OMSI 2.

## Objetivos da v0.1

A primeira versão da arquitetura deve:

- medir o comportamento do OMSI de forma confiável;
- transformar telemetria em um estado de desempenho simples;
- comparar esse estado com um Target FPS / Target Frametime;
- classificar o gargalo predominante;
- recomendar ou selecionar um perfil adequado;
- registrar decisões para diagnóstico e benchmark.

A v0.1 **não pressupõe** que todas as opções do OMSI possam ser alteradas em runtime. A capacidade real de modificar cada parâmetro será pesquisada e validada separadamente.

## Pipeline conceitual

```text
OMSI 2
   ↓
Telemetry Engine
   ↓
Frame Performance State
   ├─ FPS
   ├─ Frametime
   ├─ Stutter score
   ├─ CPU load
   ├─ GPU load
   ├─ RAM / VRAM
   └─ I/O
   ↓
Performance Controller
   ↓
Target FPS / Target Frametime
   ↓
Decision State
   ├─ Recover quality
   ├─ Hold
   └─ Reduce cost
   ↓
Adaptive Quality Manager
   ↓
Controles seguros e validados
```

## Módulos

### Telemetry Engine

Coleta e normaliza métricas. Na primeira implementação, FPS e frametime são obrigatórios. CPU, GPU, memória, VRAM e I/O entram progressivamente conforme a fonte de dados for definida.

### Performance Controller

Recebe o estado consolidado e decide se o sistema deve manter a qualidade, reduzir custo ou recuperar qualidade. O controlador deve evitar reações impulsivas a frames isolados.

### Adaptive Quality Manager

Aplica somente ajustes classificados como seguros. Cada controle terá capacidade, limites, prioridade e custo conhecidos.

### Performance Profiles

Perfis planejados:

| Perfil | Prioridade |
|---|---|
| Performance | estabilidade e baixo custo |
| Balanced | equilíbrio entre qualidade e frametime |
| Quality | preservar qualidade, reduzindo apenas quando necessário |
| Custom | limites definidos pelo usuário |

### Loading & Streaming Manager

Trabalha em conjunto com o Performance Engine para identificar stutters relacionados a I/O, carregamento de tiles, texturas, objetos e listas de AI. É uma frente de pesquisa separada porque o grau de acesso ao loader interno do OMSI ainda é desconhecido.

## Política de decisão

O controlador não deve reduzir qualidade após uma queda isolada de FPS. A arquitetura prevê:

- média móvel de frametime;
- janela curta para detectar stutter;
- janela longa para detectar degradação sustentada;
- hysteresis entre reduzir e recuperar qualidade;
- cooldown entre alterações;
- limites mínimos e máximos por recurso.

Exemplo conceitual:

```text
frametime acima do alvo por período sustentado
        ↓
identificar provável gargalo
        ↓
selecionar ajuste de menor impacto visual
        ↓
aplicar um nível
        ↓
aguardar estabilização
        ↓
medir novamente
```

## Separação CPU e GPU

A arquitetura deve evitar uma única escala genérica de "qualidade". Ajustes devem ser classificados por custo predominante.

| Categoria | Exemplos planejados |
|---|---|
| CPU / engine | AI, tráfego, pedestres, objetos, distância, tiles |
| GPU | efeitos Infinitum, reflexos, resolução de efeitos, Neural Rendering |
| CPU + GPU | espelhos, distância de visão, complexidade de cena |
| Memória / I/O | texturas, assets, cache, carregamento |

## Matriz inicial de capacidade

| Recurso | Custo principal | Estado de controle |
|---|---|---|
| Efeitos próprios do Infinitum | GPU | controlável por nós |
| Neural Rendering | GPU | integração experimental existente |
| Distância de visão | engine / CPU / GPU | pesquisar runtime |
| Tiles vizinhos | engine | pesquisar runtime |
| Tráfego AI | CPU | pesquisar runtime |
| Pedestres | CPU | pesquisar runtime |
| Carros estacionados | CPU / engine | pesquisar runtime |
| Espelhos | CPU / GPU | alto potencial, precisa pesquisa |
| Texturas | VRAM / I/O | parcialmente controlável, precisa pesquisa |
| Carregamento de assets | I/O / engine | pesquisa profunda |

## Critérios de sucesso do MVP v0.1

O MVP será considerado funcional quando conseguir, de forma reproduzível:

1. medir FPS e frametime;
2. detectar degradação sustentada e stutter;
3. gerar um `Frame Performance State`;
4. classificar o cenário como estável, pressionado ou crítico;
5. registrar a causa provável quando houver dados suficientes;
6. recomendar um perfil ou ação sem alterar parâmetros não validados.

## Próxima evolução — v0.2

A v0.2 poderá ativar ajustes automáticos de runtime **somente para controles que tenham sido testados no OMSI 2 e classificados como seguros**.

A prioridade de desenvolvimento será sempre: medir → entender → validar → automatizar.
