# Domanda 3

Per garantire l'atomicità è necessario che tutti i nodi che partecipano a una transazione giungano alla stessa decisione circa la transazione (commit o abort). Per far ciò sfruttiamo il protocollo di commit a due fasi (ma ne esistono altri). Il protocollo ricorda il matrimonio:

- Durante la prima fase il celebrante raccoglie il desiderio di sposarsi, espresso separatamente dai due partecipanti
- Nella seconda fase comunica ai partecipanti l'esito del matrimonio

In questa metafora i promessi sposi simboleggiano i server che partecipano ad una transazione, chiamati _resource manager_ (RM).
Il celebrante è associato a un processo, detto _transaction manager_ (TM).
Il protocollo di commit a due fasi si svolge tramite un rapito scambio di messaggi tra TM e RM; per rendere il protocollo resistente ai guasti, RM e TM scrivono alcuni nuovi record nei loro log.

Se il TM muore prima della global decision:

- Gli RM si trovano nello stato READY ed hanno bloccato tutte le loro risorse. Siamo nel mezzo della finestra di incertezza quindi gli RM sono bloccati. Gli RM possono decidere un abort unilaterale, in questo caso quando il TM di riprenderà di effettuerà un global abort. Se gli RM sono in grado di comunicare tra loro allora possono passarsi informazioni e decidere come risolvere il problema in assenza del TM.

Durante la fase di ripristino:

- Se il TM si trova in stato di Prepare (ultima scrittura nel file di log) significa che non ha neanche scritto la global decision nel log, allora può decidere se
  - effettuare un global abort
  - riprovare ad inviare nuovamente il messaggio prepare per provare a giungere ad una global commit
- Se il TM si trova nello stato di Global Decision allora esegue la seconda fase

# Esercizio 1

## 1

```
db.tree.find({status: "death"}, {status:1, address:1, boroname: 1, latitude:1, longitude:1, _id: 0})
```

## 2

```
db.tree.find({zipcode: 11204, status: "Alive"},{spc_latin: 1, dorocode:1, boroname:1, zip_city:1, address:1})
```

## 3

```
db.tree.find({spc_info.fall_color: "Yellow"}, {$or: [{spc_info.form: "Pyramidal}, {spc_info.form: "Upright"}]}, {spc_info.growth_rate: "Slow"})
```

## 4

```
db.tree.find({latitude: {$gt: 1000, $lt: 2000}})
```
