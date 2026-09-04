# Infinitum Performance Engine

O Performance Engine é um dos pilares centrais do projeto. O objetivo não é apenas aumentar o FPS médio, mas melhorar a estabilidade e adaptar a carga do OMSI ao hardware e à situação atual.

## Conceitos planejados

- Target FPS / Target Frametime;
- Dynamic Quality;
- CPU Budget;
- GPU Budget;
- Dynamic Draw Distance;
- Dynamic Traffic & Pedestrians;
- Adaptive Mirrors;
- perfis Performance, Balanced, Quality e Custom;
- telemetria de FPS, frametime, memória e custo dos módulos.

## Princípio

O sistema deverá reduzir primeiro recursos de alto custo e menor impacto perceptível quando o orçamento de desempenho for excedido e recuperar qualidade gradualmente quando houver margem.

## Estado técnico

A arquitetura está em desenvolvimento. Ainda é necessário determinar quais parâmetros do OMSI podem ser modificados em runtime com segurança, quais exigem recarregamento e quais demandariam integração mais profunda com a engine.
