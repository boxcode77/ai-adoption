# 5) Governance, Regolamentazione e Policy Aziendale

[Torna all'indice](../README.md)

---

## 5.1 Stato della Governance AI nelle Organizzazioni

La governance AI è diventata una priorità strategica — non più opzionale.

- **77%** delle organizzazioni sta sviluppando programmi di AI governance (IAPP 2025)
- **47%** la classifica tra le top-5 priorità strategiche
- **95%** dei C-suite ha già subito almeno un incidente legato all'uso enterprise di AI (Infosys survey)
- La fiducia nelle aziende AI è scesa dal 61% (2019) al **53%** (2025) — McKinsey Technology Trends Outlook

---

> [!WARNING]
> **Il 97% degli sviluppatori usa AI tool in modo autonomo prima che esistano policy aziendali** (Second Talent 2025). Questo significa che il vostro team probabilmente **sta già usando** strumenti AI non gestiti. Definire una governance proattiva è urgente.

---

## 5.2 Framework di Governance — 6 Componenti

Basato su NIST AI RMF, EU AI Act e best practice enterprise 2025:

### Componente 1 — Policy di Utilizzo (Acceptable Use Policy)

**Contenuto minimo della policy:**

- Elenco degli strumenti **approvati** (Claude Code, GitHub Copilot) e **non approvati**
- Task permessi e task vietati con AI (es: no codice di autenticazione senza review, no dati PII nei prompt)
- Obblighi di disclosure: il codice AI-generato deve essere identificabile nel commit/PR
- Processo di escalation in caso di dubbi sulla sicurezza del codice generato
- Revisione semestrale della policy

**Template di categorizzazione dei task:**

| Categoria | Esempi | Regola |
|---|---|---|
| 🟢 **Libero uso** | Boilerplate, documentazione, test unitari semplici | Nessuna restrizione |
| 🟡 **Con review** | Query SQL, logica business, API endpoint | Code review obbligatoria con checklist AI |
| 🔴 **Vietato o solo senior** | Autenticazione, cifratura, deploy produzione, PII | Solo senior developer, doppia review |

---

### Componente 2 — Data Privacy & Security

**Classificazione dati per l'uso con AI tools:**

```
LIVELLO 1 — Dati pubblici / non sensibili
  └─ Permesso invio a AI tools cloud (Copilot, Claude)
  └─ Esempi: codice open-source, documentazione pubblica

LIVELLO 2 — Dati aziendali interni
  └─ Permesso solo con tier Enterprise (no-training confermato)
  └─ Esempi: codice sorgente proprietario, architetture interne

LIVELLO 3 — Dati sensibili / PII / IP critica
  └─ VIETATO inviare a qualsiasi AI tool cloud
  └─ Esempi: dati clienti, credenziali, chiavi API, segreti aziendali
```

**Azioni tecniche da implementare:**

- Configurare **GitHub Copilot Business** con `no training` sui dati aziendali (opzione enterprise)
- Configurare **Anthropic Enterprise** con data retention policy definita
- Implementare **DPAPI encryption** per connection string e secrets (non passare mai secrets nei prompt)
- Bloccare l'invio di PII nei prompt tramite policy di rete o tool DLP
- Audit log attivo su GitHub Copilot Enterprise per tutte le sessioni

---

### Componente 3 — Qualità del Codice AI

**Checklist di code review per codice AI-generato:**

```markdown
## AI Code Review Checklist

### Correttezza logica
- [ ] La logica corrisponde ai requisiti, non solo alla sintassi?
- [ ] I casi edge sono gestiti (null, empty, overflow)?
- [ ] I tipi di dati sono appropriati per il contesto?

### Sicurezza
- [ ] Nessuna SQL injection / query non parametrizzata?
- [ ] Input validation presente?
- [ ] Nessuna credenziale o secret hardcoded?
- [ ] Gestione errori non espone stack trace in produzione?

### Qualità
- [ ] Il codice segue le convenzioni del progetto?
- [ ] La complessità è giustificata?
- [ ] I test coprono almeno l'80% del codice nuovo?
- [ ] La documentazione è aggiornata?

### Architettura
- [ ] Il codice è coerente con l'architettura esistente?
- [ ] Non introduce dipendenze non approvate?
- [ ] Non duplica funzionalità già esistenti?
```

---

### Componente 4 — IP & Compliance

**GitHub Copilot:**
- ✅ **IP Indemnity inclusa** in Business ed Enterprise: se il codice generato è trovato in violazione di IP, GitHub assume la responsabilità legale
- ✅ Filter anti-duplicazione codice open-source attivabile nelle impostazioni
- Data residency EU disponibile per GitHub Enterprise Cloud

**Claude Code:**
- ❌ **Nessuna IP indemnity** (valutare se questo è un blocco per il settore)
- ✅ API/Enterprise: i dati non vengono usati per training del modello
- MCP permette di tenerlo integrato con sistemi on-premise

**EU AI Act — Rilevanza per il team:**
- Gli AI coding tools rientrano generalmente nella categoria **rischio limitato** (non ad alto rischio)
- Obbligo di trasparenza: gli utenti devono sapere che stanno interagendo con AI
- Documentare l'uso degli strumenti AI nei processi di sviluppo

---

### Componente 5 — Training e Abilitazione

**Piano di formazione consigliato (8 settimane):**

| Settimane | Contenuto | Metodo |
|---|---|---|
| 1-2 | Fondamenti degli strumenti: setup, interfaccia, primi prompt | Workshop hands-on (2h) |
| 3-4 | Prompt engineering: tecniche base, few-shot, chain-of-thought | Workshop + esercizi pratici |
| 5-6 | Casi d'uso avanzati: refactoring, debug, architettura | Pair programming con AI champion |
| 7-8 | Integrazione nel workflow CI/CD, CLAUDE.md, Copilot Instructions | Progetto pilota reale |

**Ruolo degli AI Champions:**
- 2 developer senior volontari e entusiasti
- Prima risorsa di supporto peer-to-peer per il team
- Responsabili della manutenzione delle istruzioni di progetto (CLAUDE.md, Copilot org settings)
- Punto di raccolta feedback per il miglioramento continuo della governance

---

### Componente 6 — Monitoraggio & KPI

**KPI da raccogliere prima del pilot (baseline) e durante:**

| KPI | Come misurare | Frequenza |
|---|---|---|
| Story points per sprint | Dati dal tool di project management | Ogni sprint |
| Bug rate sul codice AI-generato | Label PR "ai-assisted" + bug tracking | Mensile |
| Acceptance rate suggerimenti Copilot | Dashboard GitHub Copilot Business | Settimanale |
| Tempo medio onboarding nuovo sviluppatore | Survey + tracking ore | Per ogni nuovo hire |
| NPS developer per gli strumenti AI | Survey anonima 1 domanda | Mensile |
| Code churn (% codice riscritto) | Strumenti come GitClear o analisi git | Mensile |

---

## 5.3 RACI Matrix per l'Adozione AI Tools

| Decisione / Attività | CTO / IT Mgr | Dev Lead | Sviluppatori | Legal / DPO | CISO |
|---|---|---|---|---|---|
| Selezione strumenti AI approvati | **A** | C | I | C | C |
| Definizione Acceptable Use Policy | **A** | C | I | C | R |
| Configurazione Enterprise tier e privacy | **A** | I | I | C | R |
| Training e onboarding team | I | **A/R** | C | I | I |
| Code review codice AI-generato | I | **A** | R | I | I |
| Monitoraggio KPI e revisione policy | **A** | R | C | I | C |
| Gestione incidenti sicurezza AI | A | C | I | C | **R** |
| Aggiornamento CLAUDE.md / Copilot Instructions | I | **A** | R | I | I |

> **Legenda:** R = Responsible (esegue) · A = Accountable (risponde del risultato) · C = Consulted (consultato) · I = Informed (informato)

---

## 5.4 Configurazioni Tecniche Raccomandate

### GitHub Copilot Business — Setup Enterprise

```yaml
# Configurazione raccomandata GitHub Copilot Business
organization:
  copilot:
    seat_management: sso_required           # Solo utenti SSO autenticati
    suggestions_matching_public_code: block  # Blocca duplicazione codice pubblico
    ide_chat: enabled
    cli: enabled
    allow_ghe_com: true                      # Per GitHub Enterprise

  # Copilot Instructions (equivalente CLAUDE.md per Copilot)
  instructions: |
    Stai assistendo il team di sviluppo [AZIENDA].
    Stack principale: .NET 8, C#, SQL Server, Entity Framework Core 8.
    Segui sempre i pattern architetturali definiti in /docs/architecture.md.
    Non suggerire librerie non approvate. Vedi /docs/approved-packages.md.
    Per query SQL, usa sempre parametrizzazione. Mai string concatenation.
    Per connection string, usa sempre DPAPI o configurazione protetta.
```

### Claude Code — CLAUDE.md Template

```markdown
# CLAUDE.md — Istruzioni di Progetto

## Stack tecnologico
- .NET 8 / C# 12
- SQL Server 2019+, Entity Framework Core 8
- RabbitMQ (AMQP), IIS su Windows Server
- GitHub Actions per CI/CD

## Convenzioni di codice
- Namespace: [AZIENDA].[Modulo].[Layer]
- Tutti i metodi asincroni con suffisso Async
- Repository pattern con UnitOfWork
- Gestione errori: Result<T> pattern, non eccezioni per flow control

## Vincoli di sicurezza
- MAI hardcodare connection string o API key
- Usare sempre DPAPI o IConfiguration per i segreti
- Parametrizzare sempre le query SQL
- Validare sempre l'input utente prima di query DB

## Architettura
- Vedere /docs/architecture/overview.md per il diagramma di sistema
- Pattern ERP sync: vedere /docs/erp-integration-patterns.md
- Trigger SQL Server: dichiarare sempre DECLARE @var come workaround EF Core 8

## Note importanti
- I trigger su tabelle esistenti conflittono con EF Core SaveChanges se non dichiarati
- RabbitMQ usa porta 5672 — verificare firewall aziendale prima di test
```

---

## 5.5 Conformità Normativa

| Framework | Rilevanza | Azioni richieste |
|---|---|---|
| **GDPR** | Alta — team UE, dati clienti | Classificazione dati, no PII nei prompt, DPA con vendor |
| **EU AI Act** | Media — strumenti rischio limitato | Trasparenza uso AI, documentazione processi |
| **NIST AI RMF** | Riferimento volontario | Adottare le 4 funzioni: Govern, Map, Measure, Manage |
| **ISO 42001** | Opzionale | Certificazione AI management system se richiesta da clienti |

---

[Torna all'indice](../README.md)

---
