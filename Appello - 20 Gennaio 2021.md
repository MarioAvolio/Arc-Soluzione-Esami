# Domanda 1

In mongo db le repliche avvengono mediante un'architettura master-slave (primary-second) in cui le scritture vengono eseguite prima sul nodo primario per poi essere replicate, mediante un approccio incrementale tramite file di log, su un nodo secondario. In Cassandra la replica dei dati avviene tramite una rete peer-to-peer con una struttura ad anello tra i nodi, in particolare le scritture avvengono su un nodo e vengono replicate almeno sui successivi due nodi adiacenti.

# Domanda 3

La qualità di un artefatto indica la sua capacità di soddisfare le esigenze e le aspettative di chi lo usa.
Quindi un dato è di qualità se rispecchia l'uso che se ne deve fare. La dimensione è una caratteristica per descrivere la qualità dei dati. Una sotto-dimenzione è una caratteristica per descrivere una dimensione. Le dimensioni vengono calcolate mediante metriche di valutazione. La qualità dei dati è esprimibile mediante diverse tipologie di dimensioni come l'accuratezza.
L'accuratezza di un valore x è definita come la vicinanza tra x e il valore y del mondo reale che x intende rappresentare. Ci sono due sottodimensioni dell'accuratezza:

- Accuretezza sintattica: riguarda la differenza in termini di sintassi tra un valore e un altro. E' generalmente applicabile solo alle stringhe.
  - Si possono usare metriche inerenti alla distanza tra il dato e il valore di riferimento in un dominio. Ad esempio si può usare la distanza di edit normalizzata (1-(edit(a,b)/n)).
- Accuratezza semantica: più difficile da verificare. Si può usare la cross-validation

# Esercizio 1

## 1

```
match (a:Author)
order by a.statoDiNascita desc
return a
```

## 2

```
match (b:Book) <-[:haScritto]- (a:Author)
where count(a) >= 3
return b
```

## 3

```
match (b:Book) <-[r:haVotato]- (:Reader)
order by r.rating desc
return b
limit 1
```

## 4

```
match (b:Book) <-[:daLeggere]- (r:Reader)
order by count(r) desc
return r
limit 5
```
