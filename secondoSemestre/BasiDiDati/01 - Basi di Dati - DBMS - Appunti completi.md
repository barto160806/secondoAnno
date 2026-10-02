# 01 - DBMS, modello relazionale e transazioni

Appunti spiegati dal primo PDF, da slide 36. Obiettivo: capire cosa fa un DBMS, come rappresenta i dati e perché servono livelli di astrazione, transazioni e controllo della concorrenza.

> [!abstract] Panoramica
> **Livello: da zero | Focus: DDL/DML, modelli di dati, modello relazionale, livelli, indipendenza, ACID, serializzabilità, OLTP/OLAP**

## 1. DDL e DML: struttura del database vs dati

Nel DBMS dobbiamo distinguere due famiglie di operazioni. La prima definisce la struttura; la seconda lavora sui dati contenuti in quella struttura.

| **Linguaggio** | **A cosa serve** | **Domanda pratica** | **Esempi** |
| :--- | :--- | :--- | :--- |
| DDL | Definire o modificare la struttura del database. | Come deve essere fatto il database? | CREATE TABLE, ALTER TABLE |
| DML | Inserire, leggere, modificare o eliminare dati. | Che cosa faccio con i dati? | INSERT, SELECT, UPDATE, DELETE |

Non serve ancora conoscere SQL a memoria. Questi esempi servono solo a vedere la differenza tra struttura e contenuto.

```sql
CREATE TABLE Esami (
    Materia CHAR(5),
    Candidato CHAR(8),
    Voto INT
);
```

DDL: qui stiamo definendo la forma della tabella Esami.

```sql
INSERT INTO Esami
VALUES ('BDSI1', '080709', 30);
```

DML: qui stiamo inserendo una riga dentro una struttura già esistente.

```sql
SELECT Candidato
FROM Esami
WHERE Voto = 30;
```

DML: qui stiamo interrogando i dati già presenti.

> [!tip] DA RICORDARE - DDL vs DML
> DDL = struttura. DML = dati. Se stai decidendo quali colonne esistono, sei nel DDL; se stai inserendo o cercando righe, sei nel DML.

## 2. Prima del modello relazionale: reticolare e gerarchico

Prima del modello relazionale erano diffusi modelli in cui il programma dipendeva molto di più da come i dati erano organizzati e collegati fisicamente.

### 2.1 Modello reticolare

Il modello reticolare può essere immaginato come un grafo: i nodi sono record, gli archi sono link o puntatori. Per raggiungere un dato si seguono collegamenti espliciti.

```text
Studente A  -->  Esame 1  -->  Corso X
           \->  Esame 2  -->  Corso Y
```

> [!warning] Problema principale
> Programma e struttura dei dati sono fortemente accoppiati. Se cambia l organizzazione fisica o il percorso dei puntatori, il programma può dover essere adattato.

### 2.2 Modello gerarchico

Il modello gerarchico organizza i dati come un albero: ogni nodo ha un solo padre e può avere molti figli. È naturale quando il dominio è davvero gerarchico.

```text
Universita
|
+-- Facolta di Ingegneria
|   +-- Informatica
|   +-- Elettronica
|
+-- Facolta di Economia
    +-- Economia
    +-- Finanza
```

Il limite emerge con le relazioni molti-a-molti. Se Luca, Marco e Anna seguono tutti Basi di Dati, un unico nodo Basi di Dati dovrebbe avere più padri, ma un albero non lo consente.

```text
Luca  ----\
Marco -----> Basi di Dati
Anna  ----/
```

> [!tip] DA RICORDARE - Perché il modello gerarchico diventa scomodo
> Ogni nodo può avere un solo genitore. Con relazioni molti-a-molti bisogna duplicare informazioni oppure forzare collegamenti che non rispettano più la struttura ad albero.

## 3. Il modello relazionale: collegare dati tramite valori

Nel 1970 Codd introduce il modello relazionale. L idea fondamentale è rappresentare dati e relazioni tramite valori, non tramite puntatori fisici.

Esempio universitario:

| **STUDENTI** | **Nome** |
| :--- | :--- |
| 101 | Mario |
| 102 | Luca |

| **CORSI** | **Titolo** |
| :--- | :--- |
| BDSI1 | Basi di Dati |
| ALG2 | Algoritmi 2 |

| **Studente** | **Corso** | **Voto** |
| :--- | :--- | :--- |
| 101 | BDSI1 | 30 |
| 101 | ALG2 | 27 |
| 102 | BDSI1 | 25 |

Per capire la riga 101 | BDSI1 | 30 non servono indirizzi di memoria: 101 rimanda a Mario perché è lo stesso valore presente in STUDENTI; BDSI1 rimanda a Basi di Dati perché è lo stesso valore presente in CORSI.

> [!tip] Idea chiave del modello relazionale
> Le relazioni tra i dati vengono ricostruite usando valori comuni. Il programma non deve conoscere dove quei record sono memorizzati fisicamente.

### 3.1 Record e tabelle

Un record rappresenta una singola occorrenza di un oggetto. I suoi campi descrivono le proprietà di quell oggetto.

```text
COD1 | Rossi | Mario | Analista | 1995
```

Un record della tabella STAFF.

Più record dello stesso tipo formano una tabella: tutti hanno la stessa struttura, ma valori diversi.

| **Codice** | **Cognome** | **Nome** | **Ruolo** | **Assunzione** |
| :--- | :--- | :--- | :--- | :--- |
| COD1 | Rossi | Mario | Analista | 1995 |
| COD2 | Bianchi | Pietro | Analista | 1990 |
| COD3 | Neri | Paolo | Amministratore | 1985 |

### 3.2 Esempio pratico: ricostruire un informazione

Supponiamo di avere Mary Smith, un corso di Fisica e una riga nella tabella ESAMI:

```text
STUDENTI: 276454 | Smith | Mary
CORSI:    01 | Fisica | Grant
ESAMI:    276454 | C | 01
```

- Nella riga ESAMI trovi la matricola 276454 e il codice corso 01.
- Cerchi 276454 in STUDENTI e scopri che identifica Mary Smith.
- Cerchi 01 in CORSI e scopri che identifica Fisica.
- Mettendo insieme i valori comuni, sai che Mary Smith ha ottenuto C a Fisica.

## 4. Cenno al modello a oggetti

Nel modello a oggetti un oggetto contiene sia stato sia comportamento. Lo stato è dato dagli attributi; il comportamento dai metodi.

```text
Studente
- nome
- matricola
- annoNascita

metodi:
- iscriviEsame()
- calcolaMedia()
```

Nel modello relazionale, invece, i dati sono organizzati in tabelle e le operazioni sui dati restano concettualmente separate.

## 5. I tre livelli di descrizione di una base di dati

Una base di dati può essere osservata a tre livelli. Questa separazione serve a non legare le applicazioni ai dettagli interni del DBMS.

| **Livello** | **Domanda** | **Che cosa descrive** | **Esempio** |
| :--- | :--- | :--- | :--- |
| Vista logica | Che cosa vede un certo utente? | Una porzione o rappresentazione dei dati. | Mostrare solo Matricola, Nome e Cognome. |
| Schema logico | Come sono strutturati i dati? | Tabelle, attributi e relazioni, senza dettagli fisici. | Tabella STUDENTI: Matricola, Nome, Cognome. |
| Schema fisico | Come vengono memorizzati e raggiunti? | Organizzazione su memoria e strutture ausiliarie. | Indice su Matricola. |

### 5.1 Schema logico

```text
STUDENTI(
    Matricola,
    Nome,
    Cognome,
    AnnoNascita
)
```

Lo schema logico dice quali dati esistono e come sono organizzati, ma non dice su quale settore del disco stanno, quale indice usano o in che ordine sono memorizzati.

### 5.2 Schema fisico

```text
STUDENTI salvata sequenzialmente
Indice su Matricola
ESAMI indicizzata su (Matricola, Corso)
```

Qui compaiono scelte interne al DBMS che servono soprattutto a rendere gli accessi più efficienti.

### 5.3 Vista logica

Una vista descrive come una certa applicazione o un certo utente vede il database. Il database può contenere molti attributi, ma una vista può mostrarne soltanto alcuni.

```text
Database completo: Matricola, Nome, Cognome, DataNascita, CF, Reddito, Indirizzo, Telefono

Vista docente:     Matricola, Nome, Cognome
```

## 6. Indipendenza fisica e indipendenza logica

La separazione tra livelli permette di modificare una parte del sistema senza costringere a modificare tutto il resto.

| **Tipo di indipendenza** | **Che cosa cambia** | **Che cosa idealmente non cambia** | **Esempio** |
| :--- | :--- | :--- | :--- |
| Fisica | Il modo in cui i dati sono memorizzati o raggiunti. | Programmi e schema logico. | Aggiungo un indice su Matricola. |
| Logica | La struttura logica dei dati. | Le applicazioni che non dipendono dalla modifica. | Aggiungo l attributo Email a STUDENTI. |

> [!tip] Trucco mentale
> LOGICO = cosa memorizzo. FISICO = come lo memorizzo. Aggiungere Email cambia ciò che sappiamo dello studente; aggiungere un indice cambia solo come lo troviamo.

## 7. Integrità, sicurezza e affidabilità

Un DBMS non deve soltanto conservare dati: deve anche proteggerne correttezza e disponibilità.

| **Proprietà** | **Significato** | **Esempio** |
| :--- | :--- | :--- |
| Integrità | I dati devono rispettare i vincoli definiti. | Un voto non può essere 47 se il dominio ammesso è 18-30. |
| Sicurezza | Solo utenti autorizzati possono leggere o modificare certi dati. | Uno studente legge i propri voti; un docente può registrarli. |
| Affidabilità | I dati devono sopravvivere a errori, crash e malfunzionamenti. | Dopo un guasto il DBMS deve poter recuperare uno stato corretto. |

## 8. Transazioni e proprietà ACID

Una transazione è una sequenza di operazioni che deve essere trattata come un unica operazione logica.

Esempio classico: trasferire 1000 euro dal conto A al conto B.

```text
1. A = A - 1000
2. B = B + 1000
```

Le due operazioni devono essere considerate insieme. Se il sistema si bloccasse dopo il punto 1, non possiamo lasciare 1000 euro scomparsi.

| **Lettera** | **Proprietà** | **Significato** | **Esempio** |
| :--- | :--- | :--- | :--- |
| A | Atomicità | Tutto o niente: una transazione non può restare eseguita a metà. | Se il bonifico fallisce, si annullano anche le operazioni già eseguite. |
| C | Consistenza | La transazione deve lasciare il database in uno stato che rispetta i vincoli. | Voto sempre nel range ammesso. |
| I | Isolamento | Transazioni concorrenti non devono interferire in modo scorretto. | Due accrediti simultanei non devono cancellarsi a vicenda. |
| D | Durabilità | Dopo il COMMIT le modifiche confermate devono restare permanenti. | Un crash successivo non deve far sparire una transazione già confermata. |

> [!info] COMMIT e ROLLBACK
> COMMIT = confermo definitivamente la transazione. ROLLBACK = annullo gli effetti della transazione quando qualcosa va storto.

## 9. Concorrenza: il problema del lost update

La concorrenza è utile perché più transazioni possono lavorare nello stesso periodo. Il problema nasce quando operano sugli stessi dati senza un controllo adeguato.

Saldo iniziale: 1000. T1 vuole aggiungere 1500; T2 vuole aggiungere 500. Il risultato corretto sarebbe 3000.

```text
T1 legge 1000
T2 legge 1000
T1 scrive 2500
T2 scrive 1500

Risultato finale: 1500  <-- SBAGLIATO
```

> [!info] Che cosa è successo?
> T2 ha scritto un valore calcolato usando il vecchio saldo e ha cancellato l aggiornamento di T1. Questo è un esempio di lost update.

### 9.1 Schedule e serializzabilità

Una sequenza di operazioni concorrenti viene chiamata schedule. Il DBMS vuole che, anche quando le operazioni sono intervallate, l effetto complessivo sia equivalente a una qualche esecuzione seriale.

```text
T1: read(A)
T2: read(A)
T1: write(A)
T2: write(A)
```

> [!info] Definizione intuitiva
> Uno schedule è serializzabile se produce lo stesso effetto di una esecuzione in cui le transazioni vengono eseguite una dopo l altra, in un certo ordine.

Non significa che il DBMS debba davvero eseguire tutto in serie: può mantenere concorrenza, ma deve rispettare regole che garantiscano un risultato corretto.

## 10. Strumenti del DBMS e ruolo del DBA

Il DBMS offre strumenti sia all amministratore sia agli sviluppatori.

| **DBA - Database Administrator** | **Sviluppatori** |
| :--- | :--- |
| Definire e modificare gli schemi. | Creare applicazioni che usano il database. |
| Controllare prestazioni e configurazione. | Produrre report, form, interfacce e grafici. |
| Gestire utenti e permessi. | Collegare linguaggi di programmazione e DBMS. |
| Gestire backup, recovery e ripristino. | Usare query e API per leggere e modificare dati. |

### 10.1 Vantaggi e svantaggi dei DBMS

| **Vantaggi** | **Svantaggi** |
| :--- | :--- |
| Indipendenza dei dati. | Prima di caricare dati strutturati serve uno schema. |
| Recupero efficiente tramite strutture e ottimizzazioni. | Installazione e gestione possono essere complesse. |
| Integrità, sicurezza, concorrenza e recovery centralizzati. | I DBMS tradizionali sono meno naturali per dati molto eterogenei o non strutturati. |
| Riduzione del lavoro che ogni applicazione dovrebbe implementare da zero. | Sono ottimizzati soprattutto per carichi transazionali, non per ogni tipo di analisi. |

## 11. OLTP e OLAP

Gli ultimi concetti del blocco distinguono due tipi di carico molto diversi.

| **Aspetto** | **OLTP** | **OLAP** |
| :--- | :--- | :--- |
| Obiettivo | Supportare operazioni quotidiane. | Analizzare grandi quantità di dati. |
| Operazioni | Molte, piccole, veloci e frequenti. | Poche ma pesanti e complesse. |
| Dati coinvolti | Pochi record per operazione. | Moltissime righe e dati aggregati. |
| Esempio | Registrare un pagamento o un esame. | Calcolare andamento vendite per regione e anno. |

> [!tip] DA RICORDARE - OLTP vs OLAP
> OLTP = far funzionare le operazioni del giorno per giorno. OLAP = analizzare grandi quantità di dati per capire trend e supportare decisioni.

## 12. Riepilogo flash

- DDL definisce la struttura; DML lavora sui dati.
- Nei modelli reticolare e gerarchico il modo di raggiungere i dati era più legato alla struttura.
- Nel modello relazionale dati e legami sono rappresentati tramite valori.
- Record = singola occorrenza; tabella = insieme di record omogenei.
- Vista, schema logico e schema fisico descrivono la stessa base di dati a livelli diversi.
- Indipendenza fisica: posso cambiare come memorizzo i dati senza cambiare le applicazioni.
- Indipendenza logica: posso modificare lo schema logico senza necessariamente riscrivere tutte le applicazioni.
- ACID = Atomicità, Consistenza, Isolamento, Durabilità.
- Uno schedule serializzabile ha lo stesso effetto di una esecuzione seriale corretta.
- OLTP = operatività; OLAP = analisi.

> [!question] AUTOVERIFICA
> 1) Aggiungere un indice su Matricola è una modifica logica o fisica?
> 2) Perché 101 può collegare una riga di ESAMI a Mario senza usare un puntatore?
> 3) Che differenza c è tra COMMIT e ROLLBACK?
> 4) Perché il risultato 1500 nell esempio concorrente è sbagliato?
> 5) Una dashboard che analizza milioni di vendite è OLTP o OLAP?

