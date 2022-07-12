# Domanda 1

Il protocollo 2PC ha il compito di garantire l'atomicità delle transazioni, ovvero bisogna garantire che tutti i nodi che partecipano alla transazione giungano alla stessa decisione.
Generalmente dividiamo il protocollo in due fasi:

1. In cui il TM chiede e preleva informazioni dai RM circa le loro intenzioni
2. In cui il TM fornisce la decisione globale circa la transazione

## LOG

sia i TM che gli RM scrivono su file di LOG per aumentare la resistenza ai guasti.
TM:

- Prepare: che contiene l'identità dei processi RM
- Global commit / Global Abort: esprime la decisione atomica e persistente, di portare a termine con successo l'intera transazione su tutti i nodi in cui opera.
- Complete: indica la conclusione del protocollo

RM:

- Prepare: indica l'irrevocabile disponibilità di partecipare al protocollo di commit a due fasi contribuendo a una decisione di commit.

Una volta scritto Ready il partecipante perde ogni potere decisionale e l'esito finale della transazione sarà dato dalla global decisione del TM. Un partecipante può scrivere ready solo quando si trova in uno stato affidabile: ovvero quando ha inserito opportuni lock sulle sue risorse.

## Protocollo in assenza di guasti

Il protocollo in assenza di guasti consiste in una rapida sequenza di scritture sul log e di scambi di messaggi tra RM e TM.

### Prima fase

1. Il TM scrive prepare nel log e invia un pessaggio di prepare per informare tutti gli RM dell'inizio del protocollo. Imposta anche un timeout in caso di perdita del messaggio
2. Gli RM in stato affidabile attendono il messaggio, appena arriva scrivono sul log Ready e ne trasmettono il corrispettivo messaggio al TM, in modo da acconsentire al protocollo mediante commit. Se ciò non accadesse (se non sono in stato affidabile) si invia un messaggio di not-ready.
3. Il TM:
   1. se sono tutti ready allora scrive sul log una global commit
   2. Se almeno uno è not-ready allora scrive global abort.
   3. se scatta il time-out allora scrive global abort.

### Seconda Fase

1. Il TM trasmette la decisione globale agli RM ed imposta un timeout per la rispsota.
2. Gli RM ricevono il messaggio globale e rispondono con un ack, scrivendo nel file di log commit/abort in relazione alla decisione globale. Una volta fatto ciò l'implementazione del protocollo avviene localmente per ogni RM.
3. Il TM colleziona tutti i messaggi ack, se il timeout scatta e ne manca qualcuno allora si riesegue la seconda fase del protocollo. Se tutti gli ack sono arrivati allora si scrive nel log il record complete.

Quindi un RM in stato ready perde la propria autonomia e si affida alla decisione del TM; questa decisione è particolarmente pericolosa perché se il TM si rompesse allora si lascerebbe RM in uno stato di incertezza. In particolare in questo stato tutte le risorse sono bloccate tramite meccanismi di lock. Questa fase viene chiamata _finestra di incertezza_, il protocollo cerca però di limitarla al minimo.

## Guasti

Sfortunatamente ottenere l'atomicità delle transazioni non è una cosa facile, spesse volte si va incontro a diverse problematiche: perdita di messaggi, malfunzionamento di un nodo o partizionamento della rete.

### Guasti ai componenti

Per i guasti ai componenti andiamo a definire due particolari protocolli:

- Procotolli di terminazione: affettuano la terminazione delle procedure
  - Guasto di un RM:
    - il TM si trova nello stato Prepare: scade il timeout e quindi si lancia un global abort
    - il TM si trova nello stato della Global Decision: scatta il timeout e si esegue nuovamente la seconda fase.
  - Guasto di TM:
    - RM si trova nello stato iniziale: in questo caso RM attende il messaggio di Prepare senza successo. Si esegue un abort unilaterale
    - RM si trova in stato Ready: siamo nella finestra di incertezza. RM può decidere un abort unilaterale (seguito da global abort quando TM si riprende) oppure farsi aiutare dagli altri nodi (se possono comunicare tra loro) per prendere una decisione.
- Protocolli di Ripristino: ripristinano lo stato di un nodo
  - Guasto ad un RM:
    - Se l'ultimo log è ready allora si è in dubbio circa l'esito della transazione. Vengono collezionati in un insieme (READY) gli identificativi delle transazioni in dubbio; per ciascuna di queste transazioni è necessario richiedere l'esito finale della transazione al TM (remote recovery) oppure l'informazione viene percepita tramite la seconda fase.
    - Se l'ultimo record è un'operazione di commit/abort.
      - se è abort allora si effettua _undo_ quindi le azioni vengono disfatte
      - se è commit allora _redo_, quindi le azioni vengono rifatte
  - Guasto ad un TM:
    - Se l'ultimo record è prepare allora viene seguito global abort e si passa alla seconda fase. Oppure si riprova a inviare nuovamente il messaggio di Prepare sperando di poter arrivare ad una global commit.
    - Se l'ultimo record nel log è una globla decision allora il TM ripete la seconda fase.
    - Se l'ultime record nel log è complete allora la caduta del TM non ha effetto sulla transazione.

Si noti che la ripetizione della seconda fase può portare molti RM a ricevere lo stesso messaggio, essi dovranno sempre e comunque rispondere con un ack pur ignorando le richieste ripetute.

### Perdita di messaggi e partizionamento della rete

- la perdita di un messaggio prepare o del conseguente messaggio ready non sono distinguibili dal TM. Per cui se scatta il timeout allora si effettua un global abort
- La partedita del messaggio decisionale e del successivo hack non sono distinguibili dal TM quindi si ripete la seconda fase
- Un partizionamento della rete non provoca ulteriori problemi.

## Ottimizzazione

- Regola Read Only
  - quando un RM deve eseguire operazioni di sola lettura, risponde con un messaggio con _read-only_. Il TM ignora questa tipologia di RM.
- Regola PResumed Abort
  - Se il TM riceve almeno un abort allora invia il messaggio di global abort senza scriverlo nel log e abbandona la transazione. Non aspetta le risposte degli RM. Quindi ad ogni richeista i remote recovery da parte di un partecipante in dubbio sulla cui transazione il TM non abbia informazione viene restituita la decisione di global abort. In generale solo _ready_ e _commit_ devono essere scritti sui log degli RM in modo sincrono, e anche la _global commit_ dei TM.

# Domanda 2

Definiamo il CAP Theorem:

- Consistency: tutti i nodi vedono i dati allo stesso modo e allo stesso momento. Quindi se viene effettuata una richiesta si ha la sicurezza che la risposta contenga o un errore, o una risposta negativa o una risposta positiva con il dato più aggiornato all'interno del database. Da non confondere con la consistenza in ACID in cui si vuole passare da uno stato consistente del sistema ad un altro.
- Availability: in cui si garantisce che la risposta venga sempre fornita qualora si faccia una richiesta. Non ci possono essere errori ma il dato ricavato protrebbe non essere quello più recente.
- Partition Tollerance: in cui si garantisce che il sistema funzioni sempre anche in caso di errori o di perdita di messaggi.

In un sistema di messaggistica istantanea si potrebbe pensare di sfruttare:

- Consistency: voglio che tutti gli utenti vedano gli stessi dati contemporaneamente. Non voglio che un "post" possa essere visto solo da alcuni utenti e da altri no in un certo istante.
- Availability: voglio che ogni richiesta sia sempre accontentata dal sistema.

Si potrebbe quindi sacrificare la Partition Tollerance, così facendo non è garantito che il sistema sia sempre disponibile.
Seguendo questa ideologia potremmo pensare di usare un dabase relazionale come MySQL o PostGres
