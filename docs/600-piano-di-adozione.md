# 6) Piano di Adozione e Roadmap

[Torna all'indice](../README.md)

---

## 6.1 Fasi di Rollout

### Panoramica Timeline

```
MESE 1          MESE 2          MESE 3          MESE 4-6        MESE 6+
│               │               │               │               │
▼               ▼               ▼               ▼               ▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│  PHASE 0    │ │  PHASE 1    │ │  PHASE 2    │ │  PHASE 3    │ │  PHASE 4    │
│  Governance │ │  Pilot      │ │  Claude     │ │  Rollout    │ │  Agents     │
│  & Setup    │ │  Copilot    │ │  Code Intro │ │  Completo   │ │  & Automaz. │
└─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
  Sett 1-2       Sett 3-10       Sett 8-16       Mese 4-6        Mese 6+
```

---

### Phase 0 — Governance & Setup (Settimane 1-2)

**Obiettivo:** Creare le fondamenta prima di toccare il primo strumento.

**Attività:**
- [ ] Redazione e approvazione Acceptable Use Policy
- [ ] Classificazione dati aziendali (Livello 1/2/3)
- [ ] Identificazione 2 AI Champions nel team
- [ ] Configurazione GitHub Copilot Business (SSO, no-training, IP filter)
- [ ] Briefing DPO/CISO sul piano di adozione
- [ ] Raccolta KPI baseline (velocity sprint, bug rate)
- [ ] Setup CLAUDE.md template per i progetti principali

**KPI di completamento:**
- Policy approvata e distribuita al team
- 0 tool AI attivi senza configurazione enterprise
- 2 champions identificati e informati
- Baseline KPI documentata

---

### Phase 1 — Pilot GitHub Copilot (Settimane 3-10)

**Obiettivo:** Validare il ROI su un gruppo controllato prima del rollout completo.

**Setup del pilot:**
- 3-5 sviluppatori volontari (mix di seniority)
- Licenze GitHub Copilot Business attivate
- Workshop iniziale di 2h con AI Champions
- Survey settimanale di 3 domande (NPS, blocchi, best practice)

**Attività settimana per settimana:**

| Settimana | Focus | Output |
|---|---|---|
| 3 | Setup e primi utilizzi — boilerplate, snippet | Prime metriche di adoption rate |
| 4-5 | Code completion quotidiano, documentazione inline | Acceptance rate baseline |
| 6-7 | Unit test e debug assistito | Bug rate su codice AI vs non-AI |
| 8 | Review risultati intermedi con il team | Go/no-go per espansione |
| 9-10 | Affinamento workflow, raccolta lessons learned | Report pilot finale |

**KPI di successo Phase 1:**
- Velocity sprint: target **+20%** sul baseline
- NPS developer per Copilot: **≥ 7/10**
- Bug rate su codice AI-generato: non superiore al baseline
- Almeno 3 case studies documentati dagli AI Champions

> [!NOTE]
> Se il NPS è < 5 o la velocity non migliora dopo 6 settimane, fermarsi e analizzare i blocchi prima di procedere con Phase 2. Il training o la policy potrebbero richiedere aggiustamenti.

---

### Phase 2 — Introduzione Claude Code (Settimane 8-16)

**Obiettivo:** Aggiungere capacità di ragionamento complesso per task architetturali e legacy.

**Nota:** La Phase 2 può iniziare parallelamente alla fine della Phase 1 con i developer senior.

**Attività:**
- [ ] Attivazione licenze Claude Code Team per 3-5 senior developer
- [ ] Creazione CLAUDE.md per i 3 progetti principali
- [ ] Primo task pilota: refactoring legacy identificato come priorità
- [ ] Workshop "quando usare Claude vs Copilot"
- [ ] Configurazione MCP per tool interni (se applicabile)

**Task pilota consigliati per iniziare:**
1. Refactoring di un modulo legacy complesso (multi-file, alta dipendenza)
2. Debug di un problema cronico difficile da riprodurre
3. Migrazione da un pattern architetturale obsoleto
4. Generazione test suite completa per un modulo non coperto

**KPI di successo Phase 2:**
- 3+ refactoring legacy completati con Claude Code
- Riduzione tempo debug su problemi complessi: target **-30%**
- Documentazione dei 5 use case più efficaci per il team

---

### Phase 3 — Rollout Completo (Mese 4-6)

**Obiettivo:** Portare tutta la squadra al regime operativo con entrambi gli strumenti.

**Attività:**
- [ ] Estensione licenze Copilot a tutto il team
- [ ] Workshop training per tutti (basato su lessons learned Phase 1-2)
- [ ] Integrazione checklist AI code review nel processo di PR
- [ ] Aggiornamento pipeline CI/CD per label "ai-assisted" nei commit
- [ ] Review e aggiornamento CLAUDE.md per tutti i progetti attivi
- [ ] Setup dashboard KPI mensile

**KPI di successo Phase 3:**
- **100%** del team in adoption attiva
- ROI ≥ 2× costo licenze (misurabile da velocity e ore risparmiate)
- Tempo onboarding nuovi sviluppatori: **-40%** rispetto al baseline
- Code churn non peggiorato rispetto al pre-AI

---

### Phase 4 — AI Agents & Automazione (Mese 6+)

**Obiettivo:** Sfruttare le capacità agentic per workflow ripetitivi di alto valore.

**Potenziali automazioni:**
- Generazione automatica di documentation su PR merge
- Code review automatica prima della review umana
- Generazione test di regressione su modifiche a moduli critici
- Automazione scrittura stored procedure T-SQL da specifiche
- MCP integration per interrogare documentazione ERP interna

> [!TIP]
> Non affrettare la Phase 4. Gli AI agent in produzione richiedono supervisione e test approfonditi. Iniziare con automazioni a basso rischio (documentazione) prima di agent su codice critico.

---

## 6.2 Budget Dettagliato

### Configurazione raccomandata per 10 sviluppatori

| Voce | Mensile | Annuale | Note |
|---|---|---|---|
| GitHub Copilot Business (10 dev) | $190 / ~€175 | $2.280 / ~€2.100 | Fisso, prevedibile |
| Claude Code Team (5 heavy users) | $150 / ~€138 | $1.800 / ~€1.650 | Senior/Lead developer |
| Training iniziale (ore interne stimate) | — | ~€800 | Una tantum — 2 workshop × 4h |
| **TOTALE PRIMO ANNO** | **~€313/mese** | **~€4.550** | |

### Scenari alternativi

| Scenario | Setup | Costo/anno | Indicato per |
|---|---|---|---|
| **Solo Copilot** | Business (10 dev) | ~€2.100 | Team con budget ridotto, primo approccio |
| **Solo Claude Code** | Team (10 dev) | ~€3.600 | Team con focus su task complessi |
| **Complementare (raccomandato)** | Copilot + Claude Code senior | ~€4.550 | Massima versatilità e copertura |
| **Full Enterprise** | Copilot Enterprise + Claude Max | ~€9.000+ | Grandi team, compliance critica |

### ROI Atteso

| Periodo | ROI Realistico | Condizione |
|---|---|---|
| Mesi 1-3 (Phase 0-1) | 0.5× – 1× | Curva di apprendimento, setup |
| Mesi 4-6 (Phase 2-3) | 1.5× – 2.5× | Adozione consolidata |
| Mese 6+ (regime) | 3× – 5× | Workflow ottimizzati, full team adoption |
| Con AI agents (Phase 4) | 5× – 8× | Automazioni ad alto valore in produzione |

> *Baseline ROI Microsoft Market Study 2025: 3.5× medio su 500+ enterprise con adozione strutturata*

---

## 6.3 Matrice Rischi e Mitigazioni

| # | Rischio | Probabilità | Impatto | Mitigazione |
|---|---|---|---|---|
| 1 | Codice AI in produzione non revisionato | Media | 🔴 Alto | Code review obbligatoria, checklist AI-code, test coverage ≥ 80% |
| 2 | Invio dati sensibili clienti agli strumenti AI | Media | 🔴 Alto | Classificazione dati, Enterprise tier (no-training), policy PII |
| 3 | Dipendenza da vendor (lock-in) | Bassa | 🟡 Medio | Adottare entrambi gli strumenti, monitorare alternative open-source |
| 4 | Junior dev non impara i fondamentali | Alta | 🟡 Medio | Policy: AI solo dopo 3 mesi onboarding base; mentoring obbligatorio |
| 5 | SQL injection in query AI-generate | Media | 🔴 Alto | Review mandatory su query AI, test injection, penetration testing |
| 6 | Overrun budget Claude Code (usage-based) | Media | 🟡 Medio | Cap mensile, alert a 80% budget, monitoraggio usage settimanale |
| 7 | Resistenza al cambiamento del team | Bassa | 🟡 Medio | Champions interni, quick wins visibili, workshop hands-on |
| 8 | Hallucination su librerie .NET specifiche | Alta | 🟢 Basso | Review sempre obbligatoria, package whitelist, test automatici |
| 9 | Privacy breach da CLAUDE.md con dati sensibili | Bassa | 🔴 Alto | Template CLAUDE.md senza dati sensibili, review del file prima del commit |

---

## 6.4 Criteri Go/No-Go per Avanzare tra le Fasi

### Phase 0 → Phase 1
- ✅ Policy approvata e firmata dal management
- ✅ DPO/CISO informati e allineati
- ✅ Almeno 1 AI Champion identificato
- ✅ Configurazione Enterprise attiva e verificata

### Phase 1 → Phase 2
- ✅ NPS developer ≥ 7 su almeno il 60% dei partecipanti al pilot
- ✅ Nessun incidente di sicurezza durante il pilot
- ✅ Almeno 1 case study documentato
- ✅ Checklist AI code review in uso attivo

### Phase 2 → Phase 3
- ✅ Almeno 2 refactoring legacy completati con successo
- ✅ I senior developer hanno CLAUDE.md configurati per i progetti principali
- ✅ Training material pronto per tutto il team

### Phase 3 → Phase 4
- ✅ Adoption rate ≥ 80% del team (uso settimanale attivo)
- ✅ ROI ≥ 1.5× costo licenze misurabile
- ✅ Nessun aumento del bug rate rispetto al baseline

---

> ⚠️[!DISCLAMER]
> *Documento preparato con supporto di analisi AI. Tutti i dati citati sono stati verificati su fonti primarie.*
*Per aggiornamenti, aprire una Issue o una Merge Request in questo repository.*

---

[Torna all'indice](../README.md)

---