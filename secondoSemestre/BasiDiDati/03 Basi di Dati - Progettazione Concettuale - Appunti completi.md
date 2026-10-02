# 03 - Progettazione concettuale

Appunti spiegati dal terzo PDF. Modello E-R, cardinalità, identificatori e generalizzazioni con esempi passo-passo.

> [!abstract] Panoramica
> **Livello: da zero | Nessun prerequisito SQL | Focus: capire e disegnare correttamente uno schema E-R**

## 1. Schema e istanza: la distinzione da non confondere

In una base di dati distinguiamo schema e istanza. Lo schema descrive la struttura con cui rappresentiamo i dati; l'istanza contiene i valori effettivi presenti in un certo momento.

| **Concetto** | **Aspetto** | **Quanto cambia?** | **Esempio** |
| :--- | :--- | :--- | :--- |
| Schema | Intensionale | Poco nel tempo | STUDENTE ha Matricola, Nome, Cognome |
| Istanza | Estensionale | Può cambiare continuamente | 12345, Marco, Rossi |

```text
SCHEMA:  STUDENTE(Matricola, Nome, Cognome)
ISTANZA: (12345, Marco, Rossi)
         (67890, Anna, Bianchi)
```

> [!tip] DA RICORDARE - Perché conta
> La progettazione lavora soprattutto sullo schema: decide la struttura. Le istanze arriveranno dopo, quando il database verra popolato.

## 2. Metodologia di progettazione

Una metodologia di progettazione organizza il lavoro in fasi, sceglie una strategia per ciascuna fase e definisce come rappresentare input e output. Una buona metodologia deve essere generale, produrre risultati di qualità ed essere utilizzabile in pratica.

> [!tip] DA RICORDARE - Principio centrale
> Separare nettamente le decisioni su CHE COSA rappresentare dalle decisioni su COME realizzarlo.

```text
Requisiti
   |
   v
Progettazione concettuale  ->  Schema concettuale   (CHE COSA)
   |
   v
Progettazione logica       ->  Schema logico        (COME, a livello logico)
   |
   v
Progettazione fisica       ->  Schema fisico        (COME, in memoria)
```

## 3. Modelli concettuali e logici

I modelli concettuali servono a descrivere classi di dati e correlazioni indipendentemente dal DBMS. Sono utili anche per comunicare e documentare il progetto. I modelli logici organizzano invece i dati in una forma disponibile ai programmi che useranno il DBMS, restando indipendenti dalle strutture fisiche.

> [!tip] DA RICORDARE - In questa parte del corso
> Lavoriamo soprattutto con il modello Entità-Relazione (E-R), uno dei modelli concettuali più diffusi.

## 4. I costrutti del modello E-R

I costrutti fondamentali introdotti nel PDF sono:

- Entità
- Associazione (relationship)
- Attributo
- Identificatore
- Generalizzazione

Il diagramma E-R non e ancora una tabella SQL. È una mappa concettuale della realtà che deve essere comprensibile anche prima di scegliere il DBMS.

## 5. Entità

Un'entità è una classe di oggetti, fatti, persone o cose dell'applicazione con proprietà comuni ed esistenza autonoma nel dominio.

- Impiegato
- Fattura
- Città
- Conto corrente
- Ordine
- Studente

### 5.1 Entità vs occorrenza (istanza) di entità

ENTITÀ è la classe descritta nello schema; OCCORRENZA e un singolo elemento reale appartenente a quella classe.

```text
Entità: STUDENTE
Occorrenze: Marco Rossi, Anna Bianchi, Luca Verdi
```

> [!tip] DA RICORDARE - Nel diagramma
> Rappresentiamo l'entità STUDENTE, non disegniamo uno per uno Marco, Anna e Luca. Lo schema descrive la struttura, non i dati correnti.

### 5.2 Nomi

Il nome deve identificare l'entità in modo univoco ed essere espressivo. La convenzione suggerita e usare il singolare: Studente, Libro, Città, non Studenti o Libri.

## 6. Associazioni

Un'associazione è un legame logico significativo fra due o più entità. Non ha vita autonoma: ha senso perché collega le entità coinvolte.

```text
PERSONA -- Residenza -- CITTÀ
IMPIEGATO -- Afferenza -- DIPARTIMENTO
LIBRO -- Scritto -- AUTORE
```

### 6.1 Associazione vs occorrenza di associazione

L'associazione nello schema descrive un tipo di legame; una sua occorrenza e un singolo legame concreto fra specifiche occorrenze di entità.

```text
Associazione: RESIDENZA(Persona, Città)
Occorrenza: (Marco Rossi, Roma)
```

Per un'associazione binaria, ogni occorrenza e una coppia. Per un'associazione n-aria, ogni occorrenza e una n-upla: un elemento per ciascuna entità coinvolta.

## 7. Nomi delle associazioni e associazioni multiple

Anche le associazioni devono avere nomi univoci ed espressivi. Il PDF suggerisce il singolare e, quando possibile, sostantivi invece di verbi.

Le stesse due entità possono essere collegate da più associazioni diverse.

```text
IMPIEGATO -- Residenza     -- CITTÀ
IMPIEGATO -- SedeDiLavoro -- CITTÀ
```

> [!tip] DA RICORDARE - Perché servono due associazioni
> La stessa coppia di tipi può avere significati diversi. Un impiegato può risiedere in Roma ma lavorare in Milano: i due legami non sono intercambiabili.

## 8. Associazioni n-arie e ricorsive

### 8.1 Associazione n-aria

Un'associazione può coinvolgere più di due entità. Nel PDF compare una fornitura che collega Fornitore, Prodotto e Dipartimento. Il significato del fatto dipende contemporaneamente da tutti i partecipanti.

```text
FORNITORE
     \
      FORNITURA -- PRODOTTO
     /
DIPARTIMENTO
```

### 8.2 Associazione ricorsiva

Un'associazione ricorsiva coinvolge due volte la stessa entità. In questo caso i ruoli sono fondamentali per capire chi fa cosa.

```text
PERSONA -- Conoscenza -- PERSONA
SOVRANO -- Successione -- SOVRANO
           ruoli: Predecessore / Successore
```

## 9. Attributi e dominio

Un attributo è una proprietà elementare di un'entità o di un'associazione. A ogni occorrenza associa un valore appartenente a un insieme chiamato dominio dell attributo.

```text
STUDENTE: Matricola, Nome, Cognome
CORSO: Codice, Titolo
ESAME/associazione: Voto, Data
```

> [!tip] DA RICORDARE - Dominio
> Il dominio è l'insieme dei valori ammessi per un attributo. Per esempio, il dominio di Voto potrebbe essere un insieme di interi secondo le regole adottate dal sistema.

## 10. Attributi composti

Un attributo composto raggruppa attributi più piccoli che hanno senso insieme. Il PDF usa Indirizzo come esempio.

```text
INDIRIZZO
|- Via
|- Numero civico
|- CAP
```

La scelta fra usare il valore composto o le sue parti dipende dalle operazioni previste. Se dobbiamo cercare tutti gli utenti di un certo CAP, è importante avere CAP come componente esplicita.

## 11. Esempio guidato: societa

Requisito: una societa ha più dipartimenti, localizzati in sedi; gli impiegati afferiscono ai dipartimenti da una certa data; un impiegato dirige il dipartimento; ogni impiegato partecipa a uno o più progetti con un budget.

Passo 1 - entità evidenti:

- IMPIEGATO
- DIPARTIMENTO
- PROGETTO
- SEDE/CITTÀ a seconda del livello di dettaglio

Passo 2 - associazioni:

```text
IMPIEGATO -- Afferenza -- DIPARTIMENTO      [Data]
IMPIEGATO -- Direzione -- DIPARTIMENTO
IMPIEGATO -- Partecipazione -- PROGETTO
DIPARTIMENTO -- Collocazione/Composizione -- SEDE
```

Passo 3 - attributi: Impiegato può avere Codice, Cognome, Telefono; Progetto Nome e Budget; Sede un Indirizzo composto da Via, CAP, Città.

> [!tip] DA RICORDARE - Osservazione
> La data di afferenza descrive il rapporto fra un impiegato e un dipartimento, quindi è naturale attribuirla all'associazione Afferenza, non a Impiegato o Dipartimento presi singolarmente.

## 12. Cardinalità di un'associazione

La cardinalità associa a ogni entità partecipante una coppia (minimo, massimo). Indica quante occorrenze dell'associazione possono o devono coinvolgere una singola occorrenza di quell'entità.

| **Valore** | **Significato** |
| :--- | :--- |
| min = 0 | Partecipazione opzionale: un'occorrenza può non avere alcun collegamento. |
| min = 1 | Partecipazione obbligatoria: deve avere almeno un collegamento. |
| max = 1 | Partecipazione esclusiva: al massimo un collegamento. |
| max = N | Nessun limite massimo prefissato nel modello. |

> [!tip] DA RICORDARE - Metodo per leggerle
> Leggi la coppia dal punto di vista di UNA singola occorrenza dell'entità su cui e scritta. Chiedi: quante occorrenze dell altra entità posso/devo collegare?

## 13. Esempio di cardinalità: Residenza

```text
PERSONA  -- Residenza --  CITTÀ
 (1,1)                  (0,N)
```

Lettura: ogni Persona deve essere collegata a esattamente una Città; una Città può avere zero, una o molte Persone residenti.

> [!question] CHECK - Perché Persona ha (1,1)?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Perché il requisito assunto dice che ogni persona deve avere una e una sola città di residenza.

> [!question] CHECK - Perché Città ha (0,N)?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Perché una città potrebbe non avere persone del nostro database e, se ne ha, potrebbero essere molte.

## 14. Classificazione tramite cardinalità massime

| **Tipo** | **Forma tipica** | **Esempio** |
| :--- | :--- | :--- |
| 1:1 | (..,1) - (..,1) | Ordine - Vendita - Fattura (a seconda dei requisiti) |
| 1:N | (..,1) - (..,N) | Persona - Residenza - Città |
| N:M | (..,N) - (..,N) | Studente - Esame/Frequenza - Corso |

Il tipo 1:1, 1:N o N:M guarda soltanto i massimi. I minimi servono invece a capire se la partecipazione è obbligatoria o opzionale.
> [!warning]- Spiegazione dettagliata per capire meglio
>
> Attenzione: ci sono **due notazioni diverse** che possono sembrare simili.
>
> Quando vedi una cardinalità scritta così:
>
> ```text
> (0,1)
> (1,1)
> (0,N)
> (1,N)
> ```
>
> ogni coppia significa sempre:
>
> ```text
> (minimo, massimo)
> ```
>
> Quindi:
>
> - `(0,1)` → minimo `0`, massimo `1`
> - `(1,1)` → minimo `1`, massimo `1`
> - `(0,N)` → minimo `0`, massimo `N`
> - `(1,N)` → minimo `1`, massimo `N`
>
> Il **minimo** indica se la partecipazione è obbligatoria oppure opzionale:
>
> - `0` → partecipazione opzionale
> - `1` → partecipazione obbligatoria
>
> Il **massimo** indica invece quante volte al massimo un elemento può partecipare:
>
> - `1` → al massimo una volta
> - `N` → più volte
>
> ---
>
> Quando invece diciamo che un'associazione è:
>
> ```text
> 1:1
> 1:N
> N:M
> ```
>
> quel `1` e quell'`N` **non sono minimo e massimo**.
>
> Rappresentano i **massimi delle cardinalità sui due lati dell'associazione**.
>
> ### Esempio
>
> ```text
> Persona ---- Residenza ---- Città
>
> Persona (1,1)
> Città   (0,N)
> ```
>
> Per classificare l'associazione prendiamo solo il **secondo valore** delle due coppie:
>
> ```text
> Persona → massimo 1
> Città   → massimo N
> ```
>
> quindi abbiamo:
>
> ```text
> 1:N
> ```
>
> I valori minimi ci dicono invece qualcosa in più:
>
> - `(1,1)` sulla Persona → ogni persona **deve** essere collegata a una città e al massimo a una;
> - `(0,N)` sulla Città → una città può avere **zero, una o molte persone** collegate.
>
> Anche:
>
> ```text
> (0,1) ---- (1,N)
> ```
>
> è comunque una relazione `1:N`, perché i massimi sono ancora `1` e `N`.
>
> Ciò che cambia è l'**obbligatorietà** indicata dai minimi.
>
> ### Regola pratica
>
> ```text
> (...,1) ---- (...,1)  → 1:1
>
> (...,1) ---- (...,N)  → 1:N
>
> (...,N) ---- (...,N)  → N:M
> ```
>
> **In breve:**
>
> - i **massimi** classificano la relazione (`1:1`, `1:N`, `N:M`);
> - i **minimi** indicano se la partecipazione è opzionale (`0`) oppure obbligatoria (`1`).

> [!warning] ATTENZIONE - Errore tipico
> Dire "1:N" non basta per conoscere tutto il vincolo. (0,1)-(1,N) e (1,1)-(0,N) sono entrambe 1:N, ma impongono obbligatorieta diverse.

## 15. Cardinalità degli attributi

Anche un attributo può avere cardinalità minima e massima. Se non viene indicata, si intende normalmente (1,1).

| **Cardinalità** | **Significato** | **Esempio** |
| :--- | :--- | :--- |
| (1,1) | Esattamente un valore. | Nome obbligatorio singolo |
| (0,1) | Valore opzionale e singolo. | Numero patente per chi può non averla |
| (0,N) | Zero o più valori. | Targhe auto se una persona può possederne più di una |
| (1,N) | Almeno un valore, eventualmente molti. | Recapiti se il requisito ne impone almeno uno |

## 16. Identificatori: riconoscere univocamente le occorrenze

Un identificatore permette di distinguere in modo univoco una singola occorrenza di un'entità. Ogni entità deve avere almeno un identificatore, ma può averne più di uno.

### 16.1 Identificatore interno

E costituito da uno o più attributi dell'entità stessa.

```text
AUTOMOBILE
- Targa   <- identificatore interno
- Modello
- Colore
```

Un identificatore può essere composto: per esempio, se nessun singolo attributo basta, la combinazione di più attributi può identificare l'occorrenza.

### 16.2 Identificatore esterno

Usa anche un'entità esterna attraverso un'associazione. E utile quando un valore è univoco solo all'interno di un certo contesto.

```text
STUDENTE -- Iscrizione -- UNIVERSITA
Matricola

Se la matricola e unica solo dentro una specifica universita,
la coppia (Universita, Matricola) identifica lo studente nel dominio complessivo.
```

> [!tip] DA RICORDARE - Condizione importante
> Una identificazione esterna è possibile solo attraverso un'associazione alla quale l'entità da identificare partecipa con cardinalità (1,1).

Per un'associazione non serve normalmente un identificatore separato: una sua occorrenza e già determinata dalle occorrenze delle entità che collega.
>[!example]- Studente ---- Iscrizione ---- UNIVERSITA'
>```
STUDENTE
> - Matricola: 1234
> ```
> 
> Potrebbe sembrare sufficiente. Ma immagina che la matricola `1234` possa esistere sia alla Sapienza sia a Tor Vergata.
> 
> Quindi:
> 
> ```
> Matricola = 1234
> ```
> 
> da sola non basta.
> 
> Devo sapere anche:
> 
> ```
> Università = Sapienza
> ```
> 
> Allora l’identificazione diventa:
> 
> ```
> Studente = (Università, Matricola)
> ```
> 
> Qui `Matricola` è un attributo dello Studente, ma per identificarlo completamente devo appoggiarmi anche all’entità esterna `Università`.
> 
> Quindi:
> 
> ```
> STUDENTE ---- Iscrizione ---- UNIVERSITÀ
>     |                            |
>  Matricola                    Nome
> ```
> 
> L’identificatore dello Studente è concettualmente:
> 
> ```
> Matricola + Università
> ```
> 
> Questo è un **identificatore esterno**.
> 
> ## 17. Generalizzazione e specializzazione
> 
> Una generalizzazione collega un'entità padre E a una o più entità figlie E1, E2, ... che rappresentano casi particolari del padre. Il padre contiene le caratteristiche comuni; le figlie aggiungono caratteristiche specifiche.
> 
> ```text
>              DIPENDENTE
>           /      |       \
>      IMPIEGATO FUNZIONARIO DIRIGENTE
> ```
> 
> Ogni occorrenza di una figlia e anche un'occorrenza del padre. Le proprietà del padre sono significative per tutte le figlie e vengono ereditate.

## 18. Ereditarietà

Tutti gli attributi, le associazioni e le altre generalizzazioni del padre vengono ereditati dalle entità figlie. Non serve ripeterli nello schema.

```text
PERSONA: CodiceFiscale, Nome, Età
   |
   +-- STUDENTE: Matricola
   +-- LAVORATORE: Stipendio

STUDENTE possiede anche CodiceFiscale, Nome ed Età per ereditarietà.
```

> [!tip] DA RICORDARE - Vantaggio
> L'ereditarietà evita duplicazioni nello schema è rende evidente quali proprietà sono comuni a più categorie.

## 19. Generalizzazioni totali/parziali ed esclusive/sovrapposte

| **Classificazione** | **Domanda da farti** | **Significato** |
| :--- | :--- | :--- |
| Totale | Ogni padre appartiene ad almeno una figlia? | Si: tutte le occorrenze del padre sono coperte. |
| Parziale | Può esistere un padre che non appartiene a nessuna figlia? | Si. |
| Esclusiva | Un padre può appartenere a più figlie insieme? | No: al massimo una. |
| Sovrapposta | Un padre può appartenere a più figlie insieme? | Si. |

>[!example]- 
Persona -> Studente, Lavoratore: può essere parziale perché esistono persone che non sono ne studenti ne lavoratori; può essere sovrapposta perché una persona può essere contemporaneamente studente e lavoratore.

>[!example]- 
Esempio Persona -> Uomo, Donna (nel modello binario semplificato delle slide): può essere considerata totale ed esclusiva secondo i requisiti assunti nell'esempio.

## 20. Sottoinsieme e gerarchie multiple

Le gerarchie possono avere più livelli e un'entità può partecipare a più gerarchie. Se una generalizzazione ha una sola entità figlia, si parla di sottoinsieme.

> [!tip] DA RICORDARE - Non classificare per abitudine
> Totale/parziale ed esclusiva/sovrapposta dipendono dai requisiti del dominio, non dal nome delle entità. Devi sempre leggere la frase del problema.

## 21. Esercizio delle persone: ragionamento passo-passo

Requisiti del PDF: le persone hanno CF, cognome ed età; gli uomini anche posizione militare; gli impiegati hanno stipendio e possono essere segretari, direttori o progettisti; un progettista può essere anche responsabile di progetto; gli studenti non possono essere impiegati; esistono persone che non sono ne impiegati ne studenti.

Passo 1 - superclasse: PERSONA con CF, Cognome, Età.

Passo 2 - sesso nel modello dell esercizio: UOMO e DONNA come specializzazioni di PERSONA; UOMO aggiunge PosizioneMilitare.

Passo 3 - ruolo: IMPIEGATO e STUDENTE sono specializzazioni di PERSONA. Il requisito "gli studenti non possono essere impiegati" impone disgiunzione fra queste due classi.

Passo 4 - la generalizzazione Persona -> Impiegato/Studente è parziale, perché il testo dice che esistono persone che non appartengono a nessuna delle due.

Passo 5 - IMPIEGATO si specializza in SEGRETARIO, DIRETTORE, PROGETTISTA; PROGETTISTA può avere il sottoinsieme RESPONSABILE.

> [!tip] DA RICORDARE - Idea da imparare
> I diagrammi E-R non si disegnano "a intuito": ogni cardinalità e ogni vincolo di generalizzazione deve essere giustificato da una frase del requisito.

## 22. Documentazione dello schema concettuale

Un diagramma da solo non basta. Il PDF introduce la documentazione associata allo schema: dizionario dei dati per entità e associazioni e vincoli non esprimibili graficamente.

| **Documento** | **Cosa contiene** |
| :--- | :--- |
| Dizionario delle entità | Nome, descrizione, attributi, identificatori e significato delle entità. |
| Dizionario delle associazioni | Nome, entità coinvolte, significato, attributi e vincoli rilevanti. |
| Vincoli non esprimibili | Regole del dominio che il diagramma E-R non riesce a rappresentare direttamente. |

> [!tip] DA RICORDARE - Perché serve
> La documentazione elimina ambiguità e rende lo schema comprensibile anche a chi non ha partecipato alla progettazione.

## 23. Raccolta dei requisiti: quanto devono essere precisi?

Il PDF mostra un esempio bibliografico per evidenziare che requisiti troppo generici non permettono una buona progettazione. Dire semplicemente "gestire riferimenti bibliografici" è insufficiente se non si specifica quali tipi di pubblicazione esistono, quali dati vanno memorizzati e quali operazioni devono essere supportate.

> [!tip] DA RICORDARE - Requisito ben formulato
> Deve essere completo, non ambiguo e abbastanza preciso da far emergere oggetti dell'applicazione, relazioni, attributi rilevanti e operazioni previste sui dati.

## 24. Esempio completo da zero: universita

Requisito: "Ogni studente ha una matricola e può sostenere più esami. Ogni esame riguarda un corso, ha data e voto. Ogni corso ha codice e titolo ed e tenuto da un docente. Un docente può tenere più corsi."

Passo 1 - entità: STUDENTE, CORSO, DOCENTE. Possiamo trattare ESAME come entità oppure, se ci interessa soprattutto il fatto che uno studente sostiene un corso in una certa data con un voto, come associazione con attributi. La scelta dipende dal livello di autonomia che il requisito attribuisce all esame.

```text
Soluzione concettuale semplice:

STUDENTE -- Sostiene -- CORSO
             |   |
           Data  Voto

DOCENTE -- Docenza -- CORSO
```

Cardinalità plausibili, se il requisito è quello sopra:

- Sostiene: STUDENTE (0,N), CORSO (0,N), perché uno studente può ancora non aver sostenuto esami e un corso può non avere ancora esami registrati.
- Docenza: DOCENTE (0,N), CORSO (1,1), se ogni corso deve avere un solo docente mentre un docente può tenere più corsi.

> [!tip] DA RICORDARE - Importante
> Queste cardinalità sono corrette solo rispetto ai requisiti dichiarati. Se il testo cambiasse (per esempio co-docenza), cambierebbe anche lo schema.

## 25. Errori tipici da evitare

- Confondere entità e singola occorrenza: STUDENTE è la classe, Marco e una sua occorrenza.
- Usare un attributo per un concetto che in realtà ha proprietà e relazioni proprie.
- Scrivere 1:N senza sapere quale lato e N e senza specificare i minimi.
- Dimenticare che (0,1) significa opzionale ma al massimo uno, mentre (1,1) significa obbligatorio ed esattamente uno.
- Mettere su un'entità un attributo che descrive invece un'associazione (es. Data di afferenza).
- Dimenticare un identificatore per un'entità.
- Inventare vincoli di generalizzazione non presenti nei requisiti.
- Pensare alle tabelle o a SQL prima di aver stabilito il significato del dominio.

## 26. Mappa mentale finale

```text
REQUISITI
  -> ENTITÀ + ATTRIBUTI
  -> ASSOCIAZIONI  [cardinalità min/max]
  -> IDENTIFICATORI
  -> GENERALIZZAZIONI
       - ereditarietà
       - totale/parziale
       - esclusiva/sovrapposta
=> SCHEMA CONCETTUALE DOCUMENTATO
```

## 27. Autoverifica rapida

> [!question] CHECK - STUDENTE e Marco Rossi sono la stessa cosa?
> > [!success]- Soluzione *(clicca per mostrare)*
> > No. STUDENTE e l'entità/classe nello schema; Marco Rossi e una sua occorrenza.

> [!question] CHECK - In Persona -- Residenza -- Città con Persona (1,1), può esistere una persona senza città?
> > [!success]- Soluzione *(clicca per mostrare)*
> > No, perché il minimo e 1.

> [!question] CHECK - Un attributo (0,N) cosa significa?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Può essere assente oppure avere più valori.

> [!question] CHECK - Quando un identificatore e esterno?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Quando per identificare un'occorrenza serve anche un'entità esterna collegata tramite associazione.

> [!question] CHECK - Persona -> Studente/Lavoratore, se una persona può essere entrambe, è esclusiva o sovrapposta?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Sovrapposta.

> [!question] CHECK - Se esistono persone che non sono ne Studente ne Lavoratore, la generalizzazione è totale o parziale?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Parziale.

