# INTRODUZIONE

[Torna all’indice](../README.md)

--- 

Gli strumenti di Intelligenza Artificiale (IA) per lo sviluppo software, quali Claude Code, GitHub Copilot, Cursor e strumenti simili, rappresentano attualmente tra le tecnologie più innovative a disposizione di un team di sviluppo. Questi strumenti promettono, e in numerosi casi realizzano, incrementi significativi della produttività: attività che precedentemente richiedevano giorni possono essere completate in ore, la curva di apprendimento relativa a nuove tecnologie si appiattisce e la rilevazione di bug può essere anticipata.

Nel contesto dello sviluppo software, si distinguono due modalità principali di utilizzo degli **agenti** di Intelligenza Artificiale. La prima modalità prevede che l’agente fornisca suggerimenti di codice al programmatore durante il processo di sviluppo (in-line suggestions). La seconda modalità consente all’agente di generare intere architetture software a partire da istruzioni fornite in modo strutturato.

Il presente documento si propone di analizzare gli **agenti** con lo scopo di aumentare la produttività, sottolineando l’importanza di non confonderli con i semplici modelli di linguaggio (LLM). La distinzione fondamentale tra gli agenti di Intelligenza Artificiale e un semplice LLM risiede nella capacità degli agenti di operare in modo continuativo fino al completamento del compito, tenendo conto del contesto, mentre gli LLM forniscono (generano) una singola risposta.

Pertanto, questi strumenti presentano una caratteristica distintiva rispetto a qualsiasi altro software adottato in precedenza: ***« agiscono »***. Non si limitano a rispondere a domande. Leggono il codebase, eseguono comandi, accedono a repository, interrogano database e invocano API. Operano con i **privilegi del developer che li ha configurati**. Questa caratteristica viene spesso trascurata e può rappresentare un rischio tanto quanto un vantaggio.

Un aspetto che viene sottovalutato o ignorato all’interno delle organizzazioni è che il team **sta già utilizzando** strumenti IA, approvati o meno. GitHub Copilot è già integrato in VSCode e l’utilizzo di Claude Code di Anthropic o ChatGPT di OpenAI richiede semplicemente una connessione internet.

Per curiosità, per cultura personale o per interesse, tecnici e ingegneri sono spesso attratti dall’utilizzo di questi strumenti anche in ambiente lavorativo, senza una corretta informazione sui potenziali rischi.

### Scopo di questo documento

Questo documento non ha l’intento di scoraggiare l’adozione di tali strumenti, poiché i vantaggi sono reali e significativi (come illustrato nelle sezioni successive). 

Gli obiettivi di questo documento sono duplici:

1. Fornire una valutazione trasparente dei rischi associati all’adozione di tali tecnologie, al fine di garantire decisioni informate e la definizione di un quadro di governance adeguato alla portata e alle implicazioni delle tecnologie stesse.

2. Creare le condizioni necessarie affinché l’implementazione di queste tecnologie possa generare vantaggi concreti per l’azienda e contribuire al raggiungimento degli obiettivi operativi.

3. Illustrare esempi di implementazione e configurazione dell’Intelligenza Artificiale nel contesto dello sviluppo software.


Tali obiettivi presentano una certa complessità, considerata la natura in continua evoluzione di queste tecnologie.



---

[Torna all’indice](../README.md)

---
