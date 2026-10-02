# 04 - Esempio di progettazione concettuale

Appunti spiegati dal quarto PDF. Caso di studio completo (società di formazione): dall'analisi dei requisiti in linguaggio naturale alla costruzione, raffinamento e validazione dello schema E-R finale.

> [!abstract] Panoramica
> **Livello: da zero | Prerequisiti: modello E-R base (file 02 e 03) | Focus: analisi requisiti, glossario, criteri di scelta (entità/attributo/associazione/gerarchia), strategie di progettazione, schema scheletro, raffinamento modulare, integrazione, qualità del modello E-R**

## 1. Dal testo naturale allo schema concettuale: il punto di partenza

La progettazione concettuale non parte quasi mai da specifiche già formalizzate o pulite. Nella realtà operativa, il progettista riceve interviste, documenti aziendali o descrizioni testuali in linguaggio naturale.

L'obiettivo di questo caso di studio è mostrare **come si passa metodologicamente da un testo narrativo a uno schema E-R formale e corretto**, evitando salti intuitivi rischiosi.

Il dominio analizzato è quello di una **società di formazione** che eroga corsi di aggiornamento professionale per dipendenti di aziende e liberi professionisti.

| **Fase** | **Attività principale** | **Output prodotto** |
| :--- | :--- | :--- |
| 1. Normalizzazione linguistica | Eliminazione sinonimi, omonimie e ambiguità | Glossario dei termini |
| 2. Riorganizzazione requisiti | Decomposizione in blocchi tematici coerenti | Specifiche strutturate per concetto |
| 3. Individuazione concetti cardine | Selezione delle macro-entità centrali | Schema scheletro |
| 4. Raffinamento dei componenti | Espansione analitica di entità, attributi e gerarchie | Sottoschemi dettagliati |
| 5. Integrazione e risoluzione | Collegamento dei sottoschemi e specializzazione dei legami | Schema concettuale E-R completo |
| 6. Controllo di qualità | Verifica formale (correttezza, completezza, leggibilità, minimalità) | Schema pronto per la progettazione logica |

```text
TESTO NATURALE (Requisiti grezzi)
   │
   ▼
GLOSSARIO DEI TERMINI (Vocabolario univoco)
   │
   ▼
SCHEMA SCHELETRO (Macro-architettura)
   │
   ▼
RAFFINAMENTO & INTEGRAZIONE (Dettaglio e legami temporali)
   │
   ▼
SCHEMA E-R VALIDATO
```

> [!tip] DA RICORDARE - Regola d'oro
> Non si disegna mai direttamente lo schema completo partendo dal testo. Prima si ripulisce e si organizza il linguaggio; poi si costruisce lo scheletro; infine si raffina e si integra per passi successivi.

## 2. Analisi e organizzazione dei requisiti

Il testo grezzo fornito dal committente presenta i classici difetti del linguaggio naturale:
- **Sinonimi**: parole diverse usate per indicare lo stesso identico concetto (es. *studente*, *partecipante*, *corsista*).
- **Omonimie**: la stessa parola usata per concetti distinti (es. *corso* per indicare la materia oppure una specifica sessione in calendario).
- **Informazioni eterogenee frammiste**: descrizioni di corsi, orari, anagrafiche e rapporti di lavoro intrecciate nello stesso paragrafo.

### 2.1 Trattamento dei sinonimi e normalizzazione dei termini

Il primo passo consiste nell'individuare i sinonimi e adottare un termine unico standard per ciascun concetto:

```text
Studente / Corsista  -> PARTECIPANTE
Insegnante           -> DOCENTE
Seminario            -> CORSO
Società / Azienda    -> DATORE DI LAVORO
```

In questo modo si evita il grave errore di creare entità distinte per termini che descrivono la stessa classe di oggetti.

### 2.2 Il glossario dei termini

Una volta fissato il vocabolario, si redige il **glossario**. È una tabella che definisce formalmente ogni termine ammesso nel progetto, chiarendo significato, sinonimi esclusi e relazioni con gli altri concetti.

| **Termine** | **Significato nel contesto** | **Sinonimi da evitare** | **Collegamenti diretti** |
| :--- | :--- | :--- | :--- |
| Partecipante | Persona fisica che segue o ha seguito corsi formativi. | Studente, Corsista | Corso, Edizione, Datore |
| Docente | Professionista o dipendente abilitato all'insegnamento nei corsi. | Insegnante, Formatore | Corso, Edizione |
| Corso | Offerta formativa astratta (catalogo formativo). | Seminario, Materia | Edizione, Docente |
| Edizione | Singola erogazione temporale e logistica di un corso. | Sessione, Classe | Corso, Lezione, Partecipante, Docente |
| Lezione | Singolo incontro didattico di una specifica edizione. | Incontro, Seduta | Edizione |
| Datore | Ente o azienda presso cui lavora o ha lavorato un partecipante. | Società, Impresa, Studio | Partecipante |

> [!tip] DA RICORDARE - Ruolo del glossario
> Il glossario non è ancora lo schema E-R. È il contratto linguistico che garantisce che sviluppatori, analisti e committenti usino ogni termine con un unico significato condiviso.

### 2.3 Decomposizione del testo per macro-concetti

Dopo la normalizzazione lessicale, il testo dei requisiti viene scomposto in frasi atomiche raggruppate per area tematica:
1. **Frasi relative ai Partecipanti**: anagrafica, titolo di studio, condizione occupazionale, carriera passata e presente.
2. **Frasi relative ai Datori di lavoro**: denominazione aziendale, sede legale, recapiti, contratti in essere.
3. **Frasi relative all'Offerta formativa (Corsi ed Edizioni)**: codice corso, titolo, edizioni pianificate, calendario delle lezioni e aule.
4. **Frasi relative al Corpo Docente**: anagrafica, recapiti telefonici, tipologia contrattuale (interno o collaboratore esterno), competenze e incarichi.

## 3. Criteri guida: come scegliere il costrutto E-R corretto

Durante l'analisi dei singoli requisiti, per ogni elemento del discorso occorre determinare quale costrutto del modello E-R sia più idoneo.

| **Costrutto** | **Criterio decisionale** | **Domanda diagnostica** | **Esempio pratico** |
| :--- | :--- | :--- | :--- |
| **Entità** | Il concetto ha proprietà proprie ed esistenza indipendente nel dominio. | Ha senso memorizzare più informazioni descrittive su questo elemento? | `PARTECIPANTE`, `DATORE`, `CORSO` |
| **Attributo** | Proprietà semplice priva di vita propria, riferita a una specifica entità o associazione. | Descrive solo una caratteristica elementare di qualcos'altro? | `Cognome`, `CodiceFiscale`, `DataNascita` |
| **Associazione** | Fatto logico che connette due o più entità distinte. | È una relazione/interazione tra oggetti autonomi? | `Impiego` (collega Partecipante e Datore) |
| **Generalizzazione** | Concetto che costituisce una declinazione specifica di un'entità più generale (sottotipo). | "È un tipo speciale di..." con proprietà sue esclusive? | `DIPENDENTE` e `PROFESSIONISTA` per Partecipante |

```text
                  CANDIDATO AL MODELLO
                           │
       Ha proprietà proprie ed esistenza autonoma?
              ┌────────────┴────────────┐
              SÌ                        NO
              │                         │
         È un'ENTITÀ            È una caratteristica
                                di un'altra cosa?
                                  ┌─────┴─────┐
                                  SÌ          NO
                                  │           │
                              ATTRIBUTO   ASSOCIAZIONE
```

> [!tip] DA RICORDARE - Evitare i due estremi opposti
> - **Non trasformare tutto in attributo**: se il datore di lavoro avesse solo il nome, potrebbe sembrare un attributo di Partecipante. Ma volendo memorizzare indirizzo e telefono, `Datore` deve diventare un'entità autonoma.
> - **Non trasformare tutto in entità**: attributi atomici come `Cognome`, `Età` o `Codice` non devono mai diventare entità isolate.

## 4. Modellazione dell'area Partecipanti e Datori di Lavoro

L'analisi dei requisiti sui partecipanti evidenzia sia informazioni anagrafiche, sia la complessa storia lavorativa degli iscritti.

### 4.1 Entità Partecipante e Datore

L'entità `PARTECIPANTE` raccoglie gli attributi anagrafici comuni:
- `Codice` (identificatore univoco)
- `CodiceFiscale`
- `Cognome`
- `Età`
- `Sesso`
- `CittàNascita`

L'entità `DATORE` rappresenta l'azienda o studio professionale:
- `CodiceDatore` (o denominazione univoca)
- `Nome`
- `Indirizzo`
- `Telefono`

### 4.2 Associazioni temporali: Impiego Corrente vs Impiego Passato

Un partecipante può lavorare attualmente presso un'azienda, ma può anche aver avuto precedenti rapporti di lavoro.

```text
PARTECIPANTE ──── Impiego Corrente ──── DATORE
                      |
                  DataInizio

PARTECIPANTE ──── Impiego Passato  ──── DATORE
                      |   |
             DataInizio   DataFine
```

Perché `DataInizio` e `DataFine` non sono attributi dell'entità `PARTECIPANTE`?
- Un partecipante non ha "una data di inizio" in astratto: ha una data di inizio **per quello specifico rapporto di lavoro con quella determinata azienda**.
- Una persona può aver lavorato per Azienda A (2018-2021), poi per Azienda B (2021-2024), e lavorare ora per Azienda C (dal 2024).
- Collocare le date sull'associazione modella correttamente il fatto che il vincolo temporale appartiene al legame, non alla persona singola né all'azienda.

### 4.3 Analisi delle cardinalità di impiego

| **Associazione** | **Lato Partecipante** | **Lato Datore** | **Spiegazione delle molteplicità** |
| :--- | :--- | :--- | :--- |
| **Impiego Corrente** | `(0,1)` | `(0,N)` | Un partecipante può non lavorare (0) o lavorare al massimo per 1 datore corrente (1). Un datore può avere 0 oppure molti dipendenti iscritti (N). |
| **Impiego Passato** | `(0,N)` | `(0,N)` | Un partecipante può aver avuto da 0 a molti datori passati. Un datore può comparire come ex datore di 0 o molti partecipanti. |

```text
               (0,1)                     (0,N)
PARTECIPANTE ─────────── [ImpiegoCorrente] ─────────── DATORE
PARTECIPANTE ─────────── [ImpiegoPassato]  ─────────── DATORE
               (0,N)                     (0,N)
```

### 4.4 Specializzazione dei partecipanti: Dipendente e Professionista

I requisiti stabiliscono che un partecipante possa essere un lavoratore dipendente oppure un libero professionista, con attributi dedicati:
- **Dipendente**: possiede `Livello` contrattuale e `Posizione` lavorativa.
- **Professionista**: possiede `Area` di competenza e `TitoloProfessionale`.

Questa situazione richiede una **generalizzazione**:

```text
                    PARTECIPANTE
             (Codice, CF, Cognome, Età, ...)
                         |
                       (t, e)
                     ┌───┴───┐
                     │       │
                DIPENDENTE   PROFESSIONISTA
                - Livello    - Area
                - Posizione  - TitoloProfessionale
```

> [!tip] DA RICORDARE - Ereditarietà concettuale
> I sottotipi `DIPENDENTE` e `PROFESSIONISTA` ereditano automaticamente tutti gli attributi del padre (`Codice`, `CF`, `Cognome`, ecc.) e partecipano alle associazioni definite su `PARTECIPANTE` (come gli impieghi).

> [!question] CHECK - Se un partecipante avesse potuto essere contemporaneamente dipendente part-time e libero professionista, come sarebbe cambiata la generalizzazione?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Sarebbe diventata sovrapposta (s) anziché esclusiva (e), mantenendo la copertura totale (t) se ogni partecipante appartiene ad almeno una categoria.

## 5. Modellazione dell'area Corsi, Edizioni e Lezioni

La modellazione della didattica richiede una distinzione concettuale decisiva tra corso astratto ed evento formativo concreto.

### 5.1 Distinzione concettuale: Corso vs Edizione

- **CORSO**: è il tipo astratto presente nel catalogo formativo (es. *Basi di Dati e Sistemi Informativi*, Codice `BD01`). Esiste indipendentemente dal fatto che sia attualmente erogato.
- **EDIZIONE**: è la realizzazione concreta, temporalizzata, con un proprio calendario e una specifica aula (es. *Edizione 1 - Autunno 2026* del corso `BD01`).

```text
CORSO:        [ Codice: "BD01" | Titolo: "Basi di Dati" ]
                  │
                  ├── Edizione 1: [ DataInizio: 01/10/2026 | DataFine: 15/11/2026 | N.Iscritti: 25 ]
                  ├── Edizione 2: [ DataInizio: 01/02/2027 | DataFine: 20/03/2027 | N.Iscritti: 30 ]
                  └── Edizione 3: [ DataInizio: 05/05/2027 | DataFine: 25/06/2027 | N.Iscritti: 18 ]
```

### 5.2 Associazione Tipologia e cardinalità

Il legame tra Corso ed Edizione è modellato dall'associazione `Tipologia`:

```text
          (0,N)                   (1,1)
CORSO ────────────── [Tipologia] ────────────── EDIZIONE
```

- Lato `CORSO` `(0,N)`: un corso a catalogo può non essere ancora mai stato erogato (0) oppure può avere decine di edizioni nel corso degli anni (N).
- Lato `EDIZIONE` `(1,1)`: ogni singola edizione è l'erogazione di **uno e un solo** corso. Un'edizione non può esistere senza corso, né può appartenere contemporaneamente a due corsi diversi.

### 5.3 Composizione in Lezioni

A sua volta, un'edizione non si risolve in un blocco monolitico: è articolata in una serie di incontri formativi (`LEZIONE`), ciascuno caratterizzato da `Giorno`, `Orario` e `Aula`.

```text
            (1,N)                      (1,1)
EDIZIONE ─────────── [Composizione] ─────────── LEZIONE
```

- Lato `EDIZIONE` `(1,1)` a `(1,N)`: ogni edizione deve avere almeno una lezione (1) e generalmente ne comprende molte (N).
- Lato `LEZIONE` `(1,1)`: ogni singola lezione appartiene strettamente a una sola edizione.

> [!tip] DA RICORDARE - Gerarchia di contenimento didattico
> `CORSO` (concetto generale) -> `EDIZIONE` (sessione con date e partecipanti) -> `LEZIONE` (singolo evento orario in aula). Confondere questi tre livelli porta a schemi scorretti o impossibili da interrogare.

## 6. Modellazione dell'area Docenti

L'area dei docenti presenta due particolarità didattiche importanti: un attributo multivalore e una gerarchia di inquadramento.

### 6.1 Entità Docente e attributi multivalore

L'entità `DOCENTE` descrive chi impartisce le lezioni:
- `CodiceFiscale` (identificatore)
- `Cognome`
- `Età`
- `CittàNascita`
- `Telefono`: i requisiti specificano che per ogni docente occorre memorizzare **tutti i recapiti telefonici disponibili** (cellulare, ufficio, abitazione).

Nel modello E-R, un attributo con cardinalità `(1,N)` è un **attributo multivalore**:

```text
DOCENTE
├── CodiceFiscale (1,1)
├── Cognome (1,1)
├── Età (1,1)
├── CittàNascita (1,1)
└── Telefono (1,N)  <-- Multi-valore: obbligatorio almeno 1, ammessi molti
```

### 6.2 Gerarchia dei Docenti: Interni e Collaboratori

I docenti si distinguono in due categorie contrattuali:
- **Interno**: personale dipendente della società di formazione.
- **Collaboratore**: professionista esterno a contratto.

```text
                      DOCENTE
          (CF, Cognome, Età, Telefono...)
                         │
                       (t, e)
                     ┌───┴───┐
                     │       │
                  INTERNO   COLLABORATORE
```

> [!question] CHECK - Perché il telefono con cardinalità (1,N) non è stato trasformato in una nuova entità autonoma?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Perché il numero di telefono è una semplice stringa priva di ulteriori attributi o relazioni autonome. Il costrutto di attributo multivalore (1,N) è fatto esattamente per rappresentare collezioni omogenee di valori elementari legate a un'entità.

## 7. Strategie di progettazione concettuale

Costruire uno schema E-R non significa solo sapere quali simboli usare, ma adottare una strategia ordinata per comporre i pezzi.

| **Strategia** | **Come procede** | **Vantaggi** | **Rischi** |
| :--- | :--- | :--- | :--- |
| **Top-Down** | Dal generale al particolare: si parte da un unico macro-concetto e si decompone progressivamente. | Mantiene sempre la visione d'insieme globale. | Può risultare astratta e trascurare dettagli operativi critici. |
| **Bottom-Up** | Dai dettagli al generale: si parte da tutti gli attributi elementari e si aggregano in entità e relazioni. | Molto analitica, non dimentica nessun dato iniziale. | Rischia di perdere la visione architetturale e creare ridondanze. |
| **Mista** *(standard)* | Si individuano i concetti cardine (schema scheletro), poi si raffinano i sottoschemi e infine si integrano. | Combina controllo architetturale e precisione nei dettagli. | Richiede una fase esplicita di integrazione dei legami tra aree. |

```text
TOP-DOWN:     SISTEMA ──> Macro-Aree ──> Entità ──> Attributi
BOTTOM-UP:    Attributi singoli ──> Raggruppamenti ──> Entità ──> Schema
MISTA:        Concetti Cardine (Scheletro) ──> Raffinamento Sottoschemi ──> Integrazione
```

## 8. Lo schema scheletro

La strategia mista inizia definendo lo **schema scheletro**. Si tratta di una visione ad altissimo livello che isola solo le tre o quattro entità fondamentali del business e i legami primari che le uniscono.

Nel nostro caso di studio, i pilastri dell'attività formativa sono:
1. I partecipanti
2. I corsi
3. I docenti

```text
               CORSO
              /     \
             /       \
     Partecipazione   Docenza
           /           \
          /             \
   PARTECIPANTE       DOCENTE
```

> [!tip] DA RICORDARE - Funzione dello schema scheletro
> Lo schema scheletro serve a non perdersi nella complessità dei dettagli. Non include ancora cardinalità fini, attributi o gerarchie: definisce l'ancoraggio concettuale su cui innestare tutte le espansioni successive.

## 9. Raffinamento modulare dei sottoschemi

Partendo dallo scheletro, si espande ciascun nodo in modo indipendente:

```text
SOTTOSCHEMA PARTECIPANTI:
Partecipante
  ├── Attributi anagrafici
  ├── Gerarchia (Dipendente / Professionista)
  └── Legame con Datore (Impiego corrente e Impiego passato)

SOTTOSCHEMA CORSI:
Corso
  ├── Attributi (Codice, Titolo)
  ├── Tipologia -> Edizione (Date, Numero iscritti)
  └── Composizione -> Lezione (Giorno, Ora, Aula)

SOTTOSCHEMA DOCENTI:
Docente
  ├── Attributi (CF, Cognome, Telefono multivalore)
  └── Gerarchia (Interno / Collaboratore)
```

## 10. Integrazione e risoluzione delle associazioni

La fase più delicata della strategia mista è l'**integrazione**. I legami preliminari dello schema scheletro (`Partecipazione` e `Docenza`) erano stati disegnati genericamente tra le entità principali (`Partecipante -- Corso` e `Docente -- Corso`).

Ora che abbiamo raffinato `Corso` in `Corso -> Edizione -> Lezione`, dobbiamo chiederci: **a cosa si collegano esattamente la partecipazione e la docenza?**

### 10.1 Risoluzione dell'associazione Partecipazione

Dire che "Marco partecipa a Basi di Dati" non basta. Marco non partecipa al corso a catalogo astratto: partecipa a una **specifica edizione** temporale.

Inoltre, il testo richiede di distinguere la frequenza attuale da quella conclusa con voto:
1. **Partecipazione Corrente**: unisce `PARTECIPANTE` a `EDIZIONE`. Indica che il corsista sta attualmente seguendo le lezioni di quell'edizione.
2. **Partecipazione Passata**: unisce `PARTECIPANTE` a `EDIZIONE`. Include l'attributo `ValutazioneFinale`, perché il giudizio di merito si ottiene solo al termine dell'edizione frequentata.

```text
PARTECIPANTE ──── [PartecipazioneCorrente] ──── EDIZIONE
PARTECIPANTE ──── [PartecipazionePassata]  ──── EDIZIONE
                         |
                 ValutazioneFinale
```

### 10.2 Risoluzione di Docenza: tre semantiche differenti

Il legame preliminare `Docente -- Docenza -- Corso` nasconde in realtà tre informazioni concettualmente distinte che non devono essere confuse:

| **Associazione** | **Entità collegate** | **Cosa rappresenta nella realtà** | **Esempio pratico** |
| :--- | :--- | :--- | :--- |
| **Abilitazione** | `DOCENTE` e `CORSO` | Competenza/idoneità: quali materie il docente ha il titolo per insegnare. | Il Prof. Rossi è abilitato a insegnare *Basi di Dati* e *Reti*. |
| **Docenza Corrente** | `DOCENTE` e `EDIZIONE` | Incarico attivo: quale specifica sessione in aula il docente sta tenendo ora. | Il Prof. Rossi insegna l'edizione di Ottobre 2026 di *Basi di Dati*. |
| **Docenza Passata** | `DOCENTE` e `EDIZIONE` | Storico dell'attività didattica: quali sessioni il docente ha terminato in passato. | Il Prof. Rossi ha insegnato l'edizione di Maggio 2025 di *Basi di Dati*. |

```text
DOCENTE ────────────── [Abilitazione] ────────────── CORSO
   │                                                   │
   │                                                   │ (Tipologia)
   │                                                   ▼
   ├────────────── [DocenzaCorrente] ─────────────> EDIZIONE
   │                                                   ▲
   └────────────── [DocenzaPassata]  ──────────────────┘
```

> [!tip] DA RICORDARE - Distinzione semantica
> - "Poter insegnare una materia" è un legame con il `CORSO` (titolo/qualifica).
> - "Stare insegnando un corso adesso" o "averlo insegnato l'anno scorso" è un legame con l'`EDIZIONE` (sessione operativa).

## 11. Lettura dello schema E-R complessivo

Lo schema finale completo può sembrare imponente a prima vista, ma è perfettamente governabile leggendolo come l'intersezione ordinata di quattro macro-aree:

```text
[ AREA PARTECIPANTI ]                     [ AREA OFFERTA FORMATIVA ]
┌─────────────────────────┐               ┌─────────────────────────┐
│      PARTECIPANTE       │               │          CORSO          │
│ (Codice, CF, Cognome..) │               │    (Codice, Titolo)     │
│       /          \      │               └────────────┬────────────┘
│  DIPENDENTE   PROFESS.  │                            │ (0,N)
└───────┬────────────┬────┘                            │ Tipologia
        │ (0,1)      │ (0,N)                           │ (1,1)
        │ Corrente   │ Passato                         ▼
        ▼            ▼                    ┌─────────────────────────┐
┌─────────────────────────┐               │        EDIZIONE         │
│         DATORE          │               │  (DataIniz, DataFine..) │
│ (Nome, Indirizzo, Tel)  │               └───────┬──────────┬──────┘
└─────────────────────────┘                       │ (1,N)    │ (1,1)
                                                  │          │ Composizione
       ┌──────────────────────────────────────────┘          │ (1,1)
       │ (Partecipaz. Corrente / Passata)                    ▼
       ▼                                          ┌─────────────────────────┐
[ COLLEGAMENTI TRASVERSALI ]                      │         LEZIONE         │
PARTECIPANTE <==> EDIZIONE                        │   (Giorno, Ora, Aula)   │
DOCENTE      <==> CORSO    (Abilitazione)         └─────────────────────────┘
DOCENTE      <==> EDIZIONE (Docenza C./P.)
       ▲
       │
[ AREA CORPO DOCENTE ]
┌─────────────────────────┐
│         DOCENTE         │
│ (CF, Cognome, Tel(1,N)) │
│       /         \       │
│   INTERNO    COLLABOR.  │
└─────────────────────────┘
```

## 12. Criteri di qualità dello schema concettuale

Uno schema concettuale è ben fatto non quando è "grande", ma quando rispetta quattro criteri formali di qualità:

| **Criterio** | **Definizione formale** | **Tipico errore da evitare** |
| :--- | :--- | :--- |
| **Correttezza** | Rispetto rigoroso della sintassi e della semantica del modello E-R. | Associare direttamente due associazioni tra loro, o usare cardinalità min > max. |
| **Completezza** | Rappresentazione fedele di tutti i requisiti informativi e vincoli espressi. | Dimenticare un attributo esplicito (es. recapiti telefonici) o un vincolo di partecipazione. |
| **Leggibilità** | Chiarezza estetica e semantica: layout ordinato, nomi parlanti, no incroci inutili. | Denominare le entità `E1`, `E2` o le associazioni `R1`, `R2`, rendendo il diagramma illeggibile. |
| **Minimalità** | Assenza di ridondanze non motivate e duplicazioni concettuali. | Rappresentare contemporaneamente `DataNascita` ed `Età`, o memorizzare la stessa informazione su due entità collegate. |

## 13. Metodo pratico per affrontare gli esercizi d'esame

Negli appelli d'esame e nelle prove scritte viene tipicamente assegnato un testo descrittivo da convertire in diagramma E-R. La sequenza operativa da seguire è la seguente:

```text
PASSO 1: LETTURA E NORMALIZZAZIONE
  └─ Leggere il testo sottolineando sostantivi e verbi
  └─ Eliminare sinonimi e compilare il Glossario dei termini

PASSO 2: SCHEMA SCHELETRO
  └─ Isolare le 2-4 entità principali e tracciare le associazioni grezze
  └─ Verificare che la struttura scheletrica risponda all'obiettivo primario del dominio

PASSO 3: RAFFINAMENTO MODULARE
  └─ Per ogni entità: decidere attributi atomici, multivalore e identificatori
  └─ Individuare eventuali generalizzazioni (valutare copertura ed esclusività)
  └─ Esplicitare le entità temporali o logistiche intermedie (es. Edizioni, Sessioni)

PASSO 4: RISOLUZIONE E CARDINALITÀ
  └─ Collegare i sottoschemi specificando le associazioni corrette
  └─ Assegnare cardinalità min/max (0,1), (1,1), (0,N), (1,N) su ciascun ramo
  └─ Collocare eventuali attributi sulle associazioni (es. date di inizio/fine, esiti)

PASSO 5: CHECK DI QUALITÀ FINALE
  └─ Rileggere ogni frase del testo di partenza e verificare che trovi riscontro nel diagramma
  └─ Ispezionare correttezza, completezza, leggibilità e minimalità
```

## 14. Esempio applicativo guidato: Sistema di Gestione Palestra

Applichiamo il medesimo metodo su un caso analogo per consolidare il processo mentale.

> **Testo dei requisiti:**
> *Una catena di palestre vuole gestire clienti, istruttori e corsi fitness. Ogni cliente può iscriversi a più corsi. Ogni corso è articolato in lezioni settimanali. Ciascun istruttore possiede specifiche abilitazioni per insegnare determinati corsi, e viene incaricato di tenere le sessioni programmate nei diversi mesi.*

### Sviluppo analitico guidato

1. **Glossario & Scheletro iniziale**:
   Concetti centrali: `CLIENTE`, `ISTRUTTORE`, `CORSO`.

```text
CLIENTE ──── [Iscrizione] ──── CORSO ──── [Insegnamento] ──── ISTRUTTORE
```

2. **Raffinamento: Corso astratto vs Edizione attiva**:
   Non ci si iscrive al "Pilates" in astratto, ma al "Corso Pilates di Ottobre - Corso A".
   - `CORSO` (Codice, NomeCorso)
   - `EDIZIONE_CORSO` (Mese, Anno, Quota)
   - `LEZIONE` (GiornoSettimana, OraInizio, Sala)

3. **Integrazione dei legami**:
   - `CLIENTE` si iscrive a `EDIZIONE_CORSO` con data di iscrizione e stato pagamento.
   - `ISTRUTTORE` è **abilitato** a un `CORSO` generale (possiede il brevetto per insegnare Pilates).
   - `ISTRUTTORE` è **assegnato** a una specifica `EDIZIONE_CORSO` (ha il turno attivo per le lezioni di quel mese).

```text
CLIENTE ────────────── [Iscrizione] ────────────── EDIZIONE_CORSO
                                                         │
ISTRUTTORE ─────────── [Abilitazione] ──────────> CORSO   │
    │                                              │      │ (Tipologia)
    │                                              └──────┤
    └───────────────── [Assegnazione] ────────────────────┘
```

## 15. Errori tipici da evitare

- **Trasformare in attributo un concetto con vita propria**: inserire `DatoreDiLavoro` come semplice testo in `PARTECIPANTE` impedisce di registrare l'indirizzo aziendale e i contatti della società.
- **Creare entità per attributi atomici**: definire un'entità autonoma `COGNOME` o `ETÀ` appesantisce lo schema senza alcun beneficio semantico.
- **Mettere sull'entità attributi che descrivono l'associazione**: memorizzare `DataInizioLavoro` o `ValutazioneFinale` dentro `PARTECIPANTE` rende impossibile gestire carriere lavorative multiple o più edizioni frequentate nel tempo.
- **Confondere il tipo con l'istanza temporale (Corso vs Edizione)**: dimenticare l'entità intermedia `EDIZIONE` costringe a duplicare le informazioni del corso per ogni sessione o impedisce di tracciare lezioni e calendari distinti.
- **Confondere l'abilitazione (qualifica) con l'insegnamento (fatto operativo)**: legare il docente solo al corso non permette di sapere chi ha tenuto quale classe; legarlo solo all'edizione fa perdere la traccia delle sue qualifiche e competenze formali.
- **Omettere o invertire le cardinalità**: indicare cardinalità senza verificare se il vincolo minimo sia 0 (opzionale) o 1 (obbligatorio) e se il vincolo massimo sia 1 (funzionale) o N (molti).

## 16. Riepilogo da sapere bene

- La progettazione concettuale è un'attività di astrazione metodologica che traduce requisiti informali in un modello rigoroso.
- Il glossario è lo strumento preventivo essenziale per sanare sinonimi, omonimie e ambiguità terminologiche prima di disegnare.
- Un concetto diventa **entità** se ha esistenza autonoma e proprietà descrittive proprie; diventa **attributo** se è una caratteristica atomica; diventa **associazione** se correla entità indipendenti.
- Le proprietà temporali di un legame (come date di inizio/fine incarico) devono risiedere sull'**associazione**, non sulle entità partecipanti.
- La distinzione tra **catalogo astratto** (`CORSO`) ed **evento temporale** (`EDIZIONE`) è un design pattern ricorrente nei modelli concettuali gestionali.
- L'attributo **multivalore** `(1,N)` modella un elenco di valori atomici omogenei (es. numeri di telefono) senza richiedere entità artificiose.
- La **strategia mista** è l'approccio standard: definisce prima lo schema scheletro, sviluppa i sottoschemi in parallelo e conclude con l'integrazione delle relazioni orizzontali.
- I quattro pilastri di qualità dello schema E-R sono **correttezza**, **completezza**, **leggibilità** e **minimalità**.

## 17. Mappa concettuale finale

```text
TESTO IN LINGUAGGIO NATURALE
         │
         ▼
┌─────────────────────────┐
│     NORMALIZZAZIONE     │ ──> [Glossario dei termini univoci]
└────────────┬────────────┘
             ▼
┌─────────────────────────┐
│     SCHEMA SCHELETRO    │ ──> Macro-entità: Partecipante, Corso, Docente
└────────────┬────────────┘
             ▼
┌─────────────────────────┐
│       RAFFINAMENTO      │ ──> Attributi, gerarchie (Dipendente/Prof., Int./Coll.)
│       SOTTOSCHEMI       │ ──> Scorporo Corso -> Edizione -> Lezione
└────────────┬────────────┘
             ▼
┌─────────────────────────┐
│       INTEGRAZIONE      │ ──> Partecipazione Corrente/Passata con Valutazione
│     DELLE RELAZIONI     │ ──> Abilitazione (Corso) vs Docenza C./P. (Edizione)
└────────────┬────────────┘
             ▼
┌─────────────────────────┐
│    CONTROLLO QUALITÀ    │ ──> Correttezza | Completezza | Leggibilità | Minimalità
└─────────────────────────┘
```

## 18. Autoverifica rapida

> [!question] CHECK - Perché l'attributo DataInizio nell'impiego di un partecipante deve stare sull'associazione Impiego e non sull'entità Partecipante?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Perché una persona può avere una storia lavorativa composta da più datori di lavoro nel tempo. La data descrive l'inizio di quello specifico rapporto con quella determinata azienda, non una caratteristica intrinseca del partecipante.

> [!question] CHECK - Che differenza intercorre tra l'entità Corso e l'entità Edizione?
> > [!success]- Soluzione *(clicca per mostrare)*
> > `Corso` rappresenta il concetto astratto a catalogo (es. "Basi di Dati", Codice BD01); `Edizione` rappresenta una specifica sessione erogata nel tempo con un calendario, una data di inizio/fine e un gruppo di corsisti iscritti.

> [!question] CHECK - In quali casi si ricorre a un attributo multivalore con cardinalità (1,N), come il Telefono del docente?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Quando un'entità può possedere una serie di valori atomici omogenei (uno o più numeri telefonici) che non possiedono proprietà ulteriori e non necessitano di un'entità autonoma.

> [!question] CHECK - Perché è necessario separare l'associazione Abilitazione dalle associazioni di Docenza Corrente e Docenza Passata?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Perché l'abilitazione descrive la qualifica del docente su una materia a livello di `CORSO` (potenzialità), mentre la docenza descrive l'effettivo insegnamento in aula su una specifica `EDIZIONE` (fatto operativo attuale o storico).

> [!question] CHECK - Qual è il vantaggio principale della strategia di progettazione mista rispetto a quella puramente top-down o bottom-up?
> > [!success]- Soluzione *(clicca per mostrare)*
> > Consente di fissare rapidamente l'architettura complessiva con lo schema scheletro evitando di perdersi nei dettagli, per poi raffinare e integrare analiticamente ogni sottosistema con la massima accuratezza.
