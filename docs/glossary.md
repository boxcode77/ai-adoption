# 📖 Glossario Tecnico

[← Conclusioni](06-conclusions.md) · [Torna all'indice](../README.md)

> **59 voci** — Acronimi, Strumenti, Concetti, Metriche, Standard, Sicurezza
> Ambito: AI Tools · .NET/C# · DevOps · Enterprise · Governance · Sicurezza

---

**Legenda categorie:**
`ACRONIMO` `STRUMENTO` `CONCETTO` `METRICA` `STANDARD` `SICUREZZA` `ARCHITETTURA`

---

## A

### AI Agent `CONCETTO`
*Sinonimi: Agente AI*

Sistema software autonomo basato su intelligenza artificiale in grado di pianificare ed eseguire sequenze di azioni multi-step con minimo intervento umano. A differenza dei semplici chatbot, un agente può leggere file, scrivere codice, eseguire comandi e interagire con tool esterni per raggiungere un obiettivo complesso.

---

### AI Hallucination `CONCETTO`
*Sinonimi: Allucinazione AI*

Fenomeno per cui un modello AI genera informazioni plausibili ma fattizie o inventate, presentandole con falsa confidenza. Nel coding, si manifesta come API inesistenti, nomi di librerie fabbricati, o soluzioni sintatticamente corrette ma logicamente errate. Tasso di hallucination GPT-3.5: ~39.6% nei test sistematici (2024).

---

### AMQP `ACRONIMO`
*Advanced Message Queuing Protocol*

Protocollo standard aperto per il message-oriented middleware. Utilizzato da RabbitMQ per la comunicazione asincrona tra servizi. Opera sulla porta **5672** (non-TLS) o **5671** (TLS). Spesso bloccato dai firewall aziendali su reti corporate — verificare sempre la raggiungibilità prima di integrare in ambienti enterprise.

---

### API `ACRONIMO`
*Application Programming Interface*

Insieme di definizioni e protocolli che permettono a due sistemi software di comunicare tra loro. Nel contesto AI: l'Anthropic API e la GitHub Copilot API permettono di integrare le funzionalità AI nelle proprie applicazioni e pipeline CI/CD senza interfaccia grafica.

---

## B

### Benchmark SWE-bench `METRICA`
*Sinonimi: SWE-bench Verified*

Standard industry per valutare la capacità di un modello AI di risolvere issue reali su repository GitHub open-source. Misura la percentuale di bug reali risolti correttamente in modo autonomo senza intervento umano. **Claude Opus 4.5: 80.9%** (febbraio 2026), primo modello oltre la soglia 80%. GPT-4.1 (default Copilot): 56.5%.

---

### Boilerplate `CONCETTO`

Codice standard, ripetitivo e prevedibile che deve essere scritto ogni volta con poche variazioni (es. constructor di una classe, configurazione base di un progetto, CRUD endpoints). È una delle aree dove gli AI coding tools mostrano il guadagno di produttività più alto e più immediato — GitHub Research riporta fino al +75% di risparmio di tempo su task boilerplate.

---

## C

### CAGR `ACRONIMO`
*Compound Annual Growth Rate — Tasso di Crescita Annuo Composto*

Misura la crescita media annua di un investimento o mercato su un periodo, smoothando le variazioni anno su anno. Formula: `CAGR = (Valore Finale / Valore Iniziale)^(1/anni) - 1`. Esempio del Board: il mercato AI Code Generation cresce al **27.1% CAGR** fino al 2032.

---

### CI/CD `ACRONIMO`
*Continuous Integration / Continuous Delivery (o Deployment)*

Pratiche DevOps che automatizzano l'integrazione del codice, i test e il rilascio in produzione. **CI**: ogni commit viene automaticamente compilato e testato. **CD**: il codice validato viene deployato automaticamente in staging o produzione. Strumenti comuni: GitHub Actions, Azure DevOps, Jenkins. GitHub Copilot è nativo nell'ecosistema GitHub Actions.

---

### CISO `ACRONIMO`
*Chief Information Security Officer*

Responsabile della strategia di sicurezza informatica aziendale. Nel contesto AI adoption, supervisiona la configurazione dei tier enterprise per la privacy dei dati, le policy di utilizzo degli strumenti AI e la gestione dei rischi di sicurezza legati al codice AI-generato. Membro chiave del RACI matrix per l'adozione AI.

---

### CLAUDE.md `STRUMENTO`

File di configurazione speciale riconosciuto da Claude Code. Posizionato nella root di un progetto, contiene istruzioni persistenti su architettura, convenzioni di codice, tool interni e linee guida da seguire in tutte le sessioni. Il **67%** dei team che usano Claude Code ha adottato questo formato (Greptile 2025). È l'equivalente di un "system prompt di progetto" — riduce il tempo di contestualizzazione ad ogni sessione.

---

### Claude Code `STRUMENTO`

AI coding agent sviluppato da Anthropic. Opera prevalentemente da terminale o VS Code con un paradigma agentic: esegue task multi-step in autonomia sull'intero codebase, con checkpoint e rollback. Context window di **200K token** (intera codebase in contesto). Modello sottostante: Claude Opus 4.5 (80.9% SWE-bench). Ideale per refactoring legacy, debug complesso, migrazione framework, ragionamento architetturale.

---

### Code Churn `METRICA`

Percentuale di codice che viene riscritto o cancellato entro breve tempo dalla sua creazione. Alta code churn indica bassa qualità iniziale del codice. Secondo **GitClear** (analisi 153M+ righe, 2024), l'uso non governato di AI coding tools ha aumentato il code churn — il codice AI-generato viene spesso ri-lavorato senza una policy di quality review.

---

### Code Completion `CONCETTO`
*Sinonimi: Autocomplete AI*

Funzionalità core degli AI coding tools che suggerisce in tempo reale il completamento del codice mentre si scrive, basandosi sul contesto circostante (file aperto, commenti, funzioni vicine). È il paradigma principale di GitHub Copilot. Acceptance rate di Copilot: ~**30%** (il 30% dei suggerimenti viene accettato dagli sviluppatori, il resto viene scartato).

---

### Context Window `CONCETTO`
*Sinonimi: Finestra di contesto*

Quantità massima di testo (codice, istruzioni, storia conversazione) che un modello AI può "leggere" in una singola elaborazione, misurata in **token**. Valori rilevanti per il Board:
- Claude Code: **200.000 token** (~150.000 parole, o un'intera codebase media)
- GitHub Copilot: **64.000-128.000 token** (~3-4 file tipici)

Maggiore context window = comprensione più profonda dell'architettura = suggerimenti più accurati su sistemi complessi.

---

## D

### Data Residency `CONCETTO`

Requisito che stabilisce che i dati debbano essere archiviati e processati fisicamente in una specifica area geografica, per conformità legale (es. GDPR in Europa). GitHub Enterprise Cloud offre data residency EU da fine 2024. Critico per aziende europee che usano AI tools su codice con dati sensibili di clienti.

---

### DevOps `CONCETTO`

Insieme di pratiche culturali e tecniche che combinano sviluppo software (Dev) e operazioni IT (Ops) per abbreviare il ciclo di vita dello sviluppo. Include automazione CI/CD, Infrastructure as Code, monitoraggio continuo e collaborazione tra team dev e operations. Gli AI tools si integrano nel DevOps principalmente tramite GitHub Actions (Copilot) e MCP (Claude Code).

---

### DPAPI `ACRONIMO`
*Data Protection API*

API Windows per la cifratura di dati sensibili (password, connection string) legata all'identità dell'utente o della macchina corrente. I dati cifrati con DPAPI possono essere decifrati **solo** dallo stesso utente/macchina che li ha cifrati. Utile per proteggere connection string nei deployment su Windows Server isolati — alternativa sicura al plaintext in `appsettings.json`.

---

### DPO `ACRONIMO`
*Data Protection Officer — Responsabile della Protezione dei Dati*

Figura obbligatoria per le organizzazioni che trattano dati personali su larga scala secondo il GDPR. Supervisiona la compliance alle normative privacy, incluso il controllo su quali dati possono essere inviati a servizi AI cloud esterni. Da coinvolgere in Phase 0 del piano di adozione.

---

## E

### EF Core `STRUMENTO`
*Entity Framework Core*

ORM (Object-Relational Mapper) open-source di Microsoft per .NET. Permette di lavorare con database relazionali usando oggetti C# invece di SQL diretto. **EF Core 8** ha introdotto breaking changes nella gestione dei trigger SQL Server: i trigger devono essere dichiarati esplicitamente nel contesto per evitare conflitti con `SaveChanges`.

---

### Enterprise Tier `CONCETTO`

Livello di abbonamento commerciale pensato per grandi organizzazioni, che include funzionalità aggiuntive rispetto ai piani consumer/business: audit log, SSO/SCIM, gestione centralizzata utenti, SLA garantiti, contratti di data privacy specifici (no training sui dati cliente), IP indemnity. GitHub Copilot Enterprise: **$39/utente/mese**.

---

### ERP `ACRONIMO`
*Enterprise Resource Planning*

Sistema software integrato che gestisce i processi aziendali principali (contabilità, magazzino, ordini, HR) in un'unica piattaforma. Nel contesto del team: la sincronizzazione ERP è una delle aree dove Claude Code può assistere nella gestione delle integrazioni, stored procedure complesse e mapping tra modelli di dati ERP e domain model .NET.

---

### EU AI Act `STANDARD`

Regolamento europeo sull'intelligenza artificiale, entrato in vigore nel 2024 con applicazione graduale fino al 2026. Classifica i sistemi AI per livello di rischio (inaccettabile, alto, limitato, minimo) e impone obblighi di trasparenza, governance e conformità. Gli AI coding tools rientrano generalmente nella categoria **rischio limitato** — comunque richiesta documentazione dell'uso AI nei processi di sviluppo.

---

## F

### Firewall Aziendale `SICUREZZA`

Sistema di sicurezza di rete che controlla il traffico in entrata e uscita secondo regole predefinite. Può bloccare protocolli specifici degli AI tools:
- Porta **5672** (AMQP/RabbitMQ) — spesso bloccata su reti corporate
- Traffico HTTPS verso `api.anthropic.com` — verificare con il team IT
- Traffico verso `api.github.com` per Copilot

Rilevante nella Phase 0 del piano di adozione: verificare la raggiungibilità delle API prima del pilot.

---

### Flow State `CONCETTO`

Stato psicologico di massima concentrazione e produttività, in cui lo sviluppatore è completamente immerso nel problema senza distrazioni. Secondo **McKinsey Developer Survey 2025**, gli sviluppatori che usano AI tools hanno il **doppio** delle probabilità di raggiungere il flow state, grazie alla riduzione del lavoro cognitivo ripetitivo su task meccanici (boilerplate, documentazione, ricerca sintattica).

---

## G

### GDPR `ACRONIMO`
*General Data Protection Regulation*

Regolamento UE 2016/679 sulla protezione dei dati personali, entrato in vigore nel 2018. Impone requisiti stringenti su raccolta, archiviazione e trattamento dei dati personali di cittadini UE. **Rilevante per gli AI tools**: vietato inviare PII di utenti/clienti nei prompt degli strumenti AI cloud senza adeguata base giuridica e DPA firmato con il vendor.

---

### GitHub Actions `STRUMENTO`

Piattaforma CI/CD integrata in GitHub che permette di automatizzare workflow di build, test e deployment tramite file YAML. Si integra nativamente con GitHub Copilot per suggerimenti nell'authoring dei workflow. Parte dell'ecosistema che rende Copilot la scelta naturale per team già su GitHub — le pipeline possono sfruttare AI per generazione e manutenzione degli script.

---

### GitHub Copilot `STRUMENTO`

AI coding assistant sviluppato da GitHub (Microsoft) integrato direttamente nell'IDE (VS Code, Visual Studio, JetBrains, Neovim). Suggerisce codice in tempo reale mentre si scrive, basandosi su commenti, nomi di funzione e codice circostante. Powered principalmente da modelli OpenAI (GPT-4.1 default; Claude Opus 4.5 disponibile in Pro+/Enterprise da Feb 2026). Market leader nel segmento code completion con **68% di adozione** tra i developer professionali (Stack Overflow 2025).

---

## I

### IaC `ACRONIMO`
*Infrastructure as Code*

Pratica di gestire e provisionare infrastruttura IT tramite file di configurazione leggibili da macchine (es. Terraform, Bicep, ARM Templates) invece di processi manuali. Gli AI tools accelerano la scrittura di script IaC — particolarmente utile per ambienti con multiple istanze server isolate con configurazioni simili ma non identiche.

---

### IDE `ACRONIMO`
*Integrated Development Environment — Ambiente di Sviluppo Integrato*

Software che combina editor di codice, debugger, compiler e altri strumenti in un'unica interfaccia. Esempi rilevanti per il team:
- **Visual Studio** — principale per .NET Framework
- **VS Code** — principale per .NET 8 / cross-platform, supporto nativo per entrambi gli AI tools
- **JetBrains Rider** — alternativa per .NET, supporto Copilot maturo, Claude Code limitato a Feb 2026

---

### IIS `ACRONIMO`
*Internet Information Services*

Web server di Microsoft per Windows Server, usato per ospitare applicazioni .NET/ASP.NET Core. È la piattaforma di deployment comune per applicazioni .NET enterprise in ambienti Windows Server on-premise. Rilevante per la configurazione di ambienti isolati con requisiti di sicurezza specifici (connection string via DPAPI, no accesso internet diretto).

---

### IP Indemnity `SICUREZZA`
*Sinonimi: Indennità IP / Protezione IP*

Protezione legale offerta da un vendor: se il codice generato dall'AI viene trovato in violazione di diritti di proprietà intellettuale (es. codice simile a open-source con licenza restrittiva), il vendor si assume la responsabilità legale e i costi di difesa. **GitHub Copilot Business/Enterprise include IP indemnity; Claude Code attualmente no** — gap critico per settori regolamentati o con clienti che richiedono garanzie IP esplicite.

---

## J

### JSON `ACRONIMO`
*JavaScript Object Notation*

Formato leggero per lo scambio di dati, strutturato come coppie chiave-valore. Standard de facto per le API REST. Usato estensivamente per la configurazione degli AI tools, per la formattazione dei dati nelle integrazioni ERP e come formato di output nelle API Anthropic. T-SQL supporta JSON nativo dalla versione SQL Server 2016+.

---

## K

### KPI `ACRONIMO`
*Key Performance Indicator — Indicatore Chiave di Performance*

Metrica misurabile usata per valutare il successo nel raggiungimento di un obiettivo. Per l'adozione AI tools nel team, i KPI raccomandati sono: story points per sprint, bug rate sul codice AI-generato, acceptance rate dei suggerimenti Copilot, tempo medio di onboarding, NPS developer, code churn. Da raccogliere come **baseline** prima del pilot.

---

## L

### Lean Software Development `CONCETTO`

Metodologia di sviluppo software derivata dal Lean Manufacturing Toyota, che mira a eliminare gli sprechi (waste) nel processo di sviluppo. I 7 principi: eliminare lo spreco, amplificare l'apprendimento, decidere tardi, consegnare velocemente, responsabilizzare il team, costruire l'integrità, vedere il tutto. Gli AI tools si allineano con il principio di **eliminazione degli sprechi** — riducono il tempo su task ripetitivi (boilerplate, documentazione, ricerca).

---

### LLM `ACRONIMO`
*Large Language Model — Modello Linguistico di Grandi Dimensioni*

Modello di machine learning addestrato su enormi quantità di testo per comprendere e generare linguaggio naturale (e codice). Esempi rilevanti: **Claude** (Anthropic), **GPT-4** (OpenAI), **Gemini** (Google). La dimensione si riferisce al numero di parametri (miliardi/trilioni). I modelli più grandi (es. Claude Opus) hanno prestazioni migliori su task complessi ma sono più costosi da eseguire.

---

## M

### MCP `ACRONIMO`
*Model Context Protocol*

Protocollo aperto sviluppato da Anthropic che permette a Claude Code di connettersi con tool esterni, database, API interne e sistemi di terze parti. Consente di costruire workflow agentic personalizzati — es. invocare linter interni, interrogare documentazione aziendale, triggerare build, accedere a ticket di progetto. Il **67%** dei team usa configurazioni MCP nei propri CLAUDE.md (Greptile 2025).

---

### METR `ACRONIMO`
*Model Evaluation & Threat Research*

Organizzazione di ricerca indipendente che conduce studi controllati sull'impatto degli AI tools sulla produttività degli sviluppatori. Il loro studio (Novembre 2025) ha evidenziato il "paradosso della produttività": sviluppatori a cui è **permesso** usare AI impiegano in media il **19% di tempo in più** senza governance strutturata — dato cruciale per giustificare un approccio formale all'adozione.

---

### MFA `ACRONIMO`
*Multi-Factor Authentication — Autenticazione a Più Fattori*

Meccanismo di sicurezza che richiede due o più prove di identità per accedere a un sistema (es. password + OTP, password + hardware key). Obbligatoria nei tier enterprise degli AI tools (GitHub Enterprise, Anthropic Enterprise) per proteggere l'accesso agli strumenti che hanno visibilità sul codice sorgente aziendale.

---

## N

### NIST AI RMF `STANDARD`
*National Institute of Standards and Technology — AI Risk Management Framework*

Framework statunitense per la gestione del rischio nei sistemi AI, strutturato in 4 funzioni:
- **Govern** — struttura di governance e accountability
- **Map** — identificazione del contesto e dei rischi
- **Measure** — analisi e valutazione dei rischi
- **Manage** — trattamento e monitoraggio dei rischi

È il framework più adottato nelle enterprise americane, compatibile con l'EU AI Act. Adottarlo come base per la governance AI del team garantisce solidità metodologica.

---

### NPS `ACRONIMO`
*Net Promoter Score*

Metrica per misurare la soddisfazione e la probabilità di raccomandazione. Calcolato chiedendo: *"Su scala 0-10, quanto raccomanderesti questo strumento a un collega?"* Formula: **% Promotori (9-10) - % Detrattori (0-6)**. Nel contesto AI adoption: misura il livello di soddisfazione degli sviluppatori verso i nuovi tool. Target raccomandato per il pilot: **NPS ≥ 7**.

---

## O

### ORM `ACRONIMO`
*Object-Relational Mapper*

Libreria software che crea un "ponte" tra il modello a oggetti di un linguaggio di programmazione e un database relazionale, permettendo di manipolare dati come oggetti invece di scrivere SQL direttamente. **Entity Framework Core** è l'ORM principale per .NET. Gli AI tools accelerano la scrittura di migration e query LINQ, ma richiedono review per pattern avanzati (trigger, shadow properties, bulk operations).

---

## P

### PII `ACRONIMO`
*Personally Identifiable Information — Dati Personali Identificativi*

Qualsiasi informazione che può essere usata per identificare un individuo: nome, codice fiscale, email, numero di telefono, indirizzo IP, dati biometrici. **Invio di PII a servizi AI cloud è soggetto a GDPR** e richiede base giuridica adeguata. Le policy di acceptable use devono **esplicitamente vietare** l'inserimento di PII nei prompt degli AI tools, a meno di configurazioni enterprise specifiche con DPA firmato.

---

### PostgreSQL `STRUMENTO`

Sistema di gestione di database relazionale open-source avanzato, noto per affidabilità e funzionalità estese (JSON nativo, full-text search, CTE ricorsive, estensioni). Nel contesto del team: usato nello stack del bot Telegram con .NET Core e Docker. Claude Code supporta ottimamente la scrittura di query PostgreSQL complesse grazie al context window esteso.

---

### Prompt Engineering `CONCETTO`

Disciplina di progettazione e ottimizzazione delle istruzioni (prompt) fornite a un modello AI per ottenere output di qualità massima. Tecniche principali:
- **Few-shot**: fornire esempi del risultato atteso
- **Chain-of-thought**: chiedere di ragionare passo per passo
- **Role prompting**: assegnare un ruolo specifico al modello
- **XML tags**: strutturare input e output con tag semantici

È una competenza chiave da sviluppare nel team — settimane 3-4 del piano di training.

---

## R

### RACI `ACRONIMO`
*Responsible, Accountable, Consulted, Informed*

Framework per la definizione dei ruoli e delle responsabilità in un progetto o processo:
- **R** = chi esegue il lavoro
- **A** = chi risponde del risultato finale (una sola persona per decisione)
- **C** = chi viene consultato prima di decidere
- **I** = chi viene informato dopo la decisione

Usato nel Board per definire la governance dell'adozione AI. Regola fondamentale: per ogni decisione, **una sola persona A**.

---

### RabbitMQ `STRUMENTO`

Message broker open-source che implementa il protocollo AMQP. Permette la comunicazione asincrona tra servizi tramite code di messaggi — pattern produttore/consumatore. In architetture enterprise può essere bloccato da firewall aziendali (porta 5672). Nella configurazione IIS-hosted in .NET, il connection lifecycle richiede attenzione specifica per evitare connection leak.

---

### ROI `ACRONIMO`
*Return on Investment — Ritorno sull'Investimento*

Misura il guadagno netto di un investimento rispetto al suo costo. Formula: `ROI = (Beneficio - Costo) / Costo × 100%`. Valori di riferimento per gli AI tools:
- Microsoft Market Study 2025: ROI medio **3.5×** (350%)
- Top 1% delle aziende: fino a **10.3×**
- Scenario realistico primo anno (adozione strutturata): **1.5× – 2.5×**

---

## S

### SCIM `ACRONIMO`
*System for Cross-domain Identity Management*

Standard per automatizzare il provisioning/deprovisioning degli utenti tra sistemi (es. Active Directory aziendale e GitHub Enterprise). Permette di gestire centralmente chi ha accesso agli AI tools: quando un dipendente lascia l'azienda, l'accesso viene revocato automaticamente senza intervento manuale. Parte dei requisiti di sicurezza del tier Enterprise.

---

### SLA `ACRONIMO`
*Service Level Agreement — Accordo sul Livello di Servizio*

Contratto formale tra fornitore e cliente che definisce i livelli minimi garantiti di disponibilità, performance e supporto (es. uptime 99.9%, tempo di risposta al supporto < 4h). I tier Enterprise degli AI tools includono SLA espliciti — critico per team che dipendono dagli strumenti per la produzione quotidiana.

---

### SQL Injection `SICUREZZA`

Vulnerabilità di sicurezza critica in cui un attaccante inserisce codice SQL malevolo in un campo di input per manipolare o estrarre dati dal database. **Rilevante per il codice AI-generato**: studi mostrano che il **40% delle query SQL generate da AI** contengono potenziali vulnerabilità injection. La checklist di code review deve includere **test specifici per SQL injection** su ogni query proveniente da AI tools.

---

### SQL Server `STRUMENTO`
*Sinonimi: Microsoft SQL Server, MSSQL*

Sistema di gestione di database relazionale (RDBMS) di Microsoft, ampiamente usato in contesti enterprise .NET. Supporta T-SQL (estensione di SQL con costrutti procedurali), trigger, stored procedure, JSON, Always Encrypted. È il database principale dello stack del team, con note criticità di compatibilità con EF Core 8 (trigger che richiedono dichiarazione esplicita nel DbContext).

---

### SSO `ACRONIMO`
*Single Sign-On — Autenticazione Unica*

Meccanismo che permette a un utente di autenticarsi una sola volta e accedere a più applicazioni senza reinserire le credenziali. Nei tier enterprise degli AI tools: integrazione con il provider di identità aziendale (Azure AD, Okta, Active Directory Federation Services) per gestione centralizzata degli accessi e deprovisioning automatico.

---

### Story Points `METRICA`

Unità di misura relativa della complessità/sforzo di un task in metodologie Agile/Scrum. Non corrispondono a ore ma a complessità relativa (scala tipica: 1, 2, 3, 5, 8, 13). Usati come KPI principale per misurare la **velocity** del team prima e dopo l'adozione AI tools — numero di story points completati per sprint. Target del Board: **+20% velocity** entro la fine del Phase 1 pilot.

---

## T

### T-SQL `ACRONIMO`
*Transact-SQL*

Estensione proprietaria Microsoft di SQL usata in SQL Server e Azure SQL. Aggiunge costrutti procedurali a SQL standard: variabili, flow control (`IF`/`WHILE`/`TRY-CATCH`), stored procedure, trigger, funzioni tabellari, CTE. Claude Code eccelle nella scrittura e nel debugging di T-SQL complesso grazie al context window esteso — può analizzare procedure di centinaia di righe con le loro dipendenze.

---

### Token (AI) `CONCETTO`

Unità minima di testo processata da un modello AI. Approssimativamente:
- 1 token ≈ 0.75 parole in inglese
- Le parole comuni sono 1 token; le parole rare o tecniche possono essere 2-3 token
- Il codice sorgente è più denso: ~1 token per ~0.5 parole di codice

Il **context window** si misura in token. 200.000 token di Claude Code corrispondono a circa 150.000 parole o 500 pagine di testo — sufficiente per un intero progetto medium-size.

---

## V

### Velocity (Agile) `METRICA`
*Sinonimi: Sprint Velocity*

Misura della quantità di lavoro che un team Agile completa in un'iterazione/sprint. Espressa in story points o numero di task completati. È il KPI principale per misurare l'impatto degli AI tools:
- GitHub Research: **+55%** velocità di completamento task con Copilot
- Enterprise study medio: **+26%** di miglioramento generale

---

### Vendor Lock-in `CONCETTO`

Situazione in cui un'organizzazione dipende fortemente da un fornitore specifico, rendendo difficile e costosa la migrazione verso alternative. Nel contesto AI tools: rischio di lock-in su GitHub/Microsoft (Copilot) o Anthropic (Claude Code). **Strategia di mitigazione raccomandata**: adottare entrambi gli strumenti e monitorare le alternative open-source emergenti (es. Ollama, Codeium, self-hosted models).

---

## W

### WebApplicationFactory `STRUMENTO`

Classe di ASP.NET Core (`Microsoft.AspNetCore.Mvc.Testing`) per i test di integrazione che permette di istanziare l'intera applicazione in memoria durante i test, senza deployment su un server reale. Permette di testare endpoint HTTP, middleware e dependency injection end-to-end. Utile nelle pipeline CI/CD per test di integrazione veloci senza infrastruttura esterna.

---

### Windows Server `STRUMENTO`

Sistema operativo server di Microsoft per ambienti enterprise. Piattaforma comune per deployment di applicazioni .NET Core con IIS. Nel contesto del team: gestione di istanze server isolate con requisiti di sicurezza specifici. Considerazioni per gli AI tools: le sessioni Claude Code in ambienti Windows richiedono PowerShell o WSL2 per il paradigma terminal-first.

---

## Y

### YoY `ACRONIMO`
*Year over Year — Anno su Anno*

Metrica di confronto che misura la variazione percentuale di un indicatore rispetto allo stesso periodo dell'anno precedente. Formula: `YoY% = (Valore attuale - Valore anno precedente) / Valore anno precedente × 100`. Esempio del Board: la spesa enterprise AI è cresciuta da $11.5B (2024) a $37B (2025) = **+222% YoY** (3.2×).

---

*Versione 1.0 — Febbraio 2026. La terminologia AI evolve rapidamente — si raccomanda revisione semestrale.*
*Per aggiungere o modificare voci, aprire una Merge Request con label `docs/glossary`.*

---

*← [Conclusioni](06-conclusions.md) · [Torna all'indice](../README.md)*
