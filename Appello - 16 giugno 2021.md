# Domanda 1

CA: il database garantisce che la in caso di richiesta il DBMS risponda sempre con il valore più recente dei dati. Sfortunatamente potrebbe non funzionare in caso di perdita di messaggi.

- Consistency: tutti i nodi possono interagire con i medesimi dati nello stesso momento. Quindi ogni nodo riceve la "scrittura" più recente oppure un errore
- Availability: ogni nodo risponde ad una richista con una risposta positiva o negativa. Non ci possono essere attese indefinite.

AP: Il database è sempre disponibile anche in caso di errori interni, la risposta ad una richiesta quindi è sempre data ma potrebbe non essere consistente, ovvero il dato potrebbe non essere aggiornato rispetto al nodo principale.

- Availability
- Partition Tollerance: il sistema non smette di funzionare anche in caso di perdita di messagi o errori.

# Domanda 2

- Shared Disk:
  - Architetture in cui ogni DBMS condivide lo stesso disco.
  - Ci sono problemi di concorrenza
  - Costo molto elevato
- Shared Nothing:
  - Nessuna condivisione del disco da parte dei diversi DBMS
  - Molto conveniente se si vuole scalare orizontalmente
  - Complessità elevata

# Esercizio 1

## 1

```
match (c:CONSTRUCTORS) -[:QUALIFIED]-> (:QUALIFY {position: 1}) -[:QUALIFIED_FOR]->(:RACE) -[:IN]-> (:CIRCUIT {name: "Sepang International Circuit"})
match (d:DRIVERS) -[:QUALIFIED]-> (:QUALIFY {position: 1}) -[:QUALIFIED_FOR]->(:RACE) -[:IN]-> (:CIRCUIT {name: "Sepang International Circuit"})
return c, d
```

## 2

```
match (:DRIVER) -[:GOT_TIME]-> (t:LAP_TIME) -[:OBTAINED_IN]-> (r:RACE) -[:IN]-> (c:CIRCUIT {name: "Sepang International Circuit"})
order by t.time_ms DESC
return r.date, t.time_ms
limit 3
```

## 3

```
match (:DRIVER {name: "Fernando Alfonso"}) -[:DRIVER_RESULT]-> (:RESULT {position: 1}) <-[:RACE_RESULT]- (r:RACE) -[:IN]-> (c:CIRCUIT {country: "Spain"})
return count(r.date)
```

## 4

```
match (c:CONSTRUCTOR) -[:CONSTRUCTOR_RESULT]-> (r:RESULT {position: 1})
where c.name = "Ferrari" or c.name = "McLaren"
order by c.name
return c.name,  count(r)
```
