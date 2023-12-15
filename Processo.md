
[[Sistemi operativi]]
Un **processo** è __un'istanza di un _programma_ caricato in _memoria principale___. Ad un processo corrisponde un programma, ma ad un __programma può corrispondere a più processi__.  
****
### Lo stato di un processo
- New --> Processo appena creato
- Ready --> 
- Running --> Ha in concessione la CPU
- Waiting --> in attesa di qualche evento
- Terminated

![[Screenshot_20231214_160918.png]]
L’assegnamento di un processore ad un processo (ready⇒running) è detto **_dispatching_** ed è eseguito da un particolare modulo del S.O. detto **_dispatcher._**

____
## **_PCB (Process Control Block)_**
Il Process Control Block è una struttura che contiene le informazioni su un processo nel S.O. 
- il **<mark style="background: red;">PID</mark>** (identificatore del processo, solitamente un numero intero)
- lo <mark style="background: green;">STATO</mark> (running, ready, waiting,. . . )
- il valore del <mark style="background: green;">PROGRAMM COUNTER</mark>
- il contenuto dei registri della CPU
- informazioni utili allo scheduling di CPU (priorità,. . . )
- informazioni sulla gestione della memoria (tabelle di paging,. . . )
- ...
____
### Context Switch
Durante un <mark style="background: #FFB86CA6;">CONTEXT SWITCH</mark> il S.O. salva l’execution context del processo che era running, $P_0$, e ripristina quello del processo “entrante” $P_1$ (che passa dallo stato ready allo stato running.
____
# **_Code di Processi_** 
I processi competono tra di loro per utilizzare le risorse come la CPU.
Il S.O. gestisce le richieste dei processi utilizzando **code di scheduling** e **politiche di scheduling**.
Esistono diverse code, associate a diverse risorse
- **_job queue_** (coda dei processi) raccoglie tutti i (PCB dei) processi presenti nel sistema **(ready, running, waiting)**
- **_ready queue_** (coda dei processi pronti) raccoglie tutti i (PCB dei) processi ready
- **_device queue_** (coda del dispositivo) raccoglie tutti i (PCB dei) processi in attesa di un particolare dispositivo di I/O
L’inserimento in una coda è correlato al verificarsi di un evento.
Si applica il diagramma di accodamento:

![[Code.png]]
### Scheduler
Quando una risorsa si rende disponibile, il S.O. deve scegliere uno dei processi in attesa nella corrispondente coda (N.B.: non è sempre una coda FIFO).
I principali scheduler sono tre:
- **_Long term scheduler_**: (o job scheduler) decide quali processi debbano essere inseriti nella **ready queue** ( <mark style="background: blue;">e quindi in memoria principale</mark>) e quali invece debbano restare in memoria secondaria. Determina inoltre per il  sistema il <mark style="background: blue;">grado di  multiprogrammazione</mark>.
- **_Short term scheduler_**: (o scheduler della CPU) sceglie a quale processo, tra quelli nella **ready queue**, assegnare la CPU
- **_Medium term scheduler_**: ha il compito di modulare il carico a cui è soggetto il sistema. Ciò avviene _spostando i processi dalla memoria principale alla memoria secondaria o viceversa_ queste due operazioni, dette **_swap-out_** e **_swap-in_**. Il tutto avviene con processi già <mark style="background: red;">parzialmente eseguiti</mark>.
###### Swapping
Un processo swapped-out passa in uno stato: ready+swapped o waiting+swapped.
Un processo può essere swapped-out per:
+  ribilanciare il rapporto tra CPU bound e I/O bound;
-  se la memoria principale non è sufficiente per soddisfare le richieste di tutti i processi;
- se l’utente o un altro processo lo richiede;
****
## **_Stati di UNIX_**

- <b><mark style="background: red;">created</mark></b>: il processo è appena stato creato
- <b><mark style="background: red;">user/kernel running</mark></b>: in esecuzione in modalità utente/kernel
- <b><mark style="background: red;">ready in memory</mark></b>: pronto per andare in esecuzione
- <b><mark style="background: red;">preempted</mark></b>: bloccato dal kernel al fine di eseguire un altro processo
- <b><mark style="background: red;">asleep in memory</mark></b>: caricato in memoria, ma in attesa di un evento
- <b><mark style="background: red;">ready swapped</mark></b>: pronto per eseguire, ma attualmente swapped-out (su disco)
- <b><mark style="background: red;">sleeping swapped</mark></b>: attualmente swapped-out e in attesa di un evento
- **_zombie_**: terminato, ma in attesa che il padre riceva (eseguendo la wait()) lo status di ritorno
____
## **_Creazione di un processo_**
Tutti i processi vengono creati da altri processi a parte l’init che è sempre il primo a nascere e ha PID=1. Avviene una richiesta al S.O. da parte di un processo già in esecuzione (proce-sso **parent**) di creare un nuovo processo (processo **child** ), di solito un processo ne genera un altro per avere un “aiuto” nello svolgimento di una qualche mansione.
Tramite una fork() il parent genera il child che avrà un proprio PID e PCB, siccome il child a sua volta può creare un altro child di cui essere parent, si creano gerarchie di processi. Ogni parent, all’interno del
PCB contiene informazioni per identificare i child e vice versa.




[[Sistemi operativi]] 