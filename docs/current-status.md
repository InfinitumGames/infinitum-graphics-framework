# Estado atual

## Confirmado tecnicamente

- OMSI 2 32-bit operando sobre tradução DirectX 9 → DirectX 11.
- Depth buffer acessível e visualmente coerente em 1920×1080.
- Motion vectors reconstruídos pelo LumeniteFX reagindo de forma coerente ao movimento.
- DLSS5-Feeder x86 comunicando-se com host64.
- Host neural inicializando NGX e executando avaliações experimentais de Neural Rendering.
- Instalação-base reproduzida com FeedKit.
- Viabilidade inicial de telemetria runtime identificada para frametime, mapa, tiles, veículos e humanos.
- Arquitetura híbrida x86/x64 adotada como direção do framework.

## Em validação crítica

- **Motion clarity:** perda de nitidez/blur observada durante movimento no cockpit com o pipeline neural; execução técnica do NR está confirmada, mas qualidade temporal ainda não.
- **Integridade de texturas/materiais:** pessoas/objetos brancos ou sem textura foram observados e a causa ainda precisa ser separada entre pressão de memória/address space e falha do pipeline gráfico.
- cockpit, espelhos, chuva e noite;
- benchmarks A/B reproduzíveis;
- impacto real no desempenho total do OMSI;
- custo de AI, pedestres, espelhos e carregamento de tiles.

## Em desenvolvimento

- Infinitum Performance Engine;
- Runtime Bridge x86/x64;
- Telemetry Engine e primeiro protótipo read-only;
- Loading & Streaming Manager e Tile Loading Telemetry;
- External Memory & Cache Manager — pesquisa;
- Simulation Budget / AI Profiler — pesquisa;
- Diagnostics;
- Configurator;
- módulos gráficos próprios.

## Próximo marco técnico

Criar um protótipo próprio que se conecte ao OMSI em modo de leitura e registre, com timestamps, pelo menos:

1. frametime/Timegap;
2. mapa e estado de tiles;
3. quantidade de veículos e humanos;
4. uso de memória do processo e pressão de address space;
5. eventos relevantes para correlação com stutter.

> Execução técnica não deve ser confundida com qualidade final ou ganho de desempenho validado. O projeto não promete converter o executável do OMSI para 64-bit, distribuir automaticamente a simulação por todos os núcleos ou remover os limites internos da engine x86.
