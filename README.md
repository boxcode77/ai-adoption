# Adozione degli Strumenti AI nel Team di Sviluppo

> Documento strategico per l'introduzione, regolamentazione e gestione degli strumenti AI generativi nel team di sviluppo.

## Sintesi

L'adozione degli strumenti AI per lo sviluppo software è in rapida crescita: l'**84% degli sviluppatori** usa o prevede di usare AI tools, il **51%** li usa già quotidianamente ([Stack Overflow Dev Survey 2025](https://survey.stackoverflow.co/2025/ai#1-ai-tools-in-the-development-process)). Questa diffusione avviene spesso in modo non governato, con rischi concreti per la sicurezza dei dati, la qualità del codice e la conformità normativa.

Questo documento analizza:

- **Dove** gli strumenti AI portano valore reale nel workflow di sviluppo
- **Quali** strumenti adottare e come si differenziano (Claude Code vs GitHub Copilot)
- **Quali rischi** comporta un uso non governato e come mitigarli
- **Come** strutturare un piano di adozione graduale con governance adeguata

### Raccomandazione

> [!IMPORTANT]
> L'adozione non governata degli AI tools può produrre effetti opposti a quelli attesi. La chiave è **adozione strutturata con governance chiara**: definire policy, tool approvati e processi di review *prima* del rollout, non dopo.


## Indice

- [Introduzione](docs/000-introduzione.md) — Contesto, modalità di utilizzo degli agent AI, obiettivi del documento

**Capitoli**

- [1. Aree di Utilizzo](docs/100-utilizzo.md)
  - 1.1 Su cosa sono veramente bravi gli agenti AI?
  - 1.2 Aree di utilizzo per ruolo e tipo di task
- [2. Analisi Comparativa — Claude Code vs GitHub Copilot](docs/200-comparativa.md)
  - 2.1 Differenze Fondamentali di Paradigma
  - 2.2 Dove GitHub Copilot è Più Forte
  - 2.3 Dove Claude Code è Più Forte
  - 2.4 Limiti di Entrambi
  - 2.5 Matrice di Selezione per Caso d'Uso
  - 2.6 Confronto Prezzi Dettagliato (Feb 2026)
  - 2.7 Pro Tips dalla Community
- [3. Catalogo dei Rischi](docs/300-rischi.md) — 23 rischi classificati per categoria, probabilità e impatto
  - 3.1 Esposizione dei Dati
  - 3.2 Sicurezza del Codice
  - 3.3 Rischi legati agli AI Agent
  - 3.4 Rischi Organizzativi
  - 3.5 Qualità e Affidabilità
  - 3.6 Normativi e Legali
- [4. Pattern di Vulnerabilità nel Codice AI-Generato](docs/400-potenziali-bugs.md) — 10 vulnerabilità ricorrenti con esempi e metodi di rilevazione
  - 4.1 SQL Injection · 4.2 XSS · 4.3 Log Injection · 4.4 Path Traversal
  - 4.5 Credenziali Hardcoded · 4.6 Information Disclosure · 4.7 Token Insicuri
  - 4.8 SSRF · 4.9 Missing Auth/Authorization · 4.10 Insecure Deserialization
- [5. Governance e Policy](docs/500-governance.md)
  - 5.1 Stato della Governance AI nelle Organizzazioni
  - 5.2 Framework di Governance — 6 Componenti
  - 5.3 RACI Matrix per l'Adozione AI Tools
  - 5.4 Configurazioni Tecniche Raccomandate
  - 5.5 Conformità Normativa
- [6. Piano di Adozione](docs/600-piano-di-adozione.md) — Roadmap a fasi, budget, KPI, gestione del cambiamento
  - 6.1 Fasi di Rollout
  - 6.2 Budget Dettagliato
  - 6.3 Matrice Rischi e Mitigazioni
  - 6.4 Criteri Go/No-Go per Avanzare tra le Fasi
- [7. Conclusioni e Action Items](docs/700-conclusioni.md)
  - 7.1 Raccomandazione Finale
  - 7.2 Action Items Immediati
  - 7.3 Decisioni da Prendere in Board
  - 7.4 Sintesi Comparativa Finale
  - 7.5 Segnali di Allerta da Monitorare
  - 7.6 Fonti e Riferimenti

**Allegati**

- [Allegato 1 — Analisi Produttività](docs/1000-analisi-produttivita.md)
  - A.1 Dati di Produttività apparente
  - A.2 Il Paradosso della Produttività
  - A.3 Impatto per Ruolo nel Team
  - A.4 Aree Dove l'AI è Più Forte
  - A.5 Calcolo ROI per il Team
- [Allegato 2 — Best Practices GitHub Copilot](docs/1100-best-practices-copilot.md) — Configurazione, .copilotignore, prompt efficaci, policy enterprise
- [Allegato 3 — Best Practices Claude Code](docs/1200-best-practices-claude-code.md) — Configurazione, CLAUDE.md, .claudeignore, MCP, permessi, prompt efficaci

**Riferimenti**

- [Glossario](docs/glossary.md) — Acronimi, tecnicismi e definizioni contestualizzate (59 voci)

---

## Struttura del Repository

```

README.md                               ← Sei qui — indice e sintesi
CONTRIBUTING.md                         ← Indicazioni per il contributo alla modifica di questo documento
docs/
  ├── 000-introduzione.md               ← Contesto e obiettivi
  ├── 100-utilizzo.md                   ← Aree di utilizzo e casi d'uso
  ├── 200-comparativa.md                ← Claude Code vs GitHub Copilot
  ├── 300-rischi.md                     ← Catalogo dei rischi (23 rischi)
  ├── 310-potenziali-bugs.md            ← Vulnerabilità nel codice AI-generato
  ├── 400-governance.md                 ← Governance e policy aziendale
  ├── 500-piano-di-adozione.md          ← Roadmap e piano di adozione
  ├── 900-conclusioni.md                ← Conclusioni e action items
  ├── 1000-analisi-produttivita.md      ← Annex 1: dati di produttività
  ├── 1100-best-practices-copilot.md    ← Annex 2: best practices GitHub Copilot
  ├── 1200-best-practices-claude-code.md ← Annex 3: best practices Claude Code
  └── glossary.md                       ← Glossario tecnico
```

---

*Documento preparato con supporto di analisi AI. Tutti i dati citati sono stati verificati su fonti primarie.*
