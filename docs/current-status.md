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
- Falha real de alocação de texturas reproduzida no BusBrasilFest 2025: o logfile registra repetidamente `D3DERR_OUTOFVIDEOMEMORY` e falhas subsequentes em texturas de humanos, veículos, cenário, splines e mapa.

## Em validação crítica

- **Memory/resource pressure:** determinar se o `D3DERR_OUTOFVIDEOMEMORY` está ligado principalmente ao address space x86/Large Address Aware, orçamento de textura do OMSI, wrapper D3D9→D3D11 ou combinação desses fatores.
- **Motion clarity:** perda de nitidez/blur observada durante movimento no cockpit com o pipeline neural; execução técnica do NR está confirmada, mas qualidade temporal ainda não.
- **Assets ausentes:** o logfile também contém referências realmente quebradas/ausentes em mods e cenário; isso é um problema separado das falhas de alocação.
- cockpit, espelhos, chuva e noite;
- benchmarks A/B reproduzíveis;
- impacto real no desempenho total do OMSI;
- custo de AI, pedestres, espelhos e carregamento de tiles.

## Em desenvolvimento

- Infinitum Performance Engine;
- Runtime Bridge x86/x64;
- Telemetry Engine e primeiro protótipo read-only;
- Loading & Streaming Manager e Tile Loading Telemetry;
- External Memory & Cache Manager — pesquisa prioritária após o caso `D3DERR_OUTOFVIDEOMEMORY`;
- Simulation Budget / AI Profiler — pesquisa;
- Diagnostics, incluindo verificação de Large Address Aware, memória do processo, configuração de textura e classificação de erros de assets;
- Configurator;
- módulos gráficos próprios.

## Próximo marco técnico

Antes do benchmark gráfico final, fechar o baseline de memória e recursos:

1. verificar a flag `IMAGE_FILE_LARGE_ADDRESS_AWARE` (`0x20`) do `Omsi.exe` atual e registrar versão/hash;
2. registrar `texmemlimit`, configuração do dgVoodoo e parâmetros relevantes do OMSI;
3. criar telemetria read-only para memória do processo/address space;
4. correlacionar timestamps de pressão de memória com o primeiro `D3DERR_OUTOFVIDEOMEMORY` e falhas de textura;
5. repetir o cenário de teste de forma controlada;
6. depois retomar A/B de qualidade temporal, cockpit e espelhos.

> `D3DERR_OUTOFVIDEOMEMORY` não deve ser interpretado automaticamente como esgotamento da VRAM física da GPU. O projeto precisa medir o limite efetivo do processo e da cadeia D3D antes de atribuir causa.

> Execução técnica não deve ser confundida com qualidade final ou ganho de desempenho validado. O projeto não promete converter o executável do OMSI para 64-bit, distribuir automaticamente a simulação por todos os núcleos ou remover os limites internos da engine x86.
