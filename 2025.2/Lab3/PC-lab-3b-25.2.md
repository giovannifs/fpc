# Laboratório 3B: Pipeline Concorrente de Processamento de Imagens

## Objetivos

Este laboratório tem como objetivo fazer com que o aluno:

1. **Identifique** como uma solução serial de processamento de imagens pode ser refatorada em uma solução concorrente
2. **Compreenda** a decomposição de tarefas e a paralelização de operações
3. **Analise** quais operações podem executar em paralelo e quais devem ser sequenciais
4. **Meça** as melhorias de desempenho através de comparações entre a solução serial e concorrente
5. **Implemente** padrões como pipeline, thread pool e produtor-consumidor

## Descrição do Problema

### Cenário

Você está desenvolvendo um sistema de processamento em lote para uma empresa de fotografia digital. O sistema deve ser capaz de processar um grande volume de imagens de alta resolução com as seguintes operações:

1. **Carregamento**: Ler imagens de alta resolução do disco (I/O - bound)
2. **Filtros**: Aplicar múltiplos filtros (conversão em escala de cinza, desfoque, detecção de bordas)
3. **Redimensionamento**: Redimensionar para múltiplos formatos de saída (thumbnail, web, impressão)
4. **Salvamento**: Escrever imagens processadas em diretórios de saída (I/O - bound)
5. **Metadados**: Gerar informações de processamento para cada imagem

### Análise da Solução Serial Atual

A implementação serial atual processa cada imagem sequencialmente através de todas as etapas:

```
Para cada uma das 100 imagens:
  ├─ Carregar imagem do disco (I/O)           → ~200ms
  ├─ Aplicar filtro em escala de cinza (CPU) → ~50ms
  ├─ Aplicar desfoque (CPU)                   → ~75ms
  ├─ Detectar bordas (CPU)                    → ~60ms
  ├─ Redimensionar para thumbnail (CPU)       → ~40ms
  ├─ Redimensionar para web (CPU)             → ~45ms
  ├─ Redimensionar para impressão (CPU)       → ~50ms
  ├─ Salvar arquivos (I/O)                    → ~150ms
  └─ Gerar metadados (CPU)                    → ~20ms

Tempo por imagem: ~690ms
Tempo total: ~69 segundos
```

### Desafios para o Aluno

1. **Identificação de Tarefas Paralelas**: Quais operações podem executar em paralelo? Qual é a dependência entre elas?

2. **Análise de Bottlenecks**: Qual etapa limita o throughput geral do sistema?

3. **Paralelização de Pipeline**: É possível criar um pipeline onde diferentes imagens estão em diferentes estágios simultaneamente?

4. **Gestão de Recursos**: Quantas threads são ótimas? Como balancear operações CPU-bound vs I/O-bound?

5. **Padrões de Concorrência**: Como implementar produtor-consumidor, thread pool e pipeline?

### Oportunidades de Melhoria

- **Paralelização de I/O**: Múltiplas imagens podem ser carregadas enquanto outras estão sendo processadas
- **Pipeline de Processamento**: Filtros podem ser aplicados sequencialmente a uma imagem enquanto outra é carregada
- **Pool de Threads**: Um número fixo de workers pode processar múltiplas imagens simultaneamente
- **Produtor-Consumidor**: Thread de carregamento alimenta fila de trabalho; threads de processamento consomem

### Resultados Esperados

Após análise e implementação adequada, os alunos devem reconhecer:

| Abordagem | Tempo Estimado | Speedup |
|-----------|---|---|
| Serial | ~69s | 1x |
| Com paralelização de I/O | ~35s | 2x |
| Com pipeline completo | ~20s | 3.5x |
| Com thread pool otimizado (4-8 threads) | ~10-15s | 5-7x |

## Atividades Práticas

### Parte 1: Análise e Medição
- [ ] Execute a solução serial e meça os tempos
- [ ] Identifique operações CPU-bound e I/O-bound
- [ ] Crie um gráfico de timeline do processamento serial

### Parte 2: Design Concorrente
- [ ] Desenhe o diagrama de paralelização
- [ ] Defina quantas threads serão necessárias
- [ ] Documente as dependências de dados e sincronização

### Parte 3: Implementação
- [ ] Implemente versão com paralelização de I/O
- [ ] Implemente versão com pipeline
- [ ] Implemente versão com thread pool e produtor-consumidor

### Parte 4: Análise de Desempenho
- [ ] Meça tempos de execução para cada versão
- [ ] Compare speedups obtidos com esperados
- [ ] Analise contenção de recursos e bottlenecks restantes
- [ ] Documente conclusões

## Referências de Padrões

- **Produtor-Consumidor**: Separação entre threads que geram trabalho e threads que o consomem
- **Pipeline**: Dividir tarefa em estágios, cada estágio processa uma unidade enquanto o anterior processa a próxima
- **Thread Pool**: Manter número fixo de threads reutilizáveis em vez de criar/destruir constantemente
- **Sincronização**: Usar estruturas como filas thread-safe, locks, e barreiras para coordenação

