# Estado final

> **Projeto encerrado em 1º de outubro de 2026.** Esta página registra o estado técnico no momento do encerramento e não representa trabalho em andamento.

## Confirmado tecnicamente

- OMSI 2 32-bit operando sobre tradução DirectX 9 → DirectX 11.
- Depth buffer acessível e visualmente coerente em 1920×1080.
- Motion vectors reconstruídos pelo LumeniteFX reagindo de forma coerente ao movimento.
- DLSS5-Feeder x86 comunicando-se com host64.
- Host neural inicializando NGX e executando avaliações experimentais de Neural Rendering.
- Instalação-base reproduzida com FeedKit.
- Large Address Aware confirmado no executável testado.
- Viabilidade inicial de telemetria runtime identificada para frametime, mapa, tiles, veículos e humanos.
- Falha de alocação de texturas reproduzida em conteúdo pesado, com `D3DERR_OUTOFVIDEOMEMORY` registrado no logfile.
- Teste de controle em Berlin-Spandau demonstrou que a mesma cadeia podia operar com carga de texturas significativamente menor sem reproduzir a avalanche de falhas observada no mapa pesado.

## Conclusões de pesquisa

A investigação de memória indicou que o erro de texturas não podia ser atribuído simplesmente à falta de VRAM física. Durante testes problemáticos ainda havia memória física de GPU disponível e o processo não demonstrava, pelas métricas observadas, simples exaustão do address space. A evidência apontou para pressão de recursos no caminho legado D3D9/texture manager/wrapper associada a conteúdo pesado.

O teste de controle também mostrou que quantidade de tiles isoladamente não explica a pressão: resolução, diversidade e peso dos assets, veículos AI, humanos e demais recursos do cenário precisam ser considerados.

Não foi estabelecido um limite universal de memória de texturas para o OMSI. Os valores observados pertencem aos cenários testados e não devem ser generalizados como constante da engine.

## Não concluído ao encerramento

Permaneceram sem implementação própria completa ou validação final:

- Infinitum Performance Engine;
- Runtime Bridge próprio x86/x64;
- Telemetry Engine próprio;
- Loading & Streaming Manager;
- Memory & Resource Manager / Texture Budget Manager;
- Asset & Texture Profiler;
- Adaptive Mirrors;
- Adaptive Quality Manager;
- Configurator;
- módulos gráficos próprios integrados;
- benchmark A/B final de desempenho;
- validação completa de cockpit, espelhos, chuva e noite;
- solução para blur/perda de nitidez temporal do pipeline neural.

## Interpretação correta

A execução técnica de componentes de terceiros e das provas de conceito não equivale a um produto Infinitum final. O projeto demonstrou viabilidade de várias ideias e documentou limitações importantes, mas foi encerrado antes da construção de um software integrado distribuível.

Consulte [Encerramento e retrospectiva](project-retrospective.md) para o registro consolidado.
