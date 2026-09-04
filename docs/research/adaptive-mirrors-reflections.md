# Pesquisa — Adaptive Mirrors e câmeras de reflexão do OMSI 2

> Estado: pesquisa técnica. Objetivo: determinar se espelhos e câmeras internas podem se tornar um dos primeiros subsistemas adaptativos do Infinitum Performance Engine.

## Conclusão rápida

Os espelhos do OMSI 2 são um alvo muito promissor para otimização adaptativa porque não são uma simples textura estática: cada câmera de reflexão exige uma renderização adicional da cena. Evidências públicas da comunidade e arquivos `.bus` mostram que o OMSI já possui mecanismos próprios para reduzir o custo dessas câmeras por visibilidade, distância e frequência de atualização.

A descoberta mais importante é que o Infinitum provavelmente não precisa inventar o conceito de Adaptive Mirrors do zero. O OMSI já contém uma política rudimentar. Nosso trabalho pode ser observar, medir e futuramente controlar essa política de forma mais inteligente.

## Como os espelhos são definidos

Nos arquivos `.bus`, câmeras de reflexão aparecem através de entradas como:

```text
[add_camera_reflexion]
```

ou

```text
[add_camera_reflexion_2]
```

Os parâmetros encontrados em exemplos públicos incluem posição XYZ, parâmetros de câmera/FOV/rotação e, no formato `_2`, um parâmetro adicional relacionado à distância máxima entre a câmera do usuário e a câmera de reflexão para que ela seja renderizada.

Exemplo conceitual:

```text
[add_camera_reflexion_2]
X
Y
Z
...
maximum_render_distance
```

Isso significa que parte da decisão de renderizar uma câmera pode ser feita antes de desenhar seu conteúdo.

## Render targets de reflexão

Documentação e exemplos de veículos descrevem que as câmeras são renderizadas em sequência para texturas chamadas conceitualmente:

```text
reflexion0.bmp
reflexion1.bmp
reflexion2.bmp
...
```

Essas texturas são então utilizadas pelos materiais dos espelhos/monitores.

Para o Performance Engine, isso sugere a seguinte unidade lógica:

```text
ReflectionCamera
├─ index
├─ position
├─ orientation
├─ field_of_view
├─ render_distance
├─ target_texture
├─ visible
├─ update_interval
└─ estimated_cost
```

Nem todos esses campos estão atualmente mapeados em OmsiHook; alguns representam a arquitetura que queremos descobrir.

## Política interna de atualização

Relatos técnicos antigos da comunidade descrevem uma otimização adicional: múltiplos espelhos podem ser atualizados em frequências diferentes. O primeiro pode receber atualizações mais frequentes e câmeras posteriores progressivamente menos frequentes.

Isso é especialmente importante porque transforma o problema em algo além de simplesmente `mirror on/off`.

Uma política futura poderia escolher entre:

```text
FULL RATE
1/2 RATE
1/4 RATE
LOW RATE
ON DEMAND
OFF
```

Precisamos validar experimentalmente como a versão atual do OMSI 2 implementa essa lógica antes de assumir intervalos específicos.

## `_2`, visibilidade e distância

Há documentação comunitária conflitante sobre a nomenclatura histórica de `[add_camera_reflexion]` e `[add_camera_reflexion_2]`, mas existe forte evidência prática de que a variante `_2` acrescenta um oitavo parâmetro de distância máxima de renderização.

A interpretação operacional mais segura para o Infinitum é:

- detectar o tipo de entrada;
- preservar a configuração original do veículo;
- medir quando cada câmera começa e deixa de renderizar;
- não alterar automaticamente arquivos `.bus` na primeira implementação.

Isso evita depender de descrições comunitárias contraditórias sobre qual variante significa “sempre ativa”.

## Evidência no OmsiHook

A pesquisa no Omsi-Extensions não encontrou até agora uma classe pública específica como `OmsiReflectionCamera` ou um array de câmeras de espelho já mapeado.

Por outro lado, estruturas de materiais contêm indicadores diretamente relacionados a reflexos:

```text
useRealTimeReflx
useReflxMap
reflxMap
reflxMapUsesAlpha
envMap
envMaskMap
envFactor
```

Isso confirma que propriedades de reflexão fazem parte do estado interno de materiais já parcialmente conhecido pelo OmsiHook.

OmsiHook também possui infraestrutura para observar o contexto Direct3D9 e trabalhar com objetos `IDirect3DTexture9` existentes. Isso pode ajudar a identificar as texturas utilizadas pelas câmeras de reflexão sem modificar o pipeline gráfico.

## Primeira arquitetura para Adaptive Mirrors

```text
Omsi Runtime
   ↓
Reflection Observer
   ├─ detectar câmeras/reflection targets
   ├─ medir atividade de cada target
   ├─ medir custo de frame
   └─ relacionar target ↔ material/espelho
        ↓
Mirror Budget Controller
   ├─ prioridade do espelho
   ├─ visibilidade
   ├─ distância
   ├─ frametime atual
   └─ orçamento de renderização
        ↓
Policy
   ├─ manter full rate
   ├─ reduzir update rate
   ├─ atualizar sob demanda
   └─ suspender câmera não necessária
```

## Prioridade por função

Nem todas as câmeras têm o mesmo valor para o motorista. Uma política possível, ainda a validar, seria:

| Classe | Exemplos | Prioridade |
|---|---|---|
| Segurança primária | espelho esquerdo/direito principal | crítica |
| Segurança secundária | espelho frontal/curb mirror | alta |
| Interior | espelho do salão | média |
| Monitor auxiliar | câmera interna/CCTV | baixa a média |
| Fora da visão | câmera atualmente invisível | mínima |

A prioridade real precisa considerar o veículo e não deve ser determinada apenas pela ordem das câmeras no arquivo.

## Relação com frametime

Adaptive Mirrors deve reagir a **frametime**, não simplesmente a FPS instantâneo.

Exemplo conceitual:

```text
Target: 30 FPS
Budget: 33.3 ms

Frame 24 ms → espelhos podem manter qualidade
Frame 31 ms → iniciar economia preventiva
Frame 38 ms → reduzir câmeras secundárias
Frame 50 ms → preservar somente câmeras essenciais
```

Os limites acima são apenas ilustrativos. Os valores reais serão definidos por benchmark.

## Estratégia segura de implementação

### Fase 1 — observação

Nenhuma escrita em memória.

Registrar:

```text
frame_time
camera/view do jogador
texturas de reflexão ativas
quantidade de reflection targets
mudanças de conteúdo dos targets
posição do veículo
```

### Fase 2 — caracterização

Comparar:

- reflexos OMSI `Complete`;
- modos econômicos;
- diferentes quantidades de câmeras;
- diferentes resoluções de textura de reflexão;
- câmeras dentro e fora da visão.

### Fase 3 — controle experimental

Somente depois localizar o estado interno responsável por:

- habilitação da câmera;
- intervalo/frequência de atualização;
- distância/visibilidade;
- resolução do render target, se alterável com segurança.

### Fase 4 — Adaptive Mirror Controller

Aplicar mudanças temporárias e reversíveis durante a execução, sem modificar permanentemente o veículo.

## Teste local proposto

Quando houver acesso ao PC:

1. escolher um ônibus com pelo menos três câmeras de reflexão;
2. usar cenário fixo e câmera do cockpit;
3. registrar FPS/frametime com reflexos completos;
4. registrar novamente com reflexos econômicos;
5. localizar `reflexion0`, `reflexion1`, `reflexion2` no Texture Manager ou no pipeline D3D;
6. observar quais texturas mudam a cada frame;
7. girar a câmera para esquerda/direita;
8. observar se targets deixam de atualizar;
9. medir intervalo de atualização individual;
10. correlacionar cada atualização com custo de frame.

Esse teste pode revelar a política interna de espelhos sem nenhuma engenharia reversa destrutiva.

## O que já podemos afirmar

**Alta confiança:**

- cada câmera de reflexão representa renderização adicional;
- veículos podem possuir várias câmeras;
- existem configurações por câmera nos arquivos `.bus`;
- a variante `_2` possui parâmetro adicional usado para limitar renderização por distância;
- reduzir câmeras/reflexos pode melhorar significativamente o desempenho;
- materiais internos já possuem flags relacionadas a reflexão mapeadas pelo OmsiHook.

**Ainda não confirmado:**

- endereço runtime do array de câmeras de reflexão;
- endereço do contador/intervalo de atualização;
- possibilidade segura de mudar frequência em runtime;
- possibilidade segura de redimensionar render targets;
- custo exato por câmera;
- política exata usada pelo OMSI 2.2.32.0 para escalonar múltiplos espelhos.

## Consequência para o Performance Engine

Adaptive Mirrors passa de ideia genérica para **subsistema tecnicamente plausível**.

Ele também é um bom candidato para ser o primeiro controlador adaptativo real porque possui três vantagens:

1. custo potencialmente alto e relativamente isolável;
2. qualidade pode ser reduzida de maneira seletiva;
3. o próprio OMSI já possui mecanismos de economia que podemos estudar em vez de substituir toda a renderização.

## Próxima investigação recomendada

A próxima pesquisa deve aprofundar o pipeline D3D9 de reflection render targets:

- identificar chamadas `CreateTexture`/`SetRenderTarget` relacionadas aos espelhos;
- descobrir dimensões e formatos dos targets;
- determinar a sequência de renderização por câmera;
- verificar se `reflexionN` pode ser associado de forma confiável a cada câmera do `.bus`;
- medir quantos passes extras de cena cada espelho realmente provoca.

A regra permanece: **observar primeiro, medir depois, controlar por último**.
