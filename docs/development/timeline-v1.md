# Cronograma interno — v1.0

Data-base: **11 de setembro de 2026**.

As datas abaixo são metas internas de engenharia, não promessa pública. Uma fase pode escorregar se os critérios técnicos não forem cumpridos.

| Fase | Período | Entrega principal | Critério de saída |
|---|---|---|---|
| F0 — Especificação | 11–20 set 2026 | escopo v1.0 congelado | objetivos e não-objetivos documentados |
| F1 — Tool Survey | 11–30 set 2026 | catálogo técnico das ferramentas OMSI | prioridades, licenças e testes definidos |
| F2 — Baseline Lab | 21 set–11 out 2026 | metodologia oficial de benchmark | cenários reproduzíveis e três runs consistentes |
| F3 — Telemetry v0.1 | 1–31 out 2026 | primeiro componente próprio read-only | Timegap, mapa, tiles, veículos, humanos e memória em CSV/JSON |
| F4 — Runtime Bridge | 1–30 nov 2026 | x86 ↔ x64 com IPC | sessão prolongada estável e overhead conhecido |
| F5 — Performance Engine v0.1 | 1 dez 2026–15 jan 2027 | diagnóstico de gargalos | classificação CPU/GPU/Simulation/Memory/I-O reproduzível |
| F6 — Adaptive Performance | 16 jan–28 fev 2027 | controles adaptativos validados | histerese/cooldown e ganho sem quebra da simulação |
| F7 — Loading & Memory | mar–abr 2027 | tile telemetry + memory diagnostics + cache seguro | redução mensurável de stutter/falhas quando aplicável |
| F8 — Graphics v0.5 | mai–jun 2027 | pipeline visual moderno estável | motion clarity e integridade visual aprovadas |
| F9 — Integration Alpha | jul–ago 2027 | Configurator + módulos integrados | instalação/rollback e sessões longas estáveis |
| F10 — Beta | set–out 2027 | testes comunitários | sem bloqueadores críticos |
| v1.0 | nov 2027 | primeira versão pública estável | critérios de release cumpridos |

## Trabalho paralelo

A partir de setembro de 2026 existem dois tracks permanentes:

### Track A — Research

- ferramentas existentes;
- formatos internos;
- OmsiHook/API oficial;
- memória;
- loader/tiles;
- AI/simulation;
- espelhos;
- render backends;
- dependências e licenças.

### Track B — Development & Validation

- protótipos próprios;
- instrumentação;
- testes A/B;
- benchmark automation;
- regressões;
- documentação dos resultados.

Pesquisa não deve bloquear protótipos read-only; protótipos não devem virar feature pública antes de pesquisa e validação suficientes.

## Gate obrigatório entre fases

Cada fase termina com uma decisão:

- **PASS** — pode avançar;
- **PASS WITH LIMITATIONS** — avança com limitações documentadas;
- **REWORK** — repete teste/desenvolvimento;
- **DROP** — mecanismo descartado e alternativa avaliada.

## Primeira grande entrega

**Infinitum v0.1 — Foundation & Telemetry**

Meta interna: **31 de outubro de 2026**.

Deve conseguir observar o OMSI e responder com dados objetivos:

- quanto tempo cada frame leva;
- qual mapa está ativo;
- quais tiles estão carregando;
- quantos veículos/humanos estão ativos;
- quanta memória o processo está usando;
- quando um stutter ocorreu;
- quais eventos de loading estavam próximos do stutter.

Esse milestone não precisa melhorar FPS ainda. Ele precisa transformar o OMSI de uma caixa-preta em um sistema mensurável.
