# Runtime Bridge — arquitetura v0.1

O Runtime Bridge conecta o OMSI 2 x86 aos serviços modernos do Infinitum Host x64 sem afirmar que a engine original foi convertida para 64-bit.

## Objetivos

- expor telemetria do OMSI com baixo overhead;
- isolar detalhes/versionamento de offsets e APIs;
- fornecer contratos estáveis aos módulos do framework;
- permitir IPC eficiente entre x86 e x64;
- separar leitura segura de controles experimentais de escrita.

## Fase 1 — Read-only

O primeiro protótipo deve coletar:

- Timegap/frametime;
- mapa atual;
- tiles e estados de loading;
- contagem de veículos;
- contagem de humanos;
- uso de memória do processo;
- timestamps para correlação.

Nenhuma alteração da simulação é necessária nesta fase.

## Provedores

O framework deve evitar dependência rígida de uma única tecnologia. Interfaces conceituais:

```text
TelemetryProvider
MapProvider
TileProvider
VehicleProvider
HumanProvider
MemoryProvider
RuntimeControlProvider
```

A API oficial de plugins deve ser preferida quando fornecer o dado necessário. OmsiHook pode atuar como provedor experimental para estruturas que não estejam disponíveis oficialmente, respeitando sua licença e a dependência de versão/offsets.

## IPC

Candidatos para pesquisa:

- shared memory;
- ring buffer;
- named pipes para controle/configuração;
- mensagens compactas com timestamps.

O caminho de alta frequência deve minimizar serialização, alocação e bloqueios.

## Segurança

Leitura e escrita são capacidades diferentes. Um recurso só pode migrar para `RuntimeControlProvider` quando possuir:

1. endereço/estrutura reproduzível na versão suportada;
2. efeito conhecido;
3. alteração reversível;
4. ausência de corrupção após testes prolongados;
5. comportamento conhecido em loading, troca de câmera e troca de mapa.

## Versionamento

O Runtime Bridge deve detectar a versão do OMSI antes de habilitar qualquer provedor dependente de memória. Versões desconhecidas devem cair para modo seguro/read-only ou desabilitar o provedor incompatível.
