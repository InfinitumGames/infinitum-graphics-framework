# Simulation Budget

O Simulation Budget separa gargalos de simulação dos gargalos gráficos. Reduzir resolução ou shaders não resolve um frame limitado por AI, pedestres, scripts ou lógica da engine.

## Categorias

- veículos AI agendados;
- veículos AI não agendados;
- veículos estacionados;
- pedestres;
- passageiros em paradas;
- prioridade de veículos agendados;
- scripts de veículos/objetos;
- complexidade da cena relacionada à simulação.

## Telemetria antes do controle

O primeiro objetivo é construir curvas de custo. Exemplo conceitual:

```text
AI 25%  → frametime X
AI 50%  → frametime Y
AI 75%  → frametime Z
AI 100% → frametime W
```

O mesmo deve ser feito separadamente para pedestres, passageiros e outras categorias.

## Controle adaptativo futuro

Quando um controle runtime for comprovadamente seguro, o Performance Controller poderá aplicar histerese e cooldown para evitar oscilações constantes.

```text
CPU estável     → qualidade/densidade normal
CPU pressionada → redução gradual
CPU crítica     → perfil de proteção
recuperação     → restauração gradual
```

## Script Performance Profiler

Frente de pesquisa para identificar veículos/objetos com lógica excessiva por frame. Não está confirmado que será possível medir custo individual de macros usando as interfaces atuais; primeiro é necessário mapear o que pode ser observado com API oficial, OmsiHook e instrumentação externa.

## Regra

O Simulation Budget não deve remover veículos essenciais à operação da linha nem quebrar horários/simulação para perseguir FPS. Consistência da simulação é um requisito funcional.
