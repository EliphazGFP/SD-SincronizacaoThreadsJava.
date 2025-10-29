# Relatório Técnico – Sincronização de Threads em Java

## 1. Detalhes das Execuções e Saídas Observadas

### Atividade Prática 01 – **Sem Sincronização**  

**Pasta:** `problema`  

**Arquivos de log:** `problema/log1.txt` e `problema/log2.txt` 

**Código principal:** `MeuDadoThreadsJava.java`  

**Métodos:** `armazenar()` e `carregar()` **não** `synchronized`.

**Observações:**

O consumidor leu o valor 0 várias vezes mesmo após o produtor já ter armazenado valores maiores.
As duas execuções apresentaram ordens diferentes e valores inconsistentes.
Race conditions evidentes.


**Atividade Prática 02 – Monitores + Busy-Wait**

**Pasta**: monitor

**Arquivo de log:** monitor/log_monitor.txt

**Código principal:** MeuDadoMonitorJava.java

**Métodos:** synchronized + while(!Pronto); (polling).

**Observações:**

Nenhuma race condition – o consumidor só lê após o produtor terminar.
Busy-wait (while(!Pronto);) consome CPU enquanto espera.
O valor lido é sempre consistente.


**Atividade Prática 03 – Eventos (wait() / notify())**

**Pasta:** eventos

**Arquivos de log:** eventos/log_eventos.txt e log_eventos2.txt

**Código principal:** MeuDadoEventJava.java

**Métodos:** synchronized + wait() / notify().

**Observações:**

Ordem perfeita – o consumidor lê exatamente o valor que o produtor acabou de armazenar.
Nenhum busy-wait – threads ficam bloqueadas (wait()) até serem notificadas.
Consumo de CPU mínimo.



**2. Análise Técnica**


<img width="712" height="302" alt="image" src="https://github.com/user-attachments/assets/57597574-33e9-4946-8ae8-086cc70c7826" />

**Atividade 01**

A ausência de exclusão mútua permite interleaving de instruções entre armazenar e carregar.
Resultado: condições de corrida → valores imprevisíveis e perda de atualizações.

**Atividade 02**

synchronized garante exclusão mútua – apenas uma thread modifica/ lê Dado por vez.
O uso de while(!Pronto); (busy-wait) resolve a race condition, mas gasta ciclos de CPU desnecessariamente.

**Atividade 03**

Combina monitores (synchronized) com comunicação (wait() / notify()).
A flag Pronto controla a condição de espera; wait() suspende a thread, liberando o monitor; notify() acorda a thread oposta.
Elimina busy-wait, reduzindo drasticamente o consumo de CPU e tornando a solução eficiente e escalável.

**Impacto da Sincronização**

Ausência (Ativ. 01)→ Condições de corrida → perda de atualizações → dados corrompidos.

Monitores + Busy-Wait (Ativ. 02)→ Exclusão mútua garantida, mas polling desperdiça CPU.→ Solução funcional, porém ineficiente.

Eventos wait()/notify() (Ativ. 03)→ Combina exclusão mútua com comunicação eficiente.→ Threads bloqueiam até condição ser satisfeita → zero desperdício de CPU.→ Padrão produtor-consumidor clássico implementado corretamente.


**3. Opinião Pessoal**
A sequência das três atividades é didaticamente perfeita para entender a evolução da concorrência em Java:

**Atividade 01** mostra o caos sem controle, sendo essencial para reconhecer a necessidade de sincronização.
**Atividade 02** prova que synchronized resolve race conditions, mas não é suficiente sozinho em cenários de coordenação.
**Atividade 03** entrega a solução ideal: segura, eficiente e escalável.


**Conclusão pessoal:**
Para sistemas reais (servidores, pipelines, IoT), nunca use busy-wait.
A combinação synchronized + wait()/notify() é a base para entender mecanismos modernos.

