# Pesquisa técnica de viabilidade — Performance Engine no OMSI 2

> Estado: pesquisa inicial. Este documento separa o que já é suportado por documentação pública, o que parece tecnicamente promissor e o que ainda depende de validação local no executável.

## Resumo executivo

A primeira pesquisa indica que o OMSI 2 já possui alguns mecanismos internos de adaptação de desempenho e armazena a maioria dos parâmetros globais em `options.cfg`. Porém, a interface oficial de plug-ins é limitada principalmente a variáveis locais/sistema e triggers; ela não expõe diretamente, de forma documentada, controles globais como tráfego, tiles, visibilidade ou reflexos.

Isso sugere três níveis de integração para o Infinitum Performance Engine:

1. **Configuração segura antes do jogo** — editar/gerar `options.cfg` e presets.
2. **Telemetria e diagnóstico em runtime** — usar variáveis de sistema, APIs de processo e/ou OmsiHook.
3. **Controle adaptativo real em runtime** — provavelmente dependerá de memória interna, hooks ou métodos nativos não expostos pela API oficial.

## Descobertas confirmadas

### 1. `options.cfg` é o centro das opções globais

A documentação da comunidade registra que as configurações atuais do OMSI ficam no arquivo `options.cfg` na pasta principal e presets ficam em `option_presets`.

Entradas observadas em arquivos públicos incluem:

- `[performance_tiledistmax]` — quantidade máxima de tiles vizinhos;
- `[performance_maxObjDist]` — distância máxima de objetos;
- `[performance_minObjSize]` — tamanho mínimo de objeto;
- `[performance_minObjSizeRefl]` — tamanho mínimo em reflexos;
- `[performance_realreflexions]` — modo de reflexos;
- `[performance_reflTexSize]` — textura de reflexos;
- `[performance_dyn_redrefl]` — limiares de reflexão dinâmica;
- `[performance_dyn_tile_red]` — limiares de redução dinâmica de tiles;
- `[maxFPS]` — alvo/limite de FPS;
- `[maxcomplexity]` e `[maxcomplexity_map]`;
- `[texmemlimit]`;
- `[sound_maxcount]`;
- `[AIMaxCountRandom]`;
- `[AIUnschedFactor]`;
- `[AIMaxCountParked]`;
- `[AIPassFactor]`;
- `[AIMaxCountScheduled]`;
- `[AIPriorityScheduled]`;
- `[useLowAIList]`.

### 2. OMSI já possui dois mecanismos adaptativos internos

O manual oficial descreve:

- **Forced Economical Reflections Mode** — muda reflexos de completo para econômico quando o FPS cai abaixo de um limiar e volta quando sobe acima de outro;
- **Dynamic Tile Reduction** — reduz o número de tiles ativos quando o FPS cai abaixo de um limiar e restaura quando melhora.

Isso é especialmente importante para o Infinitum Performance Engine: a ideia de hysteresis/limiares já existe na própria engine e podemos estudar como expandi-la para outros recursos.

### 3. Tiles, visibilidade e complexidade têm impacto direto conhecido

O manual e documentação da comunidade confirmam que:

- tiles carregados crescem rapidamente conforme o raio aumenta;
- distância de visibilidade é limitada também pelos tiles ativos;
- complexidade de objetos/mapa reduz a quantidade de conteúdo calculado/carregado;
- reflexos completos atualizam todos os espelhos continuamente e custam mais recursos;
- modo econômico atualiza um espelho por vez.

### 4. Tráfego e pessoas são alvos fortes de CPU

O manual afirma que AI exige recursos significativos porque veículos e pedestres precisam acompanhar suas próprias posições e as de outros agentes. Ele também documenta limites separados para veículos não agendados, agendados, pessoas, passageiros nas paradas e prioridade de linhas.

Isso valida a arquitetura de **CPU Budget** como frente importante.

### 5. Há uma variável de sistema útil para frametime

A lista pública de variáveis de sistema do OMSI inclui `Timegap`, definida como o tempo de duração do frame em segundos. Isso oferece um caminho nativo para telemetria básica de frametime sem depender inicialmente de captura gráfica externa.

### 6. A API oficial de plug-ins é limitada para nosso objetivo

A interface oficial de plug-ins permite ler/escrever variáveis de veículo, variáveis de sistema e strings, além de acionar triggers. Não há documentação pública que exponha diretamente os parâmetros de performance globais do `options.cfg` como variáveis de plug-in.

Portanto, um plug-in OMSI puro provavelmente é suficiente para telemetria e integração com variáveis conhecidas, mas não para controlar todas as opções de performance dinamicamente.

### 7. OmsiHook é uma rota técnica promissora

O projeto público Omsi-Extensions/OmsiHook fornece leitura e escrita da memória do OMSI em tempo real, expõe vários objetos globais e possui suporte a métodos remotos através de um plug-in nativo. A própria documentação mostra acesso a globais, mapa, câmera, veículos, texturas e outros objetos internos.

Isso torna o OmsiHook uma referência técnica importante para descobrir endereços, estruturas e possíveis controles do Performance Engine.

## Matriz inicial de viabilidade

| Recurso | Pré-jogo via `options.cfg` | Runtime documentado | Runtime via hook/memória | Classificação atual |
|---|---:|---:|---:|---|
| Target FPS | Sim | Não identificado | Provável | Alta viabilidade |
| Frametime/telemetria | N/A | Sim (`Timegap`) | Sim | Alta viabilidade |
| Tiles vizinhos | Sim | Parcial: OMSI já reduz dinamicamente | Provável | Alta prioridade |
| Visibilidade de objetos | Sim | Não identificado | A investigar | Média/alta |
| Complexidade de objetos/mapa | Sim | Não identificado | A investigar | Média |
| Reflexos completos/econômicos | Sim | Sim, mecanismo adaptativo interno | Provável | Alta viabilidade |
| Resolução de reflexos | Sim | Não identificado | A investigar | Média |
| Tráfego não agendado | Sim | Não identificado | A investigar | Alta prioridade |
| Tráfego agendado | Sim | Não identificado | A investigar | Alta prioridade |
| Prioridade de horários | Sim | Não identificado | A investigar | Média/alta |
| Pedestres | Sim | Não identificado | A investigar | Alta prioridade |
| Passageiros nas paradas | Sim | Não identificado | A investigar | Média |
| Carros estacionados | Sim | Não identificado | A investigar | Média |
| Lista AI reduzida | Sim | Não identificado | A investigar | Média |
| Texturas low-res/limites | Sim | Não identificado | A investigar | Média |
| Loading/streaming de assets | Parcial | Não documentado | Pesquisa profunda | Baixa certeza |

## Primeira hipótese de arquitetura técnica

### Nível A — Configurator / pré-jogo

Seguro e implementável cedo:

- ler `options.cfg`;
- criar backup;
- gerar presets;
- validar valores;
- aplicar perfil Performance/Balanced/Quality;
- comparar configuração atual com baseline recomendado;
- restaurar configuração original.

### Nível B — Telemetry Engine em runtime

Primeiro protótipo:

- ler `Timegap`;
- calcular FPS derivado;
- média móvel;
- percentis de frametime;
- detectar picos/stutter;
- coletar CPU/GPU/RAM/VRAM via sistema operacional/APIs externas;
- registrar mapa/cenário quando possível.

### Nível C — Adaptive Runtime Controller

Somente depois de encontrar e validar os controles internos:

- alterar um parâmetro por vez;
- confirmar efeito sem abrir menu/recarregar;
- testar persistência e side effects;
- criar rollback;
- só então integrar ao controlador adaptativo.

## Ordem de investigação local recomendada

Quando houver acesso ao PC com OMSI, investigar nesta ordem:

1. confirmar o conteúdo real do `options.cfg` da instalação atual;
2. mapear quais opções mudam imediatamente ao salvar pelo menu;
3. testar se edição externa do arquivo afeta uma sessão já aberta — expectativa inicial: provavelmente não para a maioria;
4. observar `Timegap` via plug-in/OmsiHook;
5. procurar em OmsiHook estruturas relacionadas a opções/performance;
6. localizar em memória um valor fácil e distintivo, por exemplo visibilidade 500/700 m;
7. alterar apenas esse valor em runtime e observar se a engine responde;
8. repetir para tiles;
9. repetir para AI/pedestres;
10. testar reflexos, aproveitando o fato de OMSI já possuir lógica adaptativa própria.

## Critérios de segurança

Um parâmetro só pode entrar no Adaptive Quality Manager como `runtime-safe` quando:

- o endereço/estrutura é reproduzível na versão alvo do OMSI;
- a alteração produz efeito previsível;
- não corrompe save, mapa ou estado da sessão;
- não exige reinício oculto;
- pode ser revertida;
- passa por teste parado, dirigindo e em cenário pesado;
- não introduz stutter maior que o benefício.

## Conclusão inicial

A pesquisa favorece a viabilidade do projeto, mas com uma distinção clara: **presets e telemetria são viáveis cedo; controle adaptativo profundo depende de engenharia de runtime**.

O ponto mais promissor é que a própria engine já contém lógica dinâmica para tiles e reflexos. Em vez de inventar tudo do zero, o Infinitum Performance Engine pode estudar esses mecanismos, reproduzir o conceito de hysteresis e expandi-lo de forma controlada para outros subsistemas.
