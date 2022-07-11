# Domanda 1

- Replica Sincrona: Tale modalità prevede che due o più nodi vengano aggiornati nello stesso momento. Gli aggiornamenti avvengono mendiante una transazione. Se uno o più nodi non sono disponibili allora la transazione non va a buon fine. Se un nodo fallisce allora si esegue un rollback.
  - Vantaggi:
    - alta disponibilità
    - perdita minima dei dati
  - Svantaggi:
    - numero di messaggi richiesti per la sincronizzazione
    - Scarsa scalabilità
- Repliche Asincrone: i cambiamento sono effettuati sul nodo primario per poi essere propagati sui secondari. Il database secondario viene quindi aggiornato successivamente rispetto al database sorgente.
  - Vantaggi:
    - bassi costi
    - scalabilità
    - flessibilità
  - Svantaggi:
    - Possibile perdita di dati.

# Domanda 2

Analizziamo il CAP Theorem:

- Consistency: tutti i nodi possono avere accesso ai dati nello stesso momento. Diversa dalle proprietà ACID in cui per consistenza si intende la garanzia per il passaggio del sistema da uno stato valido ad un altro, rispettando i vincoli di integrità.
- Availability: garanzia che ogni richiesta riceva una risposta positiva o negativa. Non ci possono quindi essere attese indefinite.
- Partition Tollerance: garanzia che il sistema continui ad operare nonostante la perdita di messaggi o fallimenti da parte del sistema.

Posso sceglierne solo due tra essi. All'interno di un gioco multiplayer formato da un numero esponenziale di player si potrebbe pensare ad avere sempre e comunque la _consistency_ dei dati in modo che ogni player non veda l'aggiornamento dei dati dopo diverso tempo. In quest contesto la latenza è molto importante: un player che ha "perso la partita" non può essere visto giocare da alcuni player e da altri no.

Mi aspetto che il sistema continui a funzionare anche in caso di malfunzionamenti, la partita non si può interrompere durante il gioco. Quindi sarebbe opportuno sfruttare la _Partition Tollerance_.

Si può quindi pensare di sfruttare un modello documentale come mongoDB.

# Domanda 3

L'accuratezza di un valore _x_ è definita come la vicinanza tra _x_ e il valore _y_, considerato come la corretta rappresentazione del fenomeno del mondo reale che _x_ intende rappresentare. Si può applicare a tuple e relazioni. Ci sono due sottodimensioni:

- Accuratezza sintattica: controlla se il valore sia presente in un insieme di valori rappresentanti il dominio trattato. L'operazione è molto simile alla ricerca in un vocabolario. Generalmente si applica a stringhe. Le metriche basate su stringhe adottano una funzione che misura la distanza tra esse (es. distanza di edit). La distanza di edit normalizzata si può definire come **1-(edit(a,b)/n)**, dove n è il numero massimo di simboli. Il valore è compreso tra 0 e 1, più si avvicina ad 1 e più le strignhe sono simili.
- Accuratezza semantica: molto più difficile da verificare. Può essere effettuata in modo manuale o tramite un'altra sorgente con cui effettuare cross-validation.
