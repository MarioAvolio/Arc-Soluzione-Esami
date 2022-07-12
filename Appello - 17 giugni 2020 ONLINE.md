# Domanda 1

La frammentazione dei dati consente di organizzare i dati stessi in modo tale da garantire la loro distribuzione efficiente e ben organizzata. Solitamente questa tecnica ha validità solo se pensata a priori.
Consideriamo una relazione R; la sua frammentazione consiste nel determinare un certo numero di frammenti Ri, ottenuti applicando a R operazioni Algebriche. La frammentazione può essere di due tipi:

- Frammentazione Orizzontale: i frammenti Ri sono insiemi di tuple con lo stesso schema della relazione R; ogni frammento orizzontale può essere visto come un'operazione di selezione sulla relazione originaria R.
- Frammentazione Verticole: I frammenti Ri hanno uno schema ottenuto come sottoinsieme dello schema della relazione R. Ciascun frammento verticolare può essere interpretato come il risultato di una proiezione applicata alla relazione R.

La frammentazione è corretta se valgono le seguenti proprietà:

- Completezza: ogni dato della relazione R deve essere presente in un qualche suo frammento Ri
- Ricostruibilità: la relazione deve essere interamente ricostruibile a partire dai suoi frammenti.

Normalmente i frammenti orizzontali sono disgiunti mentre i frammenti verticali possiedono tutti la chiave primaria della relazione R, in modo da garantire la ricostruibilità. Solitamente la frammentazione esprime proprietà logiche dei dati.
Ogni frammento viene allocato fisicamente su dei server. La relazione è virtuale (come una vista) mentre i frammenti sono fisicamente allocati su file o su database sui server. Il mapping tra frammento (o intere relazioni) e server avviene mediante lo _schema di allocazione_, consentendo il passaggio da una descrizione logica a quella fisica dei dati.
Il mapping può essere:

- ridondante: se un frammento è allocato su più server
- non ridondante: se un frammento è allocato su un solo server.

# Domanda 2

## Modello Documentale

Il modello documentale si presenta come un albero, avendo accesso dal nodo radice e scendendo verso le foglie. E' un modello multidimensionale dove ogni documento può contenere zero valori, multipli valori o altri documenti. Lo schema è flessibile poichè due documenti della stessa collezione possono avere uno schema diverso di rappresentazione dei dati, possiamo quindi aggiungere nuove informazioni su ogni documento. Non c'è bisogno di join poichè i dati sono innestati tra loro.
Esistono due tecniche principali per l'accesso dei dati:

- Referencing: si relazionano due documenti tramite un certo attributo
- Embedding: più usato rispetto al Referencing. Si aggiunge un altro documento innestato andando quindi ad incrementare il livello di profondità dell'albero.

La troppa flessibilità del modello porta problematiche in fase di query poiché non si ha uno schema prefissato su cui interrogare.

### MongoDB

Modello documenale con dati memorizzati in un modello chiamato Binary Json (BSON). MongoDB sfrutta l'embedding e l'utilizzo di indici sebbene sia anche in grado di sfruttare il referencing. Le repliche avvengono mediante l'utilizzo di tecniche master-slave: la scrittura avviene sul nodo primary e la replicazione avviene sui nodi secondari sfruttando un approccio incrementale tramite file di log. Sfrutta il CP rispetto al CAP Theorem e per garantire la partition tollerance sfrutta un meccanismo di elezione tra i nodi di un replica set. La frammentazione avviene framite un meccanismo chiamato Sharding, in particolare uno shard-cluster è formato da:

- shard: insieme di dati frammentati. si può organizzare come replica set
- Mongos:un router che instada le query sui corrispettivi shard in modo che i client non eseguano direttamente sugli shard
- config server: memorizzano la configurazione del cluster e sono necessari per capire quale shard possiede un determianto frammento. Sono organizzati come replica-set.

lo sharding viene eseguito tramite shard-key, in cui ogni documento viene assegnato ad un chunk dello shard.

## Modello Colonnare

Può essere interpretato come un'evoluzione del modello key-value. Ad ogni chiave è associata una riga di elementi monodimensionali. I dati vengono rappresentati all'interno di tabelle multidimensionali che sono ordinate, persistenti e sparse. La mappa è indicizzata da row key, column key (column family) e un timestamp. Le tabelle sono formate da un insieme di righe e sono chiamate tablet. Generalmente ogni column-family può avere diversi qualifier andando a creare uno schema molto flessibile. Ogni column family ha un nome e può contenere una serie di colonne.

### HBASE

Versione distribuita di BigTable che fa uso di Hadoop per la memorizzazione, replicazione e sincronizzazione dei dati. Ogni table è composta da righe, ognuna composta da column family che a loro volta sono formate da un numero variabile e dinamico di colonne. Le righe sono indicate da una chiave e le column family hanno una struttura chiave-valore.
Una Region è un insieme di righe della tabella. Per quanto riguarda la distribuzione abbiamo:

- HRegionServer
  - fornisce i dati per le letture e le scrittura. Gestisce le regioni interne a ciascuno
- HBaseMaster
  - Gestisce i diversi HRegionServer, assegna loro le regioni. Riconosce fallimenti degli slave
- HBaseClient
  - Fa richiesta ai Master che loro volta fanno richeista ai Server

Si sfrutta Zookeeper qualora un client faccia richiesta a più master che cooperano con i HRegionServer. Si sfrutta Hadoop per la memorizzazione dei dati in maniera orizzontale tramite WAL per favorire la robustezza ai guasti. Hadoop viene usato anche per la replicazione e sincronizzazione dei dati.

### Cassandra

Formata da keySpace formate da ColumnFamily (tabelle) che a loro volta sono composte da righe identificate da RowId. Ogni riga è formata da una collezione di coppie chiave-valore (colum value e column name). L'architettura è peer-to-peer e per la memorizzazione dei dati si sfrutta una struttura ad anello tra i diversi nodi. In particolare lo spazio di indirizzamento delle chiavi viene suddiviso tra i nodi disponibili. Se si aggiunge un nuovo nodo vi è una suddivisione dell'intervallo di indirizzamento che viene diviso tra il nuovo nodo e quello successivo. Le scritture vengono replicati sui nodi successivi (almeno su altri due). Solitamente i nodi si conoscono a vicenda tramite i Protocolli di Gossip

# Domanda 3

Il record linkage riguarda una serie di operazioni atte al confronto tra dati di dataset differenti ma inerenti allo stesso oggetto reale, al fine di trovarne delle adiacenze e collegarli. Una volta individuati questi record si passa all'operazione di fusion, in cui si vanno a risolvere i relativi conflitti e si esegue un'integrazione.
L'input del processo di record linkage riguarda una serie di tabelle, mentre l'output:

- gruppi di tuple che matchano, corrispondenti allo stesso oggetto reale
- gruppi di tuple che non metchano, corrispondenti ad oggetti reali distinti
- gruppi di tuple di cui non si possono fornire informazioni e su cui è bene effettuare ulteriori analisi più approfondite.

Il processo possiede diversi steps:

- Pre-Processing: si esegue la normalizzazione andando a creare un unico formato per tutti i dati.
- blocking: in cui si cerca di ridurre l'insieme di dati da analizzare, ovvero si cerca di ridurre lo spazio di ricerca. Si può usare il sorted neightbour.
- Comparazione: in cui si confrontano le coppie di dati. In particolare esistono diverse metodologie per eseguire un'operazione di comparazione:
  - metodi empirici
  - metodi probabilistici: in cui si calcola la probabilità di matching tra le coppie, calcolando una somma pesata delle distanze tra gli attributi delle coppie. Se il punteggio ottenuto supera una certa soglia allora si ha un match
  - metodi basati sul machine learning
  - metodi basati sulla conoscenza
  - metodi misti
