# Performance Controller

O **Performance Controller** recebe o `Frame Performance State` produzido pelo Telemetry Engine e transforma esse estado em uma decisão de alto nível.

## Responsabilidade

O controlador não mede métricas diretamente e não altera opções do OMSI por conta própria. Ele decide entre:

- **Hold** — manter a configuração atual;
- **Reduce Cost** — reduzir custo quando a degradação é sustentada;
- **Recover Quality** — recuperar qualidade quando existe margem estável;
- **Protect** — evitar novas alterações durante stutter severo, carregamento ou baixa confiança de diagnóstico.

## Entradas principais

```text
PerformanceControllerInput
├─ target_frametime_ms
├─ avg_frametime_ms
├─ p95_frametime_ms
├─ p99_frametime_ms
├─ stutter_score
├─ cpu_pressure
├─ gpu_pressure
├─ memory_pressure
├─ io_pressure
├─ confidence
└─ current_profile
```

Os nomes são conceituais e poderão mudar na implementação.

## Hysteresis e estabilidade

Uma queda isolada não deve provocar alteração automática. A decisão deve considerar:

- janela curta para picos e stutter;
- janela longa para degradação sustentada;
- limiar diferente para reduzir custo e recuperar qualidade;
- cooldown após cada alteração;
- quantidade máxima de mudanças dentro de uma janela de tempo.

Exemplo:

```text
frametime acima do alvo
    ↓
condição sustentada?
    ├─ não → Hold
    └─ sim
        ↓
confiança suficiente?
        ├─ não → Protect / Hold
        └─ sim → Reduce Cost
```

## Classificação de pressão

A primeira versão deve distinguir pelo menos:

- **CPU-bound provável**;
- **GPU-bound provável**;
- **Memory pressure**;
- **I/O / loading pressure**;
- **Unknown / mixed**.

A classificação não deve ser tratada como certeza quando os dados forem insuficientes. O campo de confiança deve acompanhar a decisão.

## Política de recuperação

Recuperar qualidade deve ser mais lento que reduzir custo. Isso evita oscilações perceptíveis quando o desempenho fica próximo do limite.

Princípios iniciais:

1. reduzir apenas um nível por vez;
2. medir novamente depois da alteração;
3. recuperar somente após margem sustentada;
4. não recuperar durante eventos recentes de stutter;
5. registrar motivo e efeito de cada decisão.

## Saída conceitual

```text
DecisionState
├─ action: hold | reduce | recover | protect
├─ bottleneck: cpu | gpu | memory | io | mixed | unknown
├─ severity
├─ confidence
├─ reason
└─ cooldown_remaining
```

## Estado da implementação

A arquitetura está definida, mas os thresholds finais só serão calibrados com benchmarks reproduzíveis no OMSI 2.

A regra central permanece: **medir primeiro, decidir depois e automatizar apenas controles validados**.
