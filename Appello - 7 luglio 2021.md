# Domanda 1

Le architetture wrapper mediator sono tipologie di architetture di integrazione dati. Hanno principalmente il compito di permettere all'utente di accedere in modo uniforme a diverse fondi di dati etoregenee e autonome, attraverso una vista unificata di tali dati.
In questo caso non si andranno a salvare tutte le informazioni in un unico database o in un unico file bensì si preferisce mantenere inalterati gli schemi o i dati, dando la possibilità all'utente di accedergli mediante uno schema globale. Con questa metodologia si vanno a tradurre le varie query, sottoposte allo schema globale, ai diversi schemi locali.
Le componenti principali di queste tipologie di architetture di integrazione dati sono:

- uno schema globale
- uno schema locale
- dei mapping tra i due per descrivere un elemento dello schema globale come si relazione con lo schema locale
  - GAV: Global as View. Lo schema globale è una view di quelli locali. Questo permette una scarsa scalabilità.
  - LAV: Local as View. Lo schema locale è una view dello schema globale. Più facile aggiungere un altro schema locale.
  - GLAV: una via di mezzo

Nello specifico per l'architettura wrapper mediator esistono due possibili approcci:

- mediation: si crea una vista di sola lettura che permette di accedere ai dati di diversi database
- mediation with update: come la prima ma permette anche le scritture

In particolare le componenti principali sono:

- i wrapper: hanno il compito di agganciare uno schema locale ad uno schema globale. Ricevono le interrogazioni dello schema globale e le traducono in interrogazioni per lo schema locale.
- i mediator: hanno il compito di
  - frammentare le query ricevuto allo schema globale per poi passarle agli schemi locali
  - integrare e fondere i diversi risultati ottenuti dall'esecuzione delle query frammentate sui diversi schemi locali
  - riconciliare le istanze
    - raggruppare le informazioni rigurdanti la stessa entità
    - rimuovere ridondanze
    - rimuovere inconsistenze

Possiamo dividere l'interazione con il mediator in due fasi:

- Publishing Fase: riguarda la fase di creazione di uno schema globale
- Querying Fase: riguarda la formulazione e l'esecuzione di una query nella rappresentazione unificata

# Domanda 2

Esistono diverse architetture per la distribuzione dei dati in un database relazionale:

- Shared Everything: non c'è una vera distribuzione dei dati. Il DBMS e il disco sono uniti in un unico nodo
- Shared Disk: esistono diversi DBMS che condividono un unico disco. Soffrono di problemi di concorrenza ed hanno un costo molto elevato.
- Shared Nothing: I DBMS non condividono il disco tra loro, ognuno possiede un proprio disco privato. Molto utile se si vuole scalare orizontalmente poiché basta inserire un nodo indipendentemente dagli altri. La sincronizzazione è molto complessa tra i diversi nodi.

Oltre a ciò la distribuzione dei dati può avvenire tramite:

- Frammentazione
- Replicazione

## Frammentazione

Si definisce Frammentazione la possibilità di allocare porzioni diverse del database su nodi differenti. La Frammentazione consente di organizzare i dati in modo tale da garantire una loro distribuzione efficiente e ben organizzata . Questa tecnica è applicabile solo se la distribuzione dei dati viene pianificata a priori. Considerando una Frammentazione **R**; la sua frammentazione consiste nel determinare un certo numero di frammenti **Ri**, ottenuti applicando ad R operazioni algebriche. In generale la frammentazione esprime proprietà logiche dei dati. La frammentazione può essere di due tipi.

- Orizzontale: i frammenti Ri sono insiemi di tuple con lo stesso schema della relazione R; ciascun frammento orizzontale può essere interpretato come il risultato di una selezione applicata alla relazione R. Normalmente i frammenti orizzontali sono disgiunti.
- Verticale: i frammenti Ri hanno uno schema ottenuto come sottoinsieme dello schema di R; ciascun frammento verticale può essere interpretato come il risultato di una proiezioni applicata alla relazione R. Solitamente i frammenti verticali possiedono la chiave primare definita per R in modo da garantire la ricostruibilità.

La Frammentazione è corretta se valgono le seguenti proprietà:

- Completezza: ogni dato della relazione R deve essere presente in qualche suo frammento Ri
- Ricostruibilità: la relazione deve essere interamente ricostruibile a partire dai suoi frammenti

Ogni frammento ri viene implementato tramite un file fisico e installato presso un server; si dice che i frammenti vengono _allocati_ sui server. Quindi la relazione è presente in modo virtuale (vista) mentre i frammenti sono effettivamente memorizzati mediante uno _schema di allocazione_ che contiene il mapping dai frammenti ai server che li memorizzano, consentendo il passaggio da una descrizione logica ad una descrizione fisica dei dati. Tale mapping può essere

- Ridondante: se ciascun frammento viene allocato esattamente su un server
- Non ridondante: quando qualche frammento o relazione viene allocato su più di un server

## Replicazione

Si intende la possibilità di allocare l'intero database su nodi differenti.
Vantaggi:

- E' utile soprattutto per il miglioramento delle prestazioni del sistema, difatti alcune copie possono essere distribuite più vicino agli utenti, migliorando la località dei dati.
- Non esiste un unico _point of failure_

Svantaggi:

- Poco indicata per aggiornamenti troppo frequenti dei dati, per cui si dovrebbero avere sincronizzazioni molto costanti. Ci possono essere anche conflitti da risolvere
- Poco indiata quando si vuole avere una consistenza costante. Difatti aspettare che tutti i dati siano sincronizzati richiederebbe tempo

Sincronizzazione:

- Sincrona: sincronizzazione nello stesso momento di tutti i dati. Gli aggiornamenti ai dati replicati fanno parte di una transazione quindi se uno o più nodi non sono disponibili la transazione non va a buon fine e si esegue un rollback. Bassa possibilità di perdere dati.
- Asincrona: i cambiamenti vengono inizialmente effettuati sul nodo primario per poi essere successivamente replicati sui nodi secondari. L'aggiornamento non viene al momento. Possibilità di perdere dati elevata.

Metodologie:

- mediante log in modo incrementale
- mediante la copia dell'intero database ogni volta che viene aggiornata

# Domanda 3

## 1

```
create (s:Person {name: "John", age: 27}),(p:Person {name: "Sally", age: 32}) return s,p
```

## 2

```
create (b:Book {title: "Venere Privata", author: "Giorgio Scerbanenco"})
return b
```

## 3

```
match (p:Person {name: "Sally"}), (p2:Person {name: "John"})
create (p)<-[r:FRIEND_OF {since: "01/09/2013"}]-(p2)
return p,p2
```

```
match (p:Person {name: "Sally"}), (p2:Person {name: "John"})
create (p)-[r:FRIEND_OF {since: "01/09/2013"}]->(p2)
return p,p2
```

## 4

```
match (s:Person {name: "Sally"}), (b:Book {title: "Venere Privata"})
create (s) -[r:HAS_READ {on: "02/03/2013", rating: 3}]-> (b)
return s,b
```

## 5

```
match (s:Person {name: "John"}), (b:Book {title: "Venere Privata"})
create (s) -[r:HAS_READ {on: "02/09/2013", rating: 5}]-> (b)
return s,b
```

## 6

```
match (j:Person {name: "John"}) -[rel:FRIEND_OF]- (s:Person {name: "Sally"})
return rel.since
```

## 7

```
match (b:Book {title: "Venere Privata"}) <-[r:HAS_READ]- (p:Person)
return avg(r.rating)
```
