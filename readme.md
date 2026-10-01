# HAK Assistant

Assistente desktop per Windows con chat AI, dettatura vocale opzionale e un insieme ristretto di operazioni locali. L'applicazione usa solo la libreria standard di Python per la GUI e le richieste HTTP; il riconoscimento vocale richiede le dipendenze opzionali in `requirements.txt`.

## Avvio

1. Installa Python 3.10 o successivo assicurandoti che Tkinter sia incluso.
2. Per usare l'AI senza un servizio cloud, installa [Ollama](https://ollama.com/), scarica un modello che supporti i tool e avvia il servizio. Per esempio:

   ```powershell
   ollama pull llama3.2
   ```

3. Avvia l'app dalla cartella del progetto:

   ```powershell
   python hak.py
   ```

4. Se necessario, apri **Impostazioni** e configura endpoint, nome del modello e chiave API:
   - **Ollama Cloud:** endpoint e modello `gemma4:31b` sono già preimpostati; inserisci la chiave API creata su [ollama.com/settings/keys](https://ollama.com/settings/keys).
   - **Ollama locale:** imposta l'endpoint `http://localhost:11434/v1/chat/completions` e avvia Ollama sul PC prima di inviare messaggi.

   La chiave viene salvata cifrata con Windows DPAPI nel file locale delle impostazioni e può essere decifrata solo dal tuo account Windows su questo PC. Non incollare chiavi API in chat, screenshot, repository o altri luoghi pubblici; se una chiave viene esposta, revocala e creane una nuova. Le vecchie impostazioni che usavano i valori predefiniti locali (`localhost` e `llama3.2`) vengono aggiornate automaticamente a Ollama Cloud.

Per ricreare l'eseguibile Windows aggiornato nella cartella del progetto:

```powershell
python conversione.py
```

`conversione.py` controlla la sintassi di `hak.py`, usa PyInstaller con l'interprete Python che avvia lo script, costruisce e prova il nuovo eseguibile in una cartella temporanea, quindi aggiorna `HAK Assistant.exe` solo se la build e il controllo di avvio riescono. Se PyInstaller manca, installalo una volta con `python -m pip install PyInstaller`. In caso di errore consulta `conversione.log`; il precedente EXE resta intatto. Chiudi HAK Assistant prima di aggiornarlo. L'eseguibile usa comunque i dati e le impostazioni locali in `%APPDATA%\\HAKAssistant`; non contiene la tua chiave API.

La voce è opzionale. `conversione.py` include SpeechRecognition e PyAudio automaticamente se entrambi sono installati nell'interprete corrente; altrimenti crea l'eseguibile senza dettatura. Per abilitarla, installa prima dipendenze vocali compatibili con la versione di Python in uso (`python -m pip install -r requirements.txt`) e rilancia `python conversione.py`.

Il pulsante **Voce** inserisce la trascrizione nel campo di testo; controllala e premi **Invia**. Per abilitarlo:

```powershell
python -m pip install -r requirements.txt
```

La dettatura usa il servizio Google tramite `SpeechRecognition` e richiede una connessione Internet. Per testo e conversazioni vocali inviati al modello, valgono le condizioni e le pratiche di privacy del provider configurato.

## Strumenti e memoria

Puoi chiedere all'assistente di cercare sul web, aprire direttamente un URL o un'app installata, chiudere/minimizzare/ripristinare una finestra (se il nome è riconoscibile dal titolo), aprire una playlist Spotify o cercarla per nome, controllare riproduzione/pausa e brano precedente/successivo, aprire una chat WhatsApp con un numero internazionale, aprire cartelle Home/Desktop/Documenti/Download, creare una nota `.txt` in `Documenti\\HAK Assistant Notes`, spegnere o riavviare Windows, bloccare la sessione, annullare uno spegnimento pianificato o ricordare una preferenza. Un link o dominio indicato viene aperto direttamente, senza passare da Google; la ricerca web viene usata solo se chiedi di cercare informazioni. Se nessuno strumento specifico è adatto, può preparare uno script PowerShell come ultima risorsa. Le azioni richieste nella stessa risposta vengono riepilogate in **un solo popup di autorizzazione**; se la proposta include PowerShell, il popup mostra inizialmente un riepilogo e offre **Mostra comandi** per ispezionare gli script prima di autorizzare. Un eventuale ulteriore gruppo di azioni nella stessa richiesta non viene eseguito né genera una seconda conferma. Lo spegnimento e il riavvio sono pianificati con 10 secondi di margine. Puoi vedere e rimuovere le procedure salvate dal pulsante **Abilità**.

Nella chat premi **Invio** per inviare e **Maiusc+Invio** per andare a capo. Le risposte dell'assistente visualizzano la formattazione Markdown comune, inclusi **grassetto**, *corsivo*, `codice`, titoli, elenchi puntati e blocchi di codice in font monospaziato con etichetta del linguaggio. Sono disponibili i temi **Chiaro**, **Scuro**, **Viola (chiaro)** e **Viola (scuro)**; durante l'elaborazione compare un indicatore animato.

Per gli script PowerShell, il popup mostra lo scopo e il codice completo prima dell'esecuzione: controllalo attentamente e autorizzalo solo se corrisponde alla richiesta. Se approvi, viene aperta una finestra PowerShell visibile con i permessi del tuo account Windows; HAK Assistant non richiede automaticamente privilegi amministrativi e non può verificare l'esito dei comandi, quindi controlla la finestra per errori o richieste di input. Gli script possono comunque modificare o eliminare dati e impostazioni accessibili al tuo account. Uno script può essere salvato in un'abilità, ma viene avviato in modo asincrono e deve quindi essere l'ultimo passaggio della procedura.

Se chiedi una procedura nuova, ad esempio «Impara ad aprire Spotify», l'assistente può impararla come abilità se la richiesta è realizzabile con gli strumenti disponibili; può quindi avviare l'app installata e salvare la procedura per riutilizzarla. Mostra sempre i passaggi e chiede un'unica autorizzazione prima di eseguirli. Le abilità possono avere fino a 8 passaggi, non possono annidare altre abilità o aggirare i permessi; uno script PowerShell, se presente, deve essere l'ultimo passaggio.

Per insegnare una preferenza, ad esempio «Ricorda che preferisco risposte concise», chiedi esplicitamente di ricordarla. Le preferenze sono salvate localmente e incluse nel contesto delle conversazioni successive. Apri **Memoria** per rivederle o cancellarle. Non salvare credenziali o informazioni sensibili.

Una ricerca di playlist per nome apre Spotify sulla ricerca, ma non può selezionare con certezza un risultato ambiguo né garantire l'avvio della riproduzione. Un link/URI di playlist apre quel contenuto in Spotify. I controlli di riproduzione inviano i tasti multimediali globali di Windows: possono essere ricevuti da Spotify o da un'altra app multimediale attiva e l'app non può verificare lo stato risultante. WhatsApp viene aperto su una chat tramite numero internazionale: l'app non invia messaggi e non avvia chiamate, che vanno confermati/selezionati nell'interfaccia WhatsApp.

La memoria non addestra né modifica il modello AI: conserva solo preferenze per personalizzare le risposte. Le istruzioni dell'assistente gli chiedono di provare prima gli strumenti pertinenti, aprire direttamente gli URL, e usare PowerShell solo come ultima risorsa con uno script completo, verificabile e specifico. Ogni operazione sul computer richiede una conferma: non è disponibile una modalità generale senza autorizzazione. Non approvare script che non hai verificato o che contengono operazioni non richieste.

Endpoint, modello, tema, istruzioni e chiave API cifrata vengono salvati in `%APPDATA%\\HAKAssistant\\settings.json`. La chiave è protetta con DPAPI di Windows; una reinstallazione di Windows o un altro account utente può richiedere di inserirla di nuovo.

## Costi e limiti

Non esiste una garanzia affidabile di un'API AI ospitata gratuita e senza limiti: i provider possono applicare quote, limiti e modificare le condizioni. L'app non include chiavi API. Un modello locale tramite Ollama evita una quota API, ma richiede risorse hardware e il modello scelto determina qualità e supporto alle chiamate degli strumenti.

## Allegati ed esportazione

Usa **Allega file** per aggiungere fino a cinque file per messaggio (massimo 8 MB per file e 16 MB complessivi). Sono supportati file di testo UTF-8 comuni (per esempio `.txt`, `.md`, `.py`, `.html`, `.css`, `.js`, `.json`, `.csv`, `.xml`, `.yaml`, `.sql` e `.ps1`) e immagini PNG, JPEG o WebP. Il testo viene incluso nel prompt; le immagini sono inviate nel formato multimodale compatibile con endpoint OpenAI/Ollama. Il modello configurato deve supportare immagini, altrimenti il provider rifiuterà la richiesta. Il contenuto degli allegati viene trasmesso al servizio AI configurato; non allegare dati che non vuoi condividere con quel provider. PDF e formati binari non sono estratti automaticamente.

**Esporta** salva la conversazione corrente in Markdown (`.md`) o HTML (`.html`), in locale nel percorso scelto. Il rendering delle risposte mostra blocchi di codice in font monospaziato con indicazione del linguaggio.

## Aggiornamenti dell'eseguibile

Gli aggiornamenti automatici sono disattivati finché non configuri un repository GitHub pubblico e fidato in **Impostazioni**, nel formato `proprietario/repository`. L'EXE pubblicato deve chiamarsi esattamente `HAK Assistant.exe`; caricalo come asset di una release stabile GitHub. Per ogni release aggiorna `APP_VERSION` in `hak.py` alla stessa versione del tag (per esempio `v1.2.0`) e ricrea l'EXE con `python conversione.py`. GitHub deve esporre per l'asset il digest `sha256` nell'API delle release: se manca, il download viene rifiutato. All'avvio l'app scarica solo versioni più recenti, verifica digest e dimensione, poi pianifica la sostituzione e il riavvio automatici alla chiusura dell'app. Configura soltanto un repository sotto il tuo controllo: una release di quel repository può sostituire ed eseguire il programma. L'aggiornamento automatico opera nell'EXE pubblicato, non quando l'app è avviata con `python hak.py`.

## Stanze online e relay

La chat online usa un relay separato: il client non espone direttamente il PC degli utenti. Per provarlo nella rete locale, avvia in una seconda finestra:

```powershell
python room_server.py
```

Poi configura `http://localhost:8765` in **Impostazioni**; crea una stanza e condividi il codice. Per renderla accessibile su Internet, pubblica `room_server.py` su un servizio/server sotto il tuo controllo e mettilo dietro un proxy HTTPS, quindi configura l'URL HTTPS pubblico. Il relay incluso usa solo la libreria standard, ascolta su `127.0.0.1` per impostazione predefinita e può essere avviato su un host accessibile impostando `HAK_RELAY_HOST=0.0.0.0`; usa `PORT` se impostata dal provider, altrimenti `HAK_RELAY_PORT` o `8765`. Termina TLS al proxy, non nel processo Python. Mantieni una sola istanza del relay: le stanze e i messaggi sono in memoria, non cifrati end-to-end e vengono eliminati dopo 24 ore o al riavvio del server. Chiunque conosca il codice può leggere e inviare messaggi, quindi condividilo solo con gli invitati. Il relay applica limiti di dimensione, lunghezza, numero di messaggi e richieste per IP, ma per un servizio pubblico stabile occorrono hosting, monitoraggio e protezioni di rete propri.
