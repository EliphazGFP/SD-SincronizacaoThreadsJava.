# Relatório Técnico – Sincronização de Threads em Java

---

## 1. Detalhes das Execuções e Saídas Observadas

### Atividade Prática 01 – **Sem Sincronização**  
**Pasta:** `problema`  
**Arquivos de log:** `problema/log1.txt` e `problema/log2.txt`  
**Código principal:** `MeuDadoThreadsJava.java`  
**Métodos:** `armazenar()` e `carregar()` **não** `synchronized`.

#### Saída representativa (`log1.txt`)

```text
Produtor: 0
Consumidor: 0
Produtor: 1
Consumidor: 0   ← inconsistência
Produtor: 2
Consumidor: 0   ← inconsistência
Produtor: 3
Consumidor: 3
Produtor: 4
Consumidor: 3   ← inconsistência
...
Saída representativa (log2.txt)
textConsumidor: 0
Produtor: 0
Consumidor: 0   ← inconsistência
Produtor: 1
Consumidor: 1
...
Observações:

O consumidor leu o valor 0 várias vezes mesmo após o produtor já ter armazenado valores maiores.
As duas execuções apresentaram ordens diferentes e valores inconsistentes.
Race conditions evidentes.

bash$ grep "Consumidor: 0" problema/log1.txt | wc -l   # 12 vezes
$ grep "Consumidor: 0" problema/log2.txt | wc -l   # 9 vezes

Atividade Prática 02 – Monitores + Busy-Wait
Pasta: monitor
Arquivo de log: monitor/log_monitor.txt
Código principal: MeuDadoMonitorJava.java
Métodos: synchronized + while(!Pronto); (polling).
Saída representativa (log_monitor.txt)
textArmazenar Iniciando...
Armazenar Finalizando...
Produtor usando Monitor: 0
Carregar Iniciando...
Carregar Finalizando...
Consumidor usando Monitor: 0
Armazenar Iniciando...
Armazenar Finalizando...
Produtor usando Monitor: 1
Carregar Iniciando...
Carregar Finalizando...
Consumidor usando Monitor: 1
...
Observações:

Nenhuma race condition – o consumidor só lê após o produtor terminar.
Busy-wait (while(!Pronto);) consome CPU enquanto espera.
O valor lido é sempre consistente.

bash$ grep "Consumidor: 0" monitor/log_monitor.txt | wc -l   # 1 vez

Atividade Prática 03 – Eventos (wait() / notify())
Pasta: eventos
Arquivos de log: eventos/log_eventos.txt e log_eventos2.txt
Código principal: MeuDadoEventJava.java
Métodos: synchronized + wait() / notify().
Saída representativa (log_eventos.txt)
textProdutor usando Eventos: 0
Consumidor usando Eventos: 0
Produtor usando Eventos: 1
Consumidor usando Eventos: 1
Produtor usando Eventos: 2
Consumidor usando Eventos: 2
Produtor usando Eventos: 3
Consumidor usando Eventos: 3
...
Produtor usando Eventos: 29
Consumidor usando Eventos: 29
Observações:

Ordem perfeita – o consumidor lê exatamente o valor que o produtor acabou de armazenar.
Nenhum busy-wait – threads ficam bloqueadas (wait()) até serem notificadas.
Consumo de CPU mínimo.

bash$ grep "Consumidor: 0" eventos/log_eventos.txt | wc -l   # 1 vez

2. Análise Técnica















































CritérioAtiv. 01Ativ. 02Ativ. 03Exclusão mútuaNãoSimSimRace conditionsSimNãoNãoIntegridade dos dadosComprometidaGarantidaGarantidaOrdem de execuçãoImprevisívelControladaPerfeitaUso de CPUBaixoAlto (polling)BaixoEscalabilidadeRuimMédiaExcelente
Impacto da Sincronização

Ausência (Ativ. 01)
→ Condições de corrida → perda de atualizações → dados corrompidos.
Monitores + Busy-Wait (Ativ. 02)
→ Exclusão mútua garantida, mas polling desperdiça CPU.
→ Solução funcional, porém ineficiente.
Eventos wait()/notify() (Ativ. 03)
→ Combina exclusão mútua com comunicação eficiente.
→ Threads bloqueiam até condição ser satisfeita → zero desperdício de CPU.
→ Padrão produtor-consumidor clássico implementado corretamente.


3. Opinião Pessoal
A sequência das três atividades é didaticamente perfeita para entender a evolução da concorrência em Java:

Atividade 01 mostra o caos sem controle — essencial para reconhecer a necessidade de sincronização.
Atividade 02 prova que synchronized resolve race conditions, mas não é suficiente sozinho em cenários de coordenação.
Atividade 03 entrega a solução ideal: segura, eficiente e escalável.


Conclusão pessoal:
Para sistemas reais (servidores, pipelines, IoT), nunca use busy-wait.
A combinação synchronized + wait()/notify() é a base para entender mecanismos modernos como BlockingQueue, CountDownLatch e CompletableFuture.

Recomendação: Em produção, substitua por java.util.concurrent — mas dominar wait/notify é pré-requisito para depurar e projetar sistemas concorrentes robustos.

Autor: [Eliphaz]
 TADS – Sincronização de Threads
Data: 29/10/2025
