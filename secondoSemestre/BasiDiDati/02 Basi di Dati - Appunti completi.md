# 02 - Modelli di dati e progettazione

Appunti spiegati dal secondo PDF. Obiettivo: capire la progettazione prima di pensare a tabelle e SQL.

> [!abstract] Panoramica
> **Livello: da zero | Focus: modellazione, entità, associazioni, cardinalità, gerarchie, requisiti**

## 1. Prima idea fondamentale: prima si capisce la realtà, poi si costruisce il database

Prima di progettare un database bisogna capire la realtà che vogliamo rappresentare e scegliere quali aspetti sono davvero rilevanti. Il database arriva dopo: prima viene il modello.

Esempio universitario. Prima di scrivere qualunque istruzione SQL, ragioniamo su ciò che esiste nel dominio:

- Studenti
- Corsi
- Esami
- Docenti

Poi ragioniamo sui legami:

- un docente insegna un corso;
- uno studente segue uno o più corsi;
- un esame riguarda un corso e coinvolge uno studente.

```text
REALTÀ  ->  MODELLO  ->  DATABASE
```

La modellazione e il ponte fra il problema reale e la base di dati.

> [!tip] DA RICORDARE - Modellazione
> È il processo con cui trasformiamo una porzione di realtà (dominio del discorso) in una descrizione formale, abbastanza precisa da poter poi essere realizzata in un DBMS.

## 2. La progettazione nel ciclo di vita di un sistema informativo

La progettazione della base di dati non e isolata: fa parte del ciclo di vita del sistema informativo. Si parla di ciclo perché, anche dopo la messa in funzione, nuovi requisiti possono costringerci a tornare a fasi precedenti.

| **Fase** | **Domanda pratica** | **Risultato tipico** |
| :--- | :--- | :--- |
| Studio di fattibilita | Vale la pena farlo? | Priorita, costi, tempi e risorse |
| Raccolta e analisi requisiti | Che cosa deve gestire il sistema? | Requisiti chiari e non ambigui |
| Progettazione | Come rappresentiamo dati e funzioni? | Schemi e scelte progettuali |
| Implementazione | Come lo realizziamo? | Database e applicazioni |
| Validazione e collaudo | E corretto e completo? | Verifica del funzionamento |
| Funzionamento | Come opera in produzione? | Sistema operativo e manutenzione |

> [!tip] DA RICORDARE - Per l esame
> Non confondere progettazione e implementazione. Progettare significa decidere la struttura; implementare significa tradurla in un sistema concreto (ad esempio tabelle e comandi SQL).

## 3. Modello astratto e modello dei dati

Un modello astratto è una rappresentazione formale di idee e conoscenze relative a un fenomeno. Non è una copia completa della realtà: seleziona ciò che e utile per lo scopo.

Esempio: una mappa della metropolitana non disegna ogni palazzo della città. Mostra stazioni, linee e collegamenti, cioè gli elementi che servono per orientarsi. Un modello di dati fa qualcosa di simile con le informazioni.

> [!tip] DA RICORDARE - Stessa realtà, modelli diversi
> La stessa realtà può essere modellata in modi diversi e a livelli di astrazione diversi. La scelta dipende da ciò che dobbiamo fare con i dati.

Un modello dei dati definisce le strutture (costruttori) che possiamo usare per organizzare i dati e le loro relazioni. Nel modello relazionale il costruttore fondamentale e la relazione, che possiamo pensare intuitivamente come una tabella composta da record omogenei.

## 4. Modelli concettuali e modelli logici

| **Tipo** | **A cosa serve** | **Esempio** |
| :--- | :--- | :--- |
| Concettuale | Descrive la realtà in modo indipendente dal DBMS e dalle strutture fisiche. | Entità-Relazione (E-R) |
| Logico | Organizza i dati in una forma gestibile da un DBMS, ma ancora indipendente dalla memorizzazione fisica. | Relazionale, gerarchico, reticolare, a oggetti |

```text
Requisiti  ->  Schema concettuale  ->  Schema logico  ->  Schema fisico
```

Esempio: nel modello concettuale diciamo che esistono STUDENTE e CORSO e che uno studente FREQUENTA corsi. Nel modello logico relazionale, più avanti, questa idea verra tradotta in strutture compatibili con tabelle. Nel modello fisico si sceglieranno indici e organizzazione dei dati su memoria.

> [!question] CHECK - Se aggiungo un indice per velocizzare la ricerca, sto modificando il modello concettuale?
> > [!success]- Soluzione *(clicca per mostrare)*
> > No. Un indice e una scelta fisica; non cambia ciò che la realtà significa.

## 5. Quattro domande della modellazione

| **Aspetto** | **Domanda** | **Esempio universitario** |
| :--- | :--- | :--- |
| Ontologico | Che cosa rappresento? | Studenti, corsi, esami, docenti |
| Logico | Con quali meccanismi di astrazione? | Entità, proprietà, associazioni, gerarchie |
| Linguistico | Con quale linguaggio/notazione? | Diagramma E-R e sue convenzioni |
| Pragmatico | Come costruisco il modello? | Metodo, passi e controlli sui requisiti |

> [!tip] DA RICORDARE - Dominio del discorso
> E la porzione di realtà che interessa all'applicazione. Non dobbiamo descrivere tutto il mondo: solo i fatti utili allo scopo del sistema.

## 6. Conoscenza concreta e conoscenza astratta

Il PDF distingue due tipi di conoscenza centrali. La conoscenza concreta riguarda i fatti specifici che esistono nel dominio; la conoscenza astratta descrive la struttura generale e i vincoli che quei fatti devono rispettare.

| **Tipo** | **Che cosa contiene**                                   | **Esempio**                                                                 |
| :------- | :------------------------------------------------------ | :-------------------------------------------------------------------------- |
| Concreta | Entità, proprietà, collezioni, associazioni specifiche. | Marco è uno studente; Marco frequenta Basi di Dati.                         |
| Astratta | Tipi, struttura, vincoli e regole generali.             | Ogni studente ha una matricola; un voto deve rispettare i vincoli previsti. |

Esiste anche conoscenza procedurale: operazioni di base, operazioni degli utenti e modalita di comunicazione con il sistema. In questa parte del corso l'attenzione e soprattutto su conoscenza concreta e astratta.

## 7. Entità, proprietà, tipi e collezioni

### 7.1 Entità

Un'entità è qualcosa del dominio di cui interessa rappresentare alcuni fatti. Può essere una persona, un oggetto, un evento o anche un concetto, purché abbia senso autonomo nel problema.

- Studente Maurizio
- una specifica automobile
- una copia fisica di un libro
- un prestito in biblioteca

### 7.2 Proprietà

Una proprietà descrive una caratteristica di una certa entità. È una coppia concettuale del tipo (attributo, valore).

```text
Studente:  Nome = "Marco"
           Matricola = "123456"
           Email = "marco@uni.it"
```

Le proprietà possono essere classificate in vari modi:

| **Contrasto** | **Significato** | **Esempio** |
| :--- | :--- | :--- |
| Primitiva / strutturata | Un solo valore semplice / valore composto da parti. | Età / Indirizzo(Via, CAP, Numero) |
| Obbligatoria / opzionale | Deve esserci / può mancare. | Matricola / secondo telefono |
| Univoca / multivalore | Un valore / più valori. | Codice fiscale / numeri di telefono |
| Costante / variabile | Non cambia / può cambiare nel tempo. | Data nascita / indirizzo |
| Calcolata / non calcolata | Derivata da altri dati / memorizzata direttamente. | Età da data nascita / data nascita |

### 7.3 Tipo di entità e collezione

Ogni entità appartiene a un tipo che ne specifica la natura. Una collezione è un insieme variabile nel tempo di entità omogenee dello stesso tipo.

```text
Tipo: STUDENTE
Proprietà previste: Nome, AnnoNascita, Matricola, Email
Collezione: tutti gli studenti presenti nel dominio in un certo momento
```

> [!tip] DA RICORDARE - Attenzione
> Tipo e collezione non sono la stessa cosa: il tipo dice come sono fatti gli oggetti; la collezione e l'insieme degli oggetti effettivamente presenti in un certo momento.

## 8. Quando una cosa è proprietà e quando è entità?

Non esiste una risposta assoluta: dipende dal livello di dettaglio richiesto. Un fatto che in un sistema e una semplice proprietà può diventare un'entità in un sistema più ricco.

Esempio bibliografico: se dell autore ci interessa solo il nome, Autore può essere trattato come proprietà di Libro. Se invece vogliamo memorizzare nazionalita, anno di nascita e collegare lo stesso autore a molti libri, conviene modellare AUTORE come entità autonoma.

```text
Versione semplice:
LIBRO(Titolo, Autore)

Versione più ricca:
AUTORE(Nome, Nazionalita, AnnoNascita)
LIBRO(Titolo, ...)
AUTORE -- HaScritto -- LIBRO
```

> [!tip] DA RICORDARE - Regola pratica
> Se un concetto ha proprie proprietà, deve essere condiviso da più oggetti o deve partecipare ad altre associazioni, spesso conviene trattarlo come entità.

## 9. Modellazione a oggetti: oggetto, tipo e classe

Il PDF usa il modello a oggetti come meccanismo di astrazione. Qui non devi pensare subito alla programmazione Java: il punto e distinguere individuo, struttura e insieme degli individui.

| **Concetto** | **Significato intuitivo** | **Esempio** |
| :--- | :--- | :--- |
| Oggetto | La rappresentazione di una singola entità, con identita, stato e comportamento. | Lo studente Marco Rossi |
| Stato | I valori delle sue proprietà in un certo momento. | Matricola=123, Email=... |
| Comportamento | Operazioni/metodi a cui può rispondere. | calcolaMedia(), cambiaEmail() |
| Tipo oggetto | Definisce quali messaggi/attributi sono previsti. | Tipo Studente |
| Classe | Insieme modificabile di oggetti dello stesso tipo. | Tutti gli studenti iscritti |

> [!tip] DA RICORDARE - Nel diagramma E-R
> L'attenzione grafica e soprattutto su collezioni/entità e associazioni. Il tipo oggetto non viene rappresentato come elemento separato: e implicito nella descrizione dell'entità e dei suoi attributi.

## 10. Associazioni: come colleghiamo le entità

Un'istanza di associazione e un fatto che collega due o più entità. Un'associazione R(X,Y) e l'insieme delle istanze di legame tra elementi delle collezioni X e Y.

```text
STUDENTE Marco  -- Frequenta -->  CORSO Basi di Dati
AUTORE Atzeni   -- HaScritto --> LIBRO Basi di dati
```

Il prodotto cartesiano X x Y rappresenta tutte le coppie teoricamente possibili fra gli elementi di X e Y. L'associazione contiene solo le coppie che corrispondono a legami reali nel dominio.

> [!tip] DA RICORDARE - Esempio
> Se abbiamo 3 studenti e 2 corsi, esistono 3 x 2 = 6 coppie possibili. FREQUENTA conterra soltanto le coppie che rappresentano iscrizioni realmente esistenti.

## 11. Molteplicità delle associazioni: 1:1, 1:N, N:M

La molteplicità descrive quanti elementi di una collezione possono essere collegati a un elemento dell altra. Il PDF parla di univocita e multivalore.

| **Tipo** | **Lettura intuitiva** | **Esempio** |
| :--- | :--- | :--- |
| 1:1 | Al massimo uno da entrambe le parti. | Dirige(Professore, Dipartimento), se ogni professore dirige al massimo un dipartimento e viceversa |
| 1:N | Un elemento del primo lato può collegarsi a molti del secondo; ciascuno del secondo al massimo a uno del primo. | Insegna(Professore, Corso), in un ipotetico sistema con un solo docente per corso |
| N:1 | Forma speculare della precedente. | SuperatoDa(Esame, Studente): molti esami possono appartenere allo stesso studente |
| N:M | Molti da entrambe le parti. | Frequenta(Studente, Corso) |

Attenzione all orientamento: quando leggi 1:N devi sempre chiederti quale lato e quello che può avere molti collegamenti. Non memorizzare il simbolo senza una frase in italiano.

> [!question] CHECK - Uno studente può frequentare molti corsi e un corso può essere frequentato da molti studenti. Che associazione e?
> > [!success]- Soluzione *(clicca per mostrare)*
> > N:M (molti-a-molti).

## 12. Totalità e parzialità

La totalità riguarda il minimo numero di collegamenti richiesto. Un'associazione è totale su X se ogni elemento di X deve partecipare ad almeno un legame; è parziale se esistono elementi di X che possono non partecipare.

```text
INSEGNA(Professore, Corso)
- totale su CORSO se ogni corso deve avere almeno un docente
- può essere parziale su PROFESSORE se esistono professori che in quel periodo non insegnano
```

Per capire la totalità poniti sempre la domanda: "Può esistere un X senza nessun Y collegato?" Se la risposta e no, la partecipazione è totale su X.

> [!question] CHECK - In Residenza(Persona, Città), se ogni persona deve risultare residente in una città, Residenza è totale su Persona?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Si. Ogni Persona deve avere almeno una Città collegata.

## 13. Associazioni con proprietà e associazioni ricorsive

Le associazioni possono avere proprie proprietà. E utile quando il dato descrive il legame e non una delle due entità prese da sole.

```text
IMPIEGATO -- Afferenza -- DIPARTIMENTO
                         |
                       Data

La Data indica da quando quell impiegato afferisce a quel dipartimento: è una proprietà del legame.
```

Un'associazione può anche essere ricorsiva (o riflessiva), cioe collegare elementi della stessa collezione.

```text
PERSONA -- MadreDi -- PERSONA
PERSONA -- Conosce -- PERSONA
```

> [!tip] DA RICORDARE - Ruoli
> Nelle associazioni ricorsive spesso e necessario distinguere i ruoli: ad esempio Madre / Figlio oppure Successore / Predecessore.

## 14. Esempio guida: biblioteca e thesaurus

Il PDF usa una biblioteca universitaria per mostrare come dalla descrizione del dominio si individuano concetti e relazioni. Fra gli elementi rilevanti troviamo descrizioni bibliografiche, copie fisiche, autori, utenti, prestiti e termini di un thesaurus.

- DESCRIZIONE_BIBLIOGRAFICA: rappresenta l opera descritta.
- COPIA: rappresenta il documento fisico realmente disponibile.
- AUTORE: può essere collegato a molte descrizioni bibliografiche.
- UTENTE: può avere prestiti in corso.
- TERMINE: parola chiave del thesaurus.
- Fra i termini possono esistere legami di preferenza e gerarchia (più generale / più specifico).

> [!tip] DA RICORDARE - Perché e utile questo esempio
> Mostra che progettare significa decidere quali concetti meritano di essere entità autonome e quali legami devono essere espliciti. Non basta trascrivere le frasi del requisito.

## 15. Gerarchie di classi: generalizzazione e specializzazione

Spesso alcune classi sono casi particolari di una classe più generale. La classe più specifica e una sottoclasse; quella più generale e una superclasse.

```text
                STUDENTE
                   |
       ---------------------------
       |            |            |
   Matricola     Laureando    Fuori corso
   (esempio di possibili specializzazioni)
```

Le due idee chiave sono:

- gli elementi della sottoclasse sono anche elementi della superclasse;
- la sottoclasse eredita le proprietà della superclasse.

Esempio: se PERSONA ha Nome e DataNascita, e STUDENTE e sottoclasse di PERSONA, allora uno Studente possiede implicitamente anche Nome e DataNascita, oltre alle proprietà specifiche come Matricola.

## 16. Sottotipi, sostitutivita ed ereditarietà

Fra i tipi oggetto il PDF introduce una relazione di sottotipo. Se T e sottotipo di T', gli elementi di T possono essere usati dove sono ammessi valori di T'. Questa idea e chiamata sostitutivita.

L'ereditarietà permette di definire un tipo a partire da un altro. Nell'ereditarietà stretta gli attributi vengono aggiunti o specializzati, senza contraddire la struttura del supertipo.

> [!tip] DA RICORDARE - Esempio
> PERSONA ha Nome. STUDENTE e un sottotipo di PERSONA e aggiunge Matricola. Ogni Studente può essere trattato come Persona, perché possiede le proprietà richieste da Persona.

## 17. Disgiunzione, copertura e partizione

| **Vincolo** | **Significato** | **Esempio** |
| :--- | :--- | :--- |
| Disgiunzione | Un elemento non può appartenere contemporaneamente a due sottoclassi dell'insieme considerato. | Se dividessimo Persona in Minorenne e Maggiorenne sulla stessa data, le classi sarebbero disgiunte. |
| Copertura | Ogni elemento della superclasse appartiene ad almeno una delle sottoclassi. | Ogni Persona e Minorenne oppure Maggiorenne. |
| Partizione | Disgiunzione + copertura. | Ogni Persona appartiene a una e una sola fra Minorenne e Maggiorenne. |

Una gerarchia può anche essere multipla: un oggetto può appartenere a più classificazioni indipendenti, se il dominio lo consente.

## 18. Conoscenza astratta e vincoli di integrita

La conoscenza astratta descrive la struttura generale del dominio e le restrizioni sui valori possibili. I vincoli di integrita possono essere statici o dinamici.

| **Tipo di vincolo** | **Idea** | **Esempio** |
| :--- | :--- | :--- |
| Statico | Deve essere vero in ogni singolo stato valido del database. | Una matricola identifica un solo studente. |
| Dinamico | Vincola il modo in cui i dati possono cambiare nel tempo. | Uno stato di prestito può passare da attivo a restituito secondo determinate regole. |

La conoscenza astratta può includere anche regole per derivare nuovi fatti da fatti già noti.

## 19. Dalla raccolta dei requisiti alla base di dati

La costruzione di una base di dati passa per analisi dei requisiti, progettazione concettuale, progettazione logica, progettazione fisica, progettazione delle applicazioni e realizzazione.

| **Fase** | **Output** |
| :--- | :--- |
| Analisi dei requisiti | Specifica dei requisiti e schemi di settore |
| Progettazione concettuale | Schema concettuale |
| Progettazione logica | Schema logico |
| Progettazione fisica | Schema fisico |

### 19.1 Come si analizzano i requisiti

**1.** Analizza il sistema esistente e raccogli requisiti informali.
**2.** Elimina ambiguità, imprecisioni e termini usati in modo non uniforme.
**3.** Raggruppa le frasi per dati, vincoli e operazioni.
**4.** Costruisci un glossario dei termini importanti.
**5.** Disegna gli schemi di settore.
**6.** Specifica le operazioni previste.
**7.** Verifica che operazioni e dati siano coerenti fra loro.

> [!warning] ATTENZIONE - Errore tipico
> Saltare direttamente alle tabelle. Prima devi capire il dominio è i vincoli: una tabella progettata troppo presto può fissare una scelta sbagliata e rendere più difficile correggere il modello.

## 20. Mini esempio completo: universita

Partiamo da un requisito informale: "Gli studenti si iscrivono a corsi. Ogni corso e tenuto da un docente. Degli studenti interessa la matricola; dei corsi il codice e il titolo."

Passo 1 - concetti:

- STUDENTE
- CORSO
- DOCENTE

Passo 2 - proprietà:

```text
STUDENTE: Matricola, Nome
CORSO: Codice, Titolo
DOCENTE: CodiceDocente, Nome
```

Passo 3 - associazioni:

```text
STUDENTE -- Frequenta -- CORSO
DOCENTE  -- Insegna  -- CORSO
```

Passo 4 - molteplicità: Frequenta e normalmente N:M; Insegna dipende dai requisiti. Se ogni corso ha un solo docente ma un docente può tenere più corsi, allora e 1:N.

> [!tip] DA RICORDARE - Perché non serve conoscere SQL qui
> Siamo ancora nella modellazione. Prima costruiamo una rappresentazione corretta del dominio; solo dopo la tradurremo in uno schema logico e, più avanti nel corso, in SQL.

## 21. Riepilogo da sapere bene

- Modellazione = trasformare il dominio del discorso in una descrizione formale.
- Modello concettuale = descrive significato e legami, indipendentemente dal DBMS.
- Modello logico = organizza i dati in una forma utilizzabile da un DBMS.
- Entità = oggetto/fatto di interesse; proprietà = caratteristiche dell'entità.
- Tipo = struttura prevista; collezione = insieme corrente di entità omogenee.
- Associazione = legame fra entità; può avere proprie proprietà e può essere ricorsiva.
- Molteplicità = 1:1, 1:N, N:1, N:M; totalità = partecipazione obbligatoria o meno.
- Gerarchia = superclasse/sottoclasse con ereditarietà; disgiunzione + copertura = partizione.
- Analisi dei requisiti = rendere requisiti completi, coerenti e non ambigui prima di progettare.

## 22. Autoverifica rapida

> [!question] CHECK - Se Autore ha nazionalita e data di nascita e compare in molti libri, lo tratteresti come semplice proprietà di Libro o come entità?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Come entità autonoma e più naturale, perché ha proprie proprietà e partecipa a molti legami.

> [!question] CHECK - Frequenta(Studente, Corso) con molti studenti per corso e molti corsi per studente: che molteplicità ha?
> > [!success]- Soluzione *(clicca per mostrare)*
> > N:M.

> [!question] CHECK - Se ogni corso deve avere almeno un docente, Insegna è totale su Corso?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Si.

> [!question] CHECK - Disgiunzione + copertura cosa produce?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Una partizione della superclasse.

> [!question] CHECK - Qual e l'ordine corretto fra concettuale, logico e fisico?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Prima schema concettuale, poi schema logico, poi schema fisico.

