# 1) AREE DI UTILIZZO

[Torna all'indice](../README.md)

---

## 1.1 Su cosa sono veramente bravi gli agenti AI?

La caratteristica distintiva degli agenti di Intelligenza Artificiale risiede nella loro capacità di eseguire autonomamente task multi-step in sequenza, determinando l’azione successiva in base al risultato del passo precedente.

Pertanto, non solo possiedono una notevole capacità di sintesi, analisi e generazione di contenuti paragonabile agli LLM più avanzati, ma sono anche in grado di pianificare, eseguire e monitorare i risultati per adattarsi alle esigenze del contesto.

Un agente come Github Copilot, ad esempio, analizza i file aperti nell’ambiente di sviluppo integrato (IDE) e **anticipa** le azioni dello sviluppatore, fornendo suggerimenti basati sul codice esistente.  Con la semplice pressione del tasto TAB, il codice viene integrato istantaneamente.  Un ulteriore utilizzo consiste nella creazione di commenti che descrivono le azioni future, consentendo all’agente di generare il codice corrispondente.  Questo approccio garantisce sia la scrittura del codice che la leggibilità basata sui commenti.  Infine, è possibile richiedere a Copilot la generazione di unit test selezionando semplicemente la funzione e digitando “genera unit test con XUnit”.

Questo metodo di utilizzo si dimostra particolarmente efficace, in quanto la quantità di codice generato è facilmente gestibile e “revisionabile”.  Diventa una modalità **naturale** di interazione con gli agenti, garantendo una revisione del codice istantanea e continua, in linea con i tempi di lavoro dello sviluppatore.

Nel caso di strumenti come Claude Code, è possibile guidare l’agente nella generazione di interi progetti “da zero”.  È sufficiente descrivere la tecnologia, il linguaggio di programmazione e le caratteristiche del progetto desiderato.  Maggiore è la specificità delle istruzioni fornite, maggiore sarà la correttezza del risultato ottenuto.  Ad esempio, se si indica all’agente di agire in loop, come nel caso di “compila la soluzione e ripara finché non è priva di errori di compilazione”, l’agente modificherà il codice ripetutamente finché la compilazione non sarà priva di errori.  È inoltre possibile richiedere all’agente di “leggere l’intero progetto e generare un progetto di unit test XUnit con una copertura del codice pari ad almeno il 90%”.

Questo approccio presenta un rischio maggiore, in quanto la quantità di codice generato potrebbe essere considerevole, richiedendo allo sviluppatore senior di revisionare una quantità significativa di codice “estraneo”, che potrebbe talvolta non essere conforme alle policy aziendali.  Al contrario, uno sviluppatore junior potrebbe essere più incline a “fidarsi” del codice generato dall’agente.



>Nota:

>Questi esempi rappresentano solo alcune delle possibilità offerte dall’attuale tecnologia, in continua evoluzione. È plausibile che, in futuro, Claude Code possa integrare funzionalità di suggerimento in-line all’interno dell’IDE, analogamente a quanto offerto da Copilot. E che Copilot fornisca strumenti di pianificazione più evoluti.


## 1.2 Aree di utilizzo:

Le aree in cui ho trovato particolarmente utili gli agent sono:
- Code base giornaliero guidato
- Progettazione software ed infrastruttura guidata
- Costruzione di tools/scripts per l'automazione (IaC)
- Documentazione (creazione e revisione)
- Creazione Unit Tests 
- Scouting/Ricerche guidate
- Pianificazione e costruzione tasks lists
- Prototipazione veloce

Nell’ambito del ciclo di vita di sviluppo di un nuovo software, che si estende dalla fase di progettazione alla sua distribuzione finale, l’implementazione dell’Intelligenza Artificiale (IA) in ogni fase del processo può contribuire significativamente alla riduzione del time-to-market e all’incremento della produttività.  Inoltre, nel contesto del refactoring di software esistente, l’utilizzo di agent automatizzati può alleggerire notevolmente il carico di lavoro associato alle attività di revisione, pianificazione e documentazione, nonché al processo di refactoring stesso.


Nel presente documento, al fine di garantire una valutazione accurata e basata sull’esperienza, verrà effettuato un confronto tra due agenti di Intelligenza Artificiale: GitHub Copilot e Claude Code.

Il capitolo successivo approfondirà le specifiche caratteristiche di ciascun agente, al fine di individuare le aree di applicazione più idonee.


---

[Torna all'indice](../README.md)

---

