# Benchmarks

Os resultados do Infinitum só são aceitos quando obtidos em cenários reproduzíveis. FPS médio isolado não aprova uma otimização.

## Regra de benchmark

Toda mudança relevante deve seguir:

**baseline → alteração → repetição → medição → regressões → conclusão**

Quando aplicável, executar no mínimo **três repetições** por configuração e registrar variação entre runs.

## Métricas obrigatórias

- FPS médio;
- 1% low;
- frametime médio;
- P95 de frametime;
- P99 de frametime;
- número/duração de stutters acima do limite definido;
- CPU total e, quando possível, thread/núcleo crítico;
- GPU usage / GPU time quando disponível;
- RAM do OMSI;
- RAM do Host x64;
- pressão de address space x86;
- VRAM;
- número de veículos AI e humanos quando relevante;
- estados/eventos de tiles quando relevante;
- tempo de loading;
- integridade visual/funcional;
- custo do próprio framework.

## Cenários oficiais iniciais

| ID | Cenário | Objetivo |
|---|---|---|
| BM-001 | mapa leve / cockpit parado | medir overhead mínimo do framework |
| BM-002 | cockpit dirigindo em rota fixa | stutter, temporal stability e motion clarity |
| BM-003 | terminal/área pesada | CPU e Simulation Budget |
| BM-004 | AI 25/50/75/100% + AUXI scaler | curva de custo e comportamento adaptativo |
| BM-005 | mirrors Full/Economical | custo de reflection passes |
| BM-006 | DX9/dgVoodoo/DXVK | comparação de backend |
| BM-007 | loading inicial | tempo e pressão de memória |
| BM-008 | percurso fixo de 15 minutos | stutter real e tile loading |
| BM-009 | chuva + limpadores | transparência, movimento e custo |
| BM-010 | noite urbana | luzes, exposição e estabilidade |
| BM-011 | cenário de pressão de memória | address space, VRAM e falhas de texturas/recursos |

## Reprodutibilidade

Cada cenário deve registrar:

- mapa;
- versão do mapa;
- ônibus;
- variante/repintura;
- ponto inicial;
- rota/linha quando aplicável;
- hora/data/clima;
- opções OMSI;
- AI/pedestres/passengers;
- backend gráfico;
- versão/configuração do framework;
- dependências ativas;
- resolução e modo de tela;
- versão de driver;
- hardware.

Sempre que possível, a execução deve usar checkpoint, rota ou procedimento repetível.

## Qualidade visual é métrica

Uma mudança que melhora FPS mas introduz:

- blur temporal;
- ghosting severo;
- texturas ausentes;
- espelhos quebrados;
- corrupção de HUD;
- shimmering pior;
- regressões em chuva/noite;
- instabilidade/crash;

não deve ser classificada automaticamente como ganho.

## Baseline atual

Em um teste experimental do pipeline via FeedKit, o Feeder reportou cerca de **0,52–0,60 ms por frame** de trabalho de feed em uma situação próxima de 21 FPS. Isso permanece somente como medição preliminar, não benchmark A/B final.

## Armazenamento futuro de resultados

Quando o Telemetry v0.1 existir, cada execução deve gerar dados estruturados em CSV/JSON com:

- metadata da execução;
- samples temporais;
- eventos;
- resumo estatístico;
- versão/hash do build testado.

Isso permitirá comparar builds e detectar regressões automaticamente.
