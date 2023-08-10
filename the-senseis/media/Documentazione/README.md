# MEEP: Event Planning

## Database di MEEP

### Struttura del Database

Il database è composto dalle seguenti tabelle:

- **User**: Rappresenta i dipendenti registrati o meno all'applicazione. Contiene nome, matricola, email e password e altre informazione riguardanti gli utenti.
- **Accompagnatore**: Rappresenta gli accompagnatori degli utenti partecipanti agli eventi. Contiene l'ID univoco, un nominativo e la mail dell'interessato.
- **Evento**: Rappresenta gli eventi che vengono creati dagli utenti. Ogni evento ha un ID univoco, matricola dell'organizzatore, titolo, orari e data, descrizione e altre informazioni rigurado gli eventi.
- **Tipologia**: Rappresenta il tipo di evento organizzato. Contiene informazioni come l'ID del tipo e la sua denominazione.
- **Evento_tipologia**: Tabella creata per associare ad ogni evento le sue tipologie. Contiene un ID univoco e due chiavi esterne, rispettivamente l'ID di Evento e l'ID di Tipologia.
- **Indirizzo**: Rappresenta l'indirizzo del domicilio degli utenti. Contiene la matricola, la via, il codice postale (CAP), il comune e la provincia.
- **Partecipazione**: Gestisce la partecipazione degli utenti agli eventi. Contiene un ID, un codice QR che contiene le informazioni riguardo l'evento e il partecipante, l'ID dell'evento, dell'utente e dell'accompagnatore.

## Backend di MEEP

### Struttura progetto

il progetto è suddiviso in diverse parti:

- **config**: contiene le configurazioni dell'app e la gestione del token per l'autenticazione degli utenti all'accesso.
- **controller**: contiene le funzionalità dell'applicazione, eseguendo diversi servizi. Alcuni esempi di controller sono EventoController, il quale esegue i servizi contenuti in EventoService, e UserController, che esegue i servizi di UserController ed alcuni di IndirizzoService.

  Metodi principali:
  - __createEvento()__ -> metodo che permette di creare un nuovo evento e salvare i dati sul db. Dati richiesti: titolo, descrizione, data, oraInizio, oraFine, maxPartecipanti, maxAccompagnatori
  - __deleteEvento()__ -> dato il codice dell'evento, ne esegue la cancellazione dal db
  - __updateEvento()__ -> permette di modificare i dati di un evento dato il suo codice. Dati richiesti: titolo, descrizione, data, oraInizio, oraFine, maxPartecipanti, maxAccompagnatori
  - __addTipologiaEvento()__ -> dato l'id della tipologia e il codice evento, aggiunge il record della relazione sul db. Dati richiesti: idTipologia, codiceEvento
  - __getEventi()__ -> restiuisce la lista di tutti gli eventi a cui è possibile partecipare
  - __signup()__ -> permette di completare la registrazione dell'utente (dopo aver effettuato il primo login) e invia all'utente una email di conferma. Dati richiesti: newPassword, checkPassword, username, biografia
  - __login()__ -> Permette, date le corrette credenziali, di effettuare il login e restituisce un JWT. Dati richiesti: matricola, password

- **entity**: contiene tutte le entità presenti sul database, comprese le annotazioni su chiavi primarie, auto-increment e foreign key con cardinalità. Per maggiori dettagli riguardo le entità guardare le annotazione del Database.
- **repository**: contiene diverse istruzioni e query utilizzate dai servizi per eseguire istruzioni che aggiungono, eliminano o modificano elementi nel database.
  - __findByCodiceEvento()__ -> recupera un evento dal db dato il suo codice evento. Dati richiesti: codiceEvento
  - __getNumeroAccompagnatori()__ -> dato il codice di un evento, ne resituisce il numero massimo di accompagnatori. Dati richiesti: codiceEvento
  - __getNumeroPartecipanti()__ -> dato il codice di un evento, ne resituisce il numero di partecipanti attuale. Dati richiesti: codiceEvento
  - __getEventiDisponibiliNoAdmin()__ -> restituisce una lista di eventi dispnibili (eventi non scaduti e di cui il numero di partecipanti non ha superato il massimo) non includendo gli eventi di cui l'utente è amministratore. Dati richiesti: matricola, dataOggi, oraAttuale
  - __getEventiDisponibili()__ -> restituisce una lista di eventi dispnibili (eventi non scaduti e di cui il numero di partecipanti non ha superato il massimo), gli eventi di cui l'utente è amministratore sono inclusi. Dati richiesti: dataOggi, oraAttuale
  - __getEventiDisponibili()__ -> restituisce una lista di eventi dispnibili (eventi non scaduti e di cui il numero di partecipanti non ha superato il massimo), gli eventi di cui l'utente è amministratore sono inclusi. Dati richiesti: dataOggi, oraAttuale
  - __updateFotoProfilo()__ -> data la matricola, permette di modificare la foto profilo (nome del file) salvato sul db. Dati richiesti: matricola, fotoProfilo

- **request**: contiene le richieste di dati, utili ai servizi per ricevere le informazioni e i dati necessari per la loro esecuzione.

  Attributi per classe:
  
  - __CreateEventoRequest__ -> titolo, descrizione, data, oraInizio, oraFine, maxPartecipanti, maxAccompagnatori.
  - __EditUserRequest__ -> newPassword, checkPassword, username, telefono, email, biografia="Let's Meet up!".
  - __EventoTipologiaRequest__ -> idTipologia, codiceEvento.
  - __FilterRequest__ -> idTipologia=null, dataEvento=null, oraEvento=null, maxPartecipanti=-1, includeAdmin=false.
  - __IndirizzoRequest__ -> via, cap, comune, provincia.
  - __LoginRequest__ -> matricola, password.
  - __QRCodeRequest__ -> n_accompagnatori=0, codice_evento, nomiAccompagnatori, emailAccompagnatori.
  __SignupRequest__ -> newPassword, checkPassword, username, biografia="Let's Meet up!".

- **response**: contiene le risposte ricevute dal server con le informazioni richieste e il codice di stato del server. Nello specifico, la UserResponse riceve tutti i dati dell'utente, i quali vengono passati alla classe UserService per fare la response.
- **service**: contiene tutti i servizi che l'applicazione esegue, quali la creazione e modifica degli eventi, l'aggiunta o la rimozione di tipologie agli eventi e il generatore di codici QR.
Vi sono anche i servizi profilo utente, quali la registrazione ed accesso all'applicazione, la modifica della foto profilo e dei dati dell'utente.
- **MeepApplication**: avvia l'applicazione.