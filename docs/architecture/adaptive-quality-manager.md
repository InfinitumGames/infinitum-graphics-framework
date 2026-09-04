# Adaptive Quality Manager

O **Adaptive Quality Manager** é o módulo responsável por aplicar ajustes aprovados pelo Performance Controller.

Ele não decide quando reduzir ou recuperar qualidade; essa decisão vem do controlador. Sua função é executar somente mudanças conhecidas, limitadas e reversíveis.

## Princípio de segurança

Nenhum parâmetro do OMSI entra como `runtime-safe` apenas porque existe em `options.cfg` ou porque parece funcionar em um teste isolado.

Para ser automatizado, o controle precisa:

- ter fonte/endereço estável na versão alvo;
- responder em runtime sem comportamento imprevisível;
- aceitar rollback;
- não corromper mapa, save ou sessão;
- não exigir reinício silencioso;
- não provocar stutter maior que o benefício;
- passar testes parado, dirigindo e em cenário pesado.

## Capability Registry

Cada ajuste será descrito por uma capacidade.

```text
QualityCapability
├─ id
├─ subsystem
├─ cost_domain
├─ runtime_support
├─ min_value
├─ max_value
├─ step
├─ visual_impact
├─ expected_gain
├─ cooldown
├─ rollback_supported
└─ validation_state
```

## Estados de validação

- **Unknown** — ainda não investigado;
- **ConfigOnly** — seguro apenas antes de iniciar/recarregar o jogo;
- **RuntimeExperimental** — alteração em runtime encontrada, mas ainda não validada;
- **RuntimeSafe** — apto a uso automático;
- **Blocked** — inseguro, instável ou incompatível.

## Prioridade de ajustes

A ordem final será definida por benchmark, mas a estratégia é reduzir primeiro recursos de alto custo e menor impacto perceptível.

Exemplos de candidatos:

| Recurso | Domínio | Estado atual |
|---|---|---|
| Efeitos próprios do Infinitum | GPU | controlável por nós |
| Neural Rendering / qualidade do backend | GPU | integração experimental |
| Reflexos | CPU/GPU | pesquisa de runtime |
| Distância de visão | CPU/GPU/engine | pesquisa de runtime |
| Tiles | engine | pesquisa de runtime |
| Tráfego AI | CPU | pesquisa de runtime |
| Pedestres | CPU | pesquisa de runtime |
| Passageiros | CPU | pesquisa de runtime |
| Espelhos | CPU/GPU | pesquisa prioritária |
| Texturas | VRAM/I/O | controle parcial a pesquisar |

## Aplicação gradual

Uma decisão `Reduce Cost` não deve derrubar várias opções de uma vez.

Fluxo esperado:

```text
DecisionState
    ↓
selecionar capability compatível com o gargalo
    ↓
validar limites e estado RuntimeSafe
    ↓
aplicar um passo
    ↓
registrar valor anterior
    ↓
iniciar cooldown
    ↓
Telemetry Engine mede o resultado
```

## Rollback

Toda alteração automática deverá registrar pelo menos:

- valor anterior;
- novo valor;
- timestamp;
- motivo;
- estado de telemetria antes da mudança;
- resultado medido depois da mudança.

O rollback deverá ser possível por capability e também de forma global ao encerrar/desativar o Performance Engine quando tecnicamente aplicável.

## Perfis

Os perfis Performance, Balanced, Quality e Custom não serão apenas presets fixos. Eles poderão definir:

- Target Frametime;
- limites mínimos de qualidade;
- quais capabilities podem ser alteradas;
- agressividade da redução;
- tempo necessário para recuperar qualidade;
- tolerância a stutter.

## v0.1 e v0.2

Na **v0.1**, o Adaptive Quality Manager pode operar em modo de simulação/recomendação, informando qual ajuste faria sem necessariamente modificar o OMSI.

Na **v0.2**, somente capabilities comprovadamente `RuntimeSafe` poderão ser executadas automaticamente.
