# Telemetry Engine

O **Telemetry Engine** transforma métricas brutas em informações úteis para o Performance Controller.

## Métricas prioritárias

### Fase 1 — obrigatórias

- FPS instantâneo;
- frametime por frame;
- média móvel de frametime;
- picos de frametime;
- contagem e intensidade de stutters.

### Fase 2 — desejáveis

- uso de CPU;
- uso por núcleo quando disponível;
- uso de GPU;
- RAM;
- VRAM;
- atividade de disco / I/O;
- custo dos módulos próprios do Infinitum.

## Frame Performance State

O controlador não deve consumir dezenas de valores crus diretamente. O Telemetry Engine produzirá um estado normalizado.

```text
FramePerformanceState
├─ target_frametime_ms
├─ avg_frametime_ms
├─ p95_frametime_ms
├─ p99_frametime_ms
├─ stutter_score
├─ cpu_pressure
├─ gpu_pressure
├─ memory_pressure
├─ io_pressure
└─ confidence
```

Os nomes acima são um contrato conceitual, não uma API final.

## Estados propostos

- **Stable** — desempenho dentro do orçamento;
- **Pressured** — acima do alvo por período sustentado;
- **Critical** — degradação severa ou stutter persistente;
- **Recovering** — margem suficiente para recuperar qualidade;
- **Unknown** — telemetria insuficiente para uma decisão segura.

## Stutter Score

O stutter deverá ser tratado separadamente do FPS médio. A pontuação poderá considerar quantidade e magnitude de frames muito acima da média recente.

A fórmula final ainda será definida após coleta de dados reais no OMSI.

## Requisitos

- baixo overhead;
- timestamps monotônicos;
- dados exportáveis para benchmark;
- logging opcional;
- ausência de decisões de qualidade dentro do próprio coletor.

O Telemetry Engine mede. O Performance Controller decide.
