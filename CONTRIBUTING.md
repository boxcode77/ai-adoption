# Come Contribuire a Questo Documento

Questo repository contiene documentazione strategica sul progetto di adozione degli AI tools. I contributi del team sono benvenuti e incoraggiati.

---

## Tipi di Contributo

| Tipo | Etichetta Issue/MR | Chi può farlo |
|---|---|---|
| Correzione dati o fonti | `fix/data` | Chiunque |
| Aggiornamento a nuova versione strumenti | `update/tools` | AI Champions |
| Aggiunta voce al glossario | `docs/glossary` | Chiunque |
| Nuova sezione o documento | `feature/docs` | Dev Lead + AI Champions |
| Modifica alla governance policy | `policy/change` | Dev Lead + CISO |

---

## Workflow per le Modifiche

### Per correzioni minori (typo, link rotto, dato aggiornato)

1. Aprire una **Issue** descrivendo la correzione
2. Creare un branch: `fix/descrizione-breve`
3. Fare il commit con il formato: `fix(docs): [descrizione]`
4. Aprire una **Merge Request / Pull Request**

### Per modifiche sostanziali (nuova sezione, cambio policy)

1. Aprire una **Issue** con label `discussion` per raccogliere feedback prima di scrivere
2. Attendere feedback da Dev Lead o AI Champions (minimo 3 giorni lavorativi)
3. Creare branch: `feature/nome-funzionalita`
4. Scrivere la modifica e aprire MR con descrizione dettagliata delle motivazioni
5. Review obbligatoria da almeno 1 AI Champion e Dev Lead

---

## Convenzioni di Scrittura

### Markdown

- **Titoli:** H1 per il titolo pagina, H2 per sezioni principali, H3 per sotto-sezioni
- **Tabelle:** usare sempre header row, allineare le colonne
- **Callout:** usare la sintassi GitHub/GitLab Flavored Markdown:
  ```markdown
  > [!NOTE]      → informazione aggiuntiva
  > [!TIP]       → consiglio pratico
  > [!WARNING]   → attenzione, impatto medio
  > [!IMPORTANT] → critico, da non ignorare
  > [!CAUTION]   → pericolo, impatto alto
  ```
- **Codice:** sempre con syntax highlighting (` ```yaml `, ` ```csharp `, ` ```bash `)
- **Link interni:** path relativo dalla cartella corrente: `[testo](../README.md)` o `[testo](altro-file.md)`

### Aggiornamento dati

Quando si aggiorna un dato (es. nuova percentuale di adozione, nuovo prezzo), includere sempre:
- Il nuovo valore
- La fonte (nome + anno)
- La data di aggiornamento nel commit message

Esempio: `fix(data): aggiorna adozione Copilot al 72% (Stack Overflow 2026)`

---

## Struttura dei Commit

Seguire il formato **Conventional Commits**:

```
tipo(ambito): descrizione breve

[corpo opzionale con dettagli]

[footer opzionale con riferimenti issue]
```

Tipi accettati: `feat` · `fix` · `docs` · `style` · `refactor` · `chore`

Esempi:
```
docs(glossary): aggiunge voce "Vector Database"
fix(data): corregge SWE-bench score Claude Opus 4.5 (80.9% → corretto)
feat(docs): aggiunge sezione su Microsoft Copilot Studio
chore: aggiorna link a documentazione Anthropic API
```

---

## Segnalare un'Issue

Quando apri una Issue, includi:

```markdown
## Tipo
[ ] Dato errato / outdated
[ ] Link rotto
[ ] Voce glossario mancante
[ ] Nuova sezione proposta
[ ] Altro

## Descrizione
[Descrivi chiaramente il problema o la proposta]

## Dove si trova
File: docs/XX-nome-file.md
Sezione: [titolo sezione]

## Fonte o riferimento
[Se è un aggiornamento di dato, includi la fonte]
```

---

*Per domande urgenti, contattare direttamente gli AI Champions del team.*
