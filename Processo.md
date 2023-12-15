
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
Tutti i processi vengono creati da altri processi, a parte l’init che è sempre il primo a nascere e ha PID=1. Avviene una richiesta al S.O. da parte di un processo già in esecuzione (processo **parent**) di creare un nuovo processo (processo **child** ), di solito un processo ne genera un altro per avere un “aiuto” nello svolgimento di una qualche mansione. 
Tramite una <mark style="background: blue;">fork()</mark> il **parent genera il child che avrà un proprio PID e PCB**, siccome il child a sua volta può creare un altro child di cui essere parent, si creano <mark style="background: blue;">gerarchie di processi</mark>. Ogni parent, all’interno del PCB contiene informazioni per identificare i child e viceversa.

## **_Terminazione di un processo_**
Normalmente un processo termina quando esegue il suo ultimo comando exit() che comunica al S.O. che termina e richiedendo la propria eliminazione, quindi exit() è una system call. Ci sono due casi:
- **_caso normale_**: il S.O. fa disallocare le risorse concesse al processo e manda al parent le informazioni/dati per la terminazione del child;
- **_caso anomalo_**: il processo termina per:
	- errori/violazioni che portano il S.O. a uccidere il processo (es. non aveva l’autorità per accederead un blocco di memoria ”segment violation”);
	- un altro processo ne richiede la terminazione;
	- il S.O. termina i processi child quando il suo parent termina
## **_Terminazione in UNIX_**
In UNIX è previsto che ogni processo abbia un padre. In tal modo è sempre possibile trasmettere lo status di uscita di un processo che termina al processo padre:
il padre, eseguendo la system call wait(), riceve tramite il S.O. il PID (e l’exit status) del processo terminato: 
```
pid_t wait(int *status) 
```
In questo modo ogni processo può venir a conoscenza di quali tra i suoi figli sono terminati dato che la completa disallocazione delle risorse di un processo terminato può avvenire SOLO se il padre esegue wait(). Fino a quando ciò non accade il processo terminato resta in uno stato particolare detto anche zombie (o defunct) per permettere questo meccanismo, qualora il padre termini prima del figlio, quest’ultimo viene adottato dal processo init.
#### <mark style="background: #FFB86CA6;">Davide dice che il processo zombie è ...</mark>
> _Processo terminato a cui il padre non ha eseguito la wait() per disallocargli le risorse e raccogliere la exit()._

### **_Processi Indipendenti o Cooperanti_**
Due processi in esecuzione potrebbero influenzarsi tra loro:
-  **_processi indipendenti_**: due processi non si influenzano a vicenda (calcoli, dati,...) quindi sono sempre riproducibili (si ottiene sempre lo stesso risultato se rieseguiti);
- **_processi concorrenti_**: due processi per risolvere un problema lavorano assieme, la riproducibilità non e garantita. Dei processi concorrenti possono essere cooperanti o competitori (usano le stesse risorse e si ostacolano). Avere processi cooperanti è utile per:
	- condivisione di informazioni o risorse;
	- computazione più veloce
	- modularità nell’implementazione (ogni modulo è un processo);
Dei processi concorrenti devono avere dei meccanismi per scambiarsi informazioni e dati:
1. shared memory (es. lavagna);
2. message passing (es. SMS).

## **_Comunicazione tra processi_**
### 1) Shared memory
In questo tipo di comunicazione, c'è un <mark style="background: red;">blocco di memoria principale con risorse condivise</mark>, al quale hanno accesso più processi. I processi vedranno questa zona come una <mark style="background: red;">variabile</mark>, che sarà stanziata dal S.O.
1. uno o più processi dicono al S.O. di volere un blocco di memoria per comunicare (shmget()) e gli viene dato un indirizzo di memoria;
2. ogni processo che vuole usare quel blocco di memoria, deve annetterlo al proprio spazio di indirizzamento (shmat());
3. il S.O. delega la gestione di questo blocco di memoria ai processi (e serve regolamentare da programma l’accesso alla memoria).

![[sghment.png]]
### 2) Scambio di Messaggi
Le comunicazione avvengono tramite un canale di comunicazione e due operazione base:
- **_send_**: usata dal mittente per inviare il messaggio sul canale
- **_receive_**: usata per prelevare/ricevere un messaggio dal canale
Si possono avere diversi tipi di comunicazione in base a:
- **Comunicazione Diretta**: <mark style="background: green;">Schema simmetrico</mark> tra ogni coppia di processi si stabilisce un solo canale (identificato dai due interlocutori e ogni canale è associato ad esattamente due processi dove l'interlocutore è identficato con <mark style="background: green;">naming</mark>.
- **Comunicazione Indiretta**: non si sa chi riceve e chi ha spedito ⇒ ci possono essere più canali e più di due processi possono usarli. Si manda e si riceve in una mailbox univocamente identificata.
### 3) Comunicazione UNIX
UNIX (standard System V) offre una forma di comunicazione elementare tramite la system call:
```C
int pipe(int pipefd[2])
```
la quale definisce un canale monodirezionale: una unnamed pipe
Ci sono due descrittori: 
```C
(pipefd[0]) //per leggere
(pipefd[1]) //per scrivere
```
I limiti di questo tipo di comunicazione sono che in un S.O. c’è un max di unnamed pipe (senza nome) e che l’unico modo di farlo ed usarlo è con una fork() (in questo modo parent e child hanno gli stessi indicatori). Essendo anonima solo i parenti la possono usare, solo quelli che hanno la stessa tabella (per i quali gli indicatori sono gli stessi)

### 4) Comunicazioni nei Sistemi Client-Server

Il modello client-server è bidirezionale e coinvolge almeno due processi; <mark style="background: blue;">i socket </mark>rappresentano gli estremi del canale di comunicazione, dove ogni estremo è l'**_endpoint_** del canale stesso. Di solito, c'è una distinzione chiara tra client e server (non scambiabili).

In ambiente UNIX, è possibile utilizzare:

- **unix-socket**: se i processi sono eseguiti nello stesso sistema operativo, sfruttando il file system;
- **internet-socket**: utilizzato su internet o in reti interne.

Nelle chiamate remote procedure call (RPC), un processo richiede un servizio da un altro processo attraverso uno stub, che indica la posizione del "servitore" e sfrutta la connessione internet.

Tipicamente, la configurazione è di tipo client-server, e un processo intermedio riceve le richieste, inoltrandole ai richiedenti.


> **_Passa al capitolo precedente_** - [[Thread]]

>**_Torna all'indice_** - [[Sistemi operativi]] 