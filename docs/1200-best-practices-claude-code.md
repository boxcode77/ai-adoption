# ANNEX 3 — Best Practices: Claude Code

[Torna all'indice](../README.md)

---

> [!NOTE]
> Questo capitolo raccoglie le configurazioni consigliate, i pattern di utilizzo efficace e le impostazioni di sicurezza per Claude Code. Claude Code è uno strumento ad agente autonomo: a differenza di Copilot, può leggere l'intera codebase, eseguire comandi, scrivere file e interagire con tool esterni. Questa potenza richiede una configurazione attenta e un utilizzo consapevole.

---

## Indice

- [1. Installazione e configurazione iniziale](#1-installazione-e-configurazione-iniziale)
- [2. CLAUDE.md — Istruzioni persistenti per il progetto](#2-claudemd--istruzioni-persistenti-per-il-progetto)
- [3. Esclusione di file sensibili — .claudeignore](#3-esclusione-di-file-sensibili--claudeignore)
- [4. Modalità di permesso e controllo delle azioni](#4-modalità-di-permesso-e-controllo-delle-azioni)
- [5. Configurazione MCP — Model Context Protocol](#5-configurazione-mcp--model-context-protocol)
- [6. Pattern di prompt efficaci](#6-pattern-di-prompt-efficaci)
- [7. Slash command e funzionalità avanzate](#7-slash-command-e-funzionalità-avanzate)
- [8. Sicurezza operativa](#8-sicurezza-operativa)

---

## 1. Installazione e configurazione iniziale

### Requisiti

- Node.js 18 o superiore
- Account Anthropic con piano Pro, Team o Enterprise

### Installazione

```bash
npm install -g @anthropic-ai/claude-code
```

### Autenticazione

```bash
claude
# Al primo avvio, viene richiesto il login con l'account Anthropic
```

Per ambienti CI/CD o headless, usare una API key:

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
claude --no-browser
```

> [!WARNING]
> Non inserire la API key in script committati nel repository. Usare variabili d'ambiente iniettate dall'orchestratore (Azure Key Vault, AWS Secrets Manager, GitHub Secrets).

### Avvio nella directory del progetto

```bash
cd /path/to/project
claude
```

Claude Code legge automaticamente il file `CLAUDE.md` nella root del progetto (e nelle directory parent) all'avvio di ogni sessione.

---

## 2. CLAUDE.md — Istruzioni persistenti per il progetto

Il file `CLAUDE.md` nella root del repository è il principale strumento di configurazione del comportamento di Claude Code. Viene caricato automaticamente all'inizio di ogni sessione come contesto persistente.

### Struttura consigliata

```markdown
# CLAUDE.md

## Descrizione del progetto
[Descrizione breve dell'applicazione, dominio di business, utenti target]

## Stack tecnologico
- [Framework, linguaggio, versione]
- [Database e ORM]
- [Tool di testing]
- [Infrastruttura e deploy]

## Struttura del progetto
[Mappa delle directory principali e loro scopo]

## Convenzioni di codice
[Naming, pattern architetturali da seguire, pattern da evitare]

## Regole di sicurezza
[Vincoli specifici — es. "non generare mai query SQL raw", "sanitizza sempre prima di loggare"]

## Workflow
[Come aprire PR, come nominare i branch, come eseguire i test]

## Cosa NON fare
[Lista esplicita di comportamenti da evitare — es. "non modificare i file di migrazione esistenti"]
```

### Esempio per un progetto Python/FastAPI

```markdown
# CLAUDE.md

## Progetto
API REST per gestione ordini B2B. Backend Python 3.12, FastAPI, PostgreSQL 16, SQLAlchemy 2 async.

## Stack
- Python 3.12, FastAPI 0.115, Pydantic v2
- PostgreSQL 16, SQLAlchemy 2 (async), Alembic per le migrazioni
- pytest + pytest-asyncio, httpx per i test
- Docker Compose in sviluppo, Kubernetes in produzione
- GitHub Actions per CI/CD

## Struttura
- app/api/          → router FastAPI (un file per dominio)
- app/services/     → logica di business (nessun accesso diretto al DB)
- app/repositories/ → accesso al database (solo qui SQLAlchemy)
- app/models/       → modelli SQLAlchemy
- app/schemas/      → schemi Pydantic (input/output API)
- tests/            → mirror della struttura app/

## Convenzioni
- Async ovunque — nessun metodo sincrono che chiama async
- I router non contengono logica: delegano ai service
- I service non accedono al DB: delegano ai repository
- Usare Pydantic v2 model_validator per validazione complessa
- Tutti gli endpoint richiedono autenticazione JWT salvo eccezioni esplicite

## Sicurezza
- Mai query SQL raw — usare sempre SQLAlchemy ORM o text() con parametri bind
- Non includere dati personali nei log — usare sanitize_for_log() in app/utils/logging.py
- Validare sempre i parametri di path e query con Pydantic prima di usarli
- Non esporre stack trace nelle risposte HTTP — usare i gestori di errori in app/core/exceptions.py

## Test
- Un file di test per ogni router e ogni service
- Usare pytest fixtures per il database di test (vedere conftest.py)
- Nominare i test: test_[metodo]_[scenario]_[risultato_atteso]

## Cosa NON fare
- Non modificare i file in alembic/versions/ già applicati in produzione
- Non cambiare la firma dei metodi pubblici dei service senza aggiornare tutti i chiamanti
- Non committare file .env
- Non installare dipendenze senza aggiornarle in requirements.txt e requirements-dev.txt
```

### CLAUDE.md globale (utente)

Oltre al file di progetto, Claude Code supporta un file globale in `~/.claude/CLAUDE.md` che viene caricato in ogni sessione, indipendentemente dal progetto. Usarlo per preferenze personali trasversali:

```markdown
# Preferenze globali

- Lingua di risposta: italiano
- Chiedere sempre conferma prima di modificare più di 5 file in una singola operazione
- Non committare automaticamente — chiedere sempre conferma prima di ogni commit
- Preferire soluzioni minimaliste — non aggiungere astrazioni non richieste
```

---

## 3. Esclusione di file sensibili — .claudeignore

Il file `.claudeignore` esclude file e directory dalla lettura automatica di Claude Code durante la costruzione del contesto. La sintassi è identica a `.gitignore`.

> [!IMPORTANT]
> Questo è il principale strumento per prevenire il rischio R-01 (trasmissione involontaria di credenziali al modello cloud). Configurarlo è prioritario rispetto a qualsiasi altra impostazione.

### Configurazione consigliata

```gitignore
# Credenziali e segreti — PRIORITÀ MASSIMA
.env
.env.*
.env.local
.env.production
*.pem
*.key
*.pfx
*.p12
*.cert
appsettings.Development.json
appsettings.Production.json
secrets.json
**/secrets/
**/*credentials*
**/*password*

# Configurazioni con dettagli infrastruttura
terraform.tfvars
**/ansible/vault*
**/k8s/secrets/
.kube/config
~/.aws/credentials

# File di grandi dimensioni non utili come contesto
**/node_modules/
**/bin/
**/obj/
**/.terraform/
**/dist/
**/build/
**/__pycache__/
**/.venv/
**/venv/

# Dati di test con dati reali
**/fixtures/production_*
**/seeds/real_*
**/dumps/
```

---

## 4. Modalità di permesso e controllo delle azioni

Claude Code opera in diverse modalità di permesso che determinano quali azioni può eseguire autonomamente e quali richiedono conferma esplicita.

### Modalità disponibili

| Modalità | Comportamento | Quando usarla |
|---|---|---|
| **Default** | Chiede conferma per azioni irreversibili (scrittura file, comandi shell, commit) | Utilizzo quotidiano standard |
| **Auto-approve** (`--dangerously-skip-permissions`) | Esegue tutto senza chiedere conferma | Solo in ambienti isolati e controllati |
| **Read-only** | Solo lettura — nessuna modifica | Esplorazione codebase, revisione, domande |

> [!CAUTION]
> Non usare mai `--dangerously-skip-permissions` in un ambiente con accesso a dati di produzione, credenziali cloud attive o repository condivisi. Usarlo solo in container isolati o VM dedicate.

### Configurare i permessi nel settings.json

Il file `~/.claude/settings.json` permette di pre-approvare categorie di azioni specifiche, riducendo le interruzioni senza rinunciare al controllo:

```json
{
  "permissions": {
    "allow": [
      "Read(*)",                    // lettura di qualsiasi file
      "Edit(src/**)",               // modifica solo in src/
      "Bash(git status)",           // comando specifico pre-approvato
      "Bash(git diff)",
      "Bash(npm test)",
      "Bash(pytest)"
    ],
    "deny": [
      "Bash(git push*)",            // push sempre richiede conferma
      "Bash(rm *)",                 // nessuna cancellazione automatica
      "Bash(kubectl*)",             // nessun comando Kubernetes automatico
      "Bash(terraform apply*)"      // nessun apply infrastruttura automatico
    ]
  }
}
```

### Permessi a livello di progetto

Il file `.claude/settings.json` nella root del progetto definisce permessi specifici per quel repository, con precedenza sul file globale:

```json
{
  "permissions": {
    "allow": [
      "Bash(dotnet build)",
      "Bash(dotnet test)"
    ],
    "deny": [
      "Bash(dotnet publish*)"      // deploy solo manuale
    ]
  }
}
```

---

## 5. Configurazione MCP — Model Context Protocol

MCP permette a Claude Code di connettersi a tool esterni: database, API, filesystem remoti, servizi aziendali. La configurazione avviene nel file `~/.claude/claude_desktop_config.json` (per Claude Desktop) o nel file di configurazione MCP di Claude Code.

> [!WARNING]
> Installare server MCP solo da fonti verificate e mantenute attivamente. Un server MCP ha accesso alle stesse risorse di Claude Code — filesystem, rete, credenziali. Vedere il rischio R-11 (Tool Poisoning).

### Esempio di configurazione MCP sicura

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/project"],
      "description": "Accesso filesystem limitato alla directory di progetto"
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      },
      "description": "Accesso GitHub — token con scope read:repo, no write"
    }
  }
}
```

### Principi di sicurezza per MCP

- **Scope minimo**: configurare ogni server MCP con le credenziali minime necessarie (token read-only se l'agent non deve scrivere)
- **Nessun server MCP sconosciuto**: non aggiungere server MCP trovati su GitHub casuale senza verificarne il codice sorgente e l'autore
- **Allowlist aziendale**: definire a livello di policy quali server MCP sono approvati per uso nel team
- **Credenziali separate**: non riutilizzare credenziali di produzione per i token MCP — creare token dedicati con scadenza

---

## 6. Pattern di prompt efficaci

### Principio generale

Claude Code ha un contesto molto ampio (200K token) e comprende l'intera codebase. I prompt più efficaci sono quelli che definiscono chiaramente il **risultato atteso**, i **vincoli**, e le **cose da non toccare**.

### Pattern 1 — Task specifico con scope delimitato

```
✅ "Refactora il metodo ProcessOrder in OrderService.cs per:
   1. Estrarre la logica di validazione in un metodo privato ValidateOrder
   2. Sostituire le eccezioni con Result<T> (usando il type già presente in Core/Result.cs)
   3. Aggiungere logging strutturato con ILogger già iniettato

   Non modificare la firma pubblica del metodo.
   Non toccare altri file oltre a OrderService.cs e il relativo test."
```

### Pattern 2 — Esplorazione prima dell'azione

```
✅ "Prima di modificare qualsiasi cosa, analizza come viene gestita
   l'autenticazione in questo progetto. Poi proponi dove sarebbe
   corretto aggiungere il controllo del ruolo 'Manager'
   per l'endpoint /orders/approve."
```

Claude Code esplorerà il codice, spiegherà la struttura esistente e proporrà un piano prima di scrivere una riga.

### Pattern 3 — Debug guidato

```
✅ "Il test OrderServiceTests.ProcessOrder_WithInvalidProduct_ShouldFail
   fallisce con questo errore: [incolla errore].

   Analizza la causa senza modificare nulla.
   Poi proponi la correzione e aspetta la mia approvazione."
```

### Pattern 4 — Generazione con checklist di sicurezza

```
✅ "Crea un nuovo endpoint POST /api/documents/upload per upload di file PDF.

   Requisiti di sicurezza obbligatori:
   - Validare il MIME type reale del file, non solo l'estensione
   - Limite massimo 10MB
   - Salvare con nome casuale generato dal server, non il nome originale
   - Non esporre il path di storage nella risposta
   - Richiedere autenticazione JWT con claim 'upload:documents'

   Usa i pattern già presenti in altri controller per autenticazione e risposta."
```

### Pattern 5 — Review del codice

```
✅ "Leggi i file modificati nell'ultimo commit (git diff HEAD~1)
   e fai una review con focus su:
   - Vulnerabilità di sicurezza
   - Mancanza di gestione degli errori
   - Test mancanti per i nuovi percorsi di codice

   Non proporre refactoring stilistici, solo problemi funzionali e di sicurezza."
```

### Cosa evitare

- **Task troppo ampi senza checkpoint**: "Refactora l'intero modulo orders" senza fasi → Claude potrebbe modificare decine di file in una sola operazione
- **Affidarsi al contesto implicito**: Claude conosce la codebase, ma è più preciso quando il task include riferimenti espliciti ai file/metodi coinvolti
- **Accettare commit automatici senza review**: anche con la modalità di conferma attiva, leggere sempre il diff prima di approvare un commit
- **Task su ambienti di produzione**: non usare Claude Code direttamente su ambienti prod — usarlo in sviluppo/staging e promuovere il codice attraverso la pipeline normale

---

## 7. Slash command e funzionalità avanzate

### Slash command principali

| Comando | Funzione |
|---|---|
| `/help` | Lista dei comandi disponibili |
| `/clear` | Pulisce il contesto della sessione corrente |
| `/compact` | Compatta il contesto per liberare spazio (utile in sessioni lunghe) |
| `/memory` | Mostra e modifica la memoria persistente dell'agente |
| `/model` | Cambia il modello in uso (Opus, Sonnet, Haiku) |
| `/review` | Avvia una review del codice modificato nella sessione |
| `/init` | Genera un CLAUDE.md per il progetto corrente (utile per setup iniziale) |

### Modalità di esecuzione

**Modalità interattiva** (default): sessione REPL con dialogo continuo.

**Modalità non interattiva** (per scripting e CI/CD):

```bash
# Esegue un task singolo e termina
claude --print "Analizza il file src/auth.py per vulnerabilità SQL injection"

# Output in formato JSON (utile per parsing in pipeline)
claude --print --output-format json "Conta le occorrenze di TODO in src/"

# Esecuzione da file di task
claude --print < task.txt
```

### Memoria persistente

Claude Code mantiene una memoria persistente in `~/.claude/memory/` che sopravvive alle sessioni. Utile per tenere traccia di:

- Decisioni architetturali prese nel progetto
- Errori ricorrenti e loro soluzioni
- Preferenze di lavoro

```bash
# Aggiungere una nota alla memoria
"Ricorda che in questo progetto usiamo sempre Polly per i retry
delle chiamate HTTP esterne — non reinventare meccanismi custom"

# Claude aggiornerà automaticamente il suo file di memoria
```

---

## 8. Sicurezza operativa

### Checklist pre-sessione

Prima di avviare una sessione di lavoro su codice sensibile:

- [ ] Il file `.claudeignore` è presente e configurato correttamente
- [ ] Non ci sono file `.env` o `appsettings.*.json` con credenziali reali nella directory di lavoro
- [ ] Il piano Anthropic in uso è Pro/Team/Enterprise (garanzia no-training)
- [ ] I permessi MCP configurati sono al minimo necessario per il task

### Checklist post-sessione / pre-commit

- [ ] Leggere il diff completo prima di approvare qualsiasi commit proposto da Claude
- [ ] Verificare che non siano stati modificati file fuori dallo scope dichiarato nel prompt
- [ ] Controllare che non siano stati aggiunti import o dipendenze non richieste
- [ ] Eseguire i test prima del commit — non fare affidamento sul fatto che Claude li abbia eseguiti

### Segnali di allarme durante una sessione

Interrompere la sessione e verificare se Claude:

- Propone di modificare file di configurazione infrastruttura non menzionati nel task
- Suggerisce di disabilitare test o controlli di sicurezza per far funzionare il codice
- Installa dipendenze non presenti nel progetto senza averle menzionate
- Propone azioni di rete non previste dal task (chiamate a API esterne, download)
- Sembra "ignorare" vincoli esplicitamente dichiarati nel CLAUDE.md o nel prompt

> [!IMPORTANT]  
> Questi comportamenti non indicano necessariamente un problema di sicurezza, ma richiedono attenzione. Chiedere spiegazione prima di procedere: "Perché stai modificando questo file? Non era nel scope del task."

---

[Torna all'indice](../README.md)

---

> [!NOTE]  
*Documento preparato con supporto di analisi AI. Tutti i dati citati sono stati verificati su fonti primarie.*
*Per aggiornamenti, aprire una Issue o una Merge Request in questo repository.*

