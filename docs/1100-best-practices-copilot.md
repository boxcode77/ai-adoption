# ANNEX 2 — Best Practices: GitHub Copilot

[Torna all'indice](../README.md)

---

> [!NOTE]
> Questo capitolo raccoglie le configurazioni consigliate, i pattern di utilizzo efficace e le impostazioni di sicurezza per GitHub Copilot. Le indicazioni si applicano principalmente ai piani Business ed Enterprise, che offrono le garanzie di privacy e governance necessarie per un uso aziendale.

---

## Indice

- [1. Configurazione iniziale](#1-configurazione-iniziale)
- [2. Repository Instructions — copilot-instructions.md](#2-repository-instructions--copilot-instructionsmd)
- [3. Esclusione di file sensibili — .copilotignore](#3-esclusione-di-file-sensibili--copilotignore)
- [4. Impostazioni privacy e sicurezza](#4-impostazioni-privacy-e-sicurezza)
- [5. Utilizzo efficace delle Chat](#5-utilizzo-efficace-della-chat)
- [6. Pattern di prompt efficaci](#6-pattern-di-prompt-efficaci)
- [7. Integrazione nella pipeline CI/CD](#7-integrazione-nella-pipeline-cicd)
- [8. Configurazione enterprise e policy organizzative](#8-configurazione-enterprise-e-policy-organizzative)

---

## 1. Configurazione iniziale

### Estensione VS Code

Installare l'estensione ufficiale dal marketplace Microsoft:

```
GitHub Copilot          → ID: GitHub.copilot
GitHub Copilot Chat     → ID: GitHub.copilot-chat
```

Verificare che l'account GitHub collegato sia quello aziendale con piano Business/Enterprise attivo — non l'account personale.

### Impostazioni consigliate (settings.json)

```json
{
  // Abilita suggerimenti inline
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,
    "markdown": false,
    "yaml": false         // disabilitare per file di configurazione sensibili
  },

  // Mostra suggerimenti solo su richiesta esplicita (Tab) — meno distrattivo
  "editor.inlineSuggest.enabled": true,
  "github.copilot.inlineSuggest.enable": true,

  // Non usare Copilot nei file di test senza revisione
  "github.copilot.advanced": {
    "inlineSuggestCount": 3    // mostra fino a 3 suggerimenti alternativi (Alt+])
  }
}
```

### JetBrains (IntelliJ, Rider, WebStorm)

Installare il plugin **GitHub Copilot** dal marketplace JetBrains. Le impostazioni si trovano in `Settings → Tools → GitHub Copilot`. La funzionalità è equivalente a VS Code; la chat è disponibile come pannello laterale.

---

## 2. Repository Instructions — copilot-instructions.md

Il file `.github/copilot-instructions.md` permette di fornire a Copilot istruzioni persistenti specifiche per il repository. Viene letto automaticamente in ogni sessione di chat nel contesto di quel repository.

### Cosa includere

- Stack tecnologico e versioni in uso
- Convenzioni di naming e stile del progetto
- Pattern architetturali da seguire (o evitare)
- Framework di test in uso
- Indicazioni di sicurezza specifiche del dominio

### Esempio per un progetto .NET / SQL Server

```markdown
# Copilot Instructions

## Stack
- .NET 8, C# 12, ASP.NET Core minimal API
- SQL Server 2022, EF Core 8 con approccio Code First
- xUnit per i test, FluentAssertions per le asserzioni
- Azure DevOps per CI/CD

## Convenzioni
- Nomi in PascalCase per classi e metodi, camelCase per variabili locali
- Usare record immutabili per i DTO
- Restituire sempre Result<T> o OneOf<> per gli errori — mai eccezioni per il flusso normale
- Async/await ovunque — nessun metodo sincrono che wrappa async

## Sicurezza
- Non generare mai query SQL con concatenazione di stringhe — usare sempre parametri EF Core o SqlParameters
- Non loggare mai oggetti contenenti dati personali direttamente — usare metodi di sanitizzazione
- Validare sempre l'input nelle minimal API con FluentValidation prima di passarlo ai servizi

## Test
- Un file di test per ogni classe di servizio, nella cartella Tests/[NomeServizio]Tests
- Usare InMemory database per i test di repository, mock per le dipendenze esterne
- Nominare i test con il pattern: Metodo_Scenario_RisultatoAtteso
```

> [!TIP]
> Il file viene incluso nel contesto di ogni chat, non nei suggerimenti inline. Per avere effetto sulle completion, le istruzioni devono essere richiamate esplicitamente nel prompt della chat.

---

## 3. Esclusione di file sensibili — .copilotignore

Il file `.copilotignore` nella root del repository esclude file e cartelle dal contesto di Copilot, in modo analogo a `.gitignore`. È distinto da `.gitignore`: un file può essere in git ma escluso da Copilot.

### Configurazione consigliata

```gitignore
# Credenziali e segreti
.env
.env.*
*.pem
*.key
*.pfx
*.p12
appsettings.*.json       # contengono connection string in sviluppo locale
secrets.json
**/secrets/**

# Dati sensibili
**/migrations/data/**    # seed data con dati reali
**/*_backup*
**/dumps/**

# Configurazioni infrastruttura con dettagli interni
terraform.tfvars
**/ansible/vars/vault*
**/k8s/secrets/**

# File generati — non utili come contesto
**/bin/**
**/obj/**
**/node_modules/**
**/.terraform/**
```

> [!WARNING]
> `.copilotignore` non impedisce a Copilot di vedere i file se l'utente li incolla manualmente nel prompt della chat. L'esclusione opera solo sulla costruzione automatica del contesto.

---

## 4. Impostazioni privacy e sicurezza

### Piano Business/Enterprise — verifiche da effettuare

Nella console di amministrazione GitHub (`github.com/organizations/[org]/settings/copilot`):

- **Allow GitHub to use my code snippets for product improvements** → **disabilitato** (impostazione predefinita per Business/Enterprise — verificare che sia off)
- **Suggestions matching public code** → impostare su **Block** per evitare che Copilot suggerisca codice identico a snippet pubblici (riduce rischio copyright)
- **Enable Copilot for** → limitare ai membri del team autorizzati, non abilitare per tutta l'organizzazione senza policy

### Verifica lato developer

In VS Code, aprire la command palette (`Ctrl+Shift+P`) e cercare `GitHub Copilot: View Logs` per vedere quali file vengono inviati come contesto nelle richieste. È utile per verificare che i file esclusi non vengano inclusi.

---

## 5. Utilizzo efficace della Chat

### Variabili di contesto disponibili

Copilot Chat supporta variabili speciali per includere contesto preciso:

| Variabile | Cosa include |
|---|---|
| `#file` | Un file specifico (es. `#file:UserService.cs`) |
| `#selection` | La selezione attiva nell'editor |
| `#codebase` | L'intera codebase indicizzata (ricerca semantica) |
| `#terminal` | L'output del terminale attivo |
| `#problems` | Gli errori e warning nel pannello Problems |

### Slash command disponibili

| Comando | Uso |
|---|---|
| `/explain` | Spiega il codice selezionato o un file |
| `/fix` | Propone una correzione per un errore o warning |
| `/tests` | Genera test unitari per il codice selezionato |
| `/doc` | Genera documentazione XML/JSDoc per il metodo selezionato |
| `/optimize` | Suggerisce ottimizzazioni di performance |
| `/new` | Crea un nuovo file o progetto da una descrizione |

### Agenti disponibili in chat

| Agente | Contesto |
|---|---|
| `@workspace` | Ragiona sull'intera codebase del workspace |
| `@vscode` | Risponde a domande su configurazioni VS Code |
| `@terminal` | Suggerisce comandi shell e spiega output del terminale |
| `@github` | Accede a issue, PR e dati del repository remoto |

---

## 6. Pattern di prompt efficaci

### Principio generale

Copilot è efficace quando il contesto è esplicito. Più informazioni fornisce il prompt, più il suggerimento è utile e sicuro.

### Pattern 1 — Specifica il contesto tecnologico

```
❌ "Scrivi una query per cercare gli utenti per email"

✅ "Scrivi una query EF Core 8 in C# per cercare utenti per email
   nella tabella Users. Usa query parametrizzata, restituisci
   Task<User?>, gestisci il caso not found con null."
```

### Pattern 2 — Includi i vincoli di sicurezza nel prompt

```
✅ "Crea un endpoint ASP.NET Core minimal API POST /users che:
   - Valida l'input con FluentValidation
   - Non accetta dati personali nei log
   - Restituisce 400 con dettagli validazione, 201 con l'id creato
   - Usa [Authorize] con policy 'AdminOnly'"
```

### Pattern 3 — Chiedi revisione con focus sicurezza

```
✅ "#selection Esamina questo codice per vulnerabilità di sicurezza.
   Verifica in particolare: SQL injection, gestione degli errori
   che espone stack trace, credenziali hardcoded, mancanza di
   validazione input."
```

### Pattern 4 — Genera test con scenari negativi

```
✅ "/tests Genera test xUnit per questo servizio. Includi:
   - Caso happy path
   - Input null o vuoti
   - Valori boundary
   - Simulazione di errore del repository
   Usa FluentAssertions per le asserzioni."
```

### Pattern 5 — Refactoring con vincoli espliciti

```
✅ "#file:LegacyService.cs Refactora questo file per:
   - Sostituire le query SQL raw con EF Core
   - Aggiungere async/await
   - Separare la logica di business dal data access
   Non cambiare i metodi pubblici e la loro firma."
```

### Cosa evitare

- **Prompt vaghi**: "Migliorami questo codice" → Copilot non sa cosa ottimizzare
- **Contesto mancante**: chiedere di scrivere codice senza specificare framework, versione, pattern
- **Accettare senza leggere**: ogni suggerimento va letto prima di accettare con Tab, specialmente per logiche di sicurezza
- **Prompt con dati reali**: non incollare mai connection string, chiavi API o dati di clienti nel prompt della chat

---

## 7. Integrazione nella pipeline CI/CD

### Copilot code review automatica (GitHub Actions)

Copilot può essere configurato per effettuare review automatiche sulle Pull Request. Nel repository, abilitare la funzionalità da `Settings → Copilot → Code Review`.

Copilot commenterà automaticamente le PR con suggerimenti su:
- Potenziali bug e logiche errate
- Codice non testato
- Mancanza di gestione degli errori

> [!NOTE]
> La code review automatica di Copilot è un primo livello, non un sostituto della review umana. I commenti di Copilot vanno trattati come suggerimenti da valutare, non come verdetti.

### Copilot Autofix (GitHub Advanced Security)

Se il piano include GitHub Advanced Security, Copilot può suggerire fix automatici per le vulnerabilità rilevate da CodeQL. Il fix viene proposto come commit direttamente nell'interfaccia della PR.

**Configurazione consigliata**: abilitare Autofix ma richiedere approvazione umana prima del merge — non accettare fix automatici senza review.

---

## 8. Configurazione enterprise e policy organizzative

### Policy da definire a livello organizzativo

Nella console admin GitHub, definire le seguenti policy prima del rollout:

| Policy | Impostazione consigliata |
|---|---|
| Copilot access | Solo membri esplicitamente aggiunti, non tutta l'org |
| Suggestions matching public code | Block |
| Copilot in github.com | Abilitare solo per i repository approvati |
| Copilot Extensions | Solo estensioni approvate dall'IT |
| Editor chat | Abilitato |
| Copilot in CLI | Valutare in base al profilo di rischio del team |

### Audit log

GitHub mantiene audit log delle azioni Copilot a livello organizzativo. Accessibile da `Organization Settings → Audit Log`, filtrando per `copilot`. Monitorare periodicamente per:
- Accessi da utenti non autorizzati
- Abilitazione di feature non approvate
- Pattern anomali di utilizzo


---

[Torna all'indice](../README.md)

---

> [!NOTE]  
*Documento preparato con supporto di analisi AI. Tutti i dati citati sono stati verificati su fonti primarie.*
*Per aggiornamenti, aprire una Issue o una Merge Request in questo repository.*