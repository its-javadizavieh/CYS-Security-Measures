# Lab 07 — Classificazione Dati e Permessi di Accesso

## Obiettivo

Creare una matrice di classificazione dei dati e configurare i permessi di file/cartelle su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

60 minuti

## Prerequisiti

- PC con **Windows 10/11**, **Ubuntu 22.04** o **macOS**
- Accesso amministratore / sudo / admin

## Scenario

L'azienda "SecureTech SRL" deve classificare i propri dati e configurare i permessi di accesso per proteggere le informazioni riservate.

---

## Step

### Parte A — Classificazione dei Dati (15 min)

**Step 1 — Comprendere i livelli**

| Livello | Etichetta | Chi può accedere | Esempio |
|---------|-----------|-----------------|---------|
| 1 | **Pubblico** | Tutti | Brochure, sito web |
| 2 | **Interno** | Solo dipendenti | Procedure, organigramma |
| 3 | **Riservato** | Solo il team specifico | Contratti clienti, piani commerciali |
| 4 | **Strett. riservato** | Solo persone autorizzate per nome | Password, dati sanitari, buste paga |

**Step 2 — Esercizio: classificare questi documenti**

Compilate la tabella assegnando il livello (1-4):

| Documento | Livello | Perché |
|-----------|---------|--------|
| Listino prezzi clienti | _____ | _____ |
| Buste paga dipendenti | _____ | _____ |
| Comunicato stampa | _____ | _____ |
| File con password dei server | _____ | _____ |
| Manuale utente del software | _____ | _____ |

> Discutete le risposte con il compagno di banco!

---

### Parte B — Creare cartelle e file di test (10 min)

**Windows (PowerShell come Amministratore)**

```powershell
New-Item -ItemType Directory -Path "C:\SecureTech\Pubblico" -Force
New-Item -ItemType Directory -Path "C:\SecureTech\Interno" -Force
New-Item -ItemType Directory -Path "C:\SecureTech\Riservato" -Force

Set-Content "C:\SecureTech\Pubblico\brochure.txt" "Benvenuti in SecureTech!"
Set-Content "C:\SecureTech\Interno\procedura.txt" "Backup giornaliero ore 02:00"
Set-Content "C:\SecureTech\Riservato\contratto.txt" "Importo: 150.000 EUR"
```

**Ubuntu/Linux**

```bash
mkdir -p ~/SecureTech/{pubblico,interno,riservato}
echo "Brochure pubblica" > ~/SecureTech/pubblico/brochure.txt
echo "Procedura interna" > ~/SecureTech/interno/procedura.txt
echo "Contratto riservato" > ~/SecureTech/riservato/contratto.txt
```

**macOS**

```bash
mkdir -p ~/SecureTech/{pubblico,interno,riservato}
echo "Brochure pubblica" > ~/SecureTech/pubblico/brochure.txt
echo "Procedura interna" > ~/SecureTech/interno/procedura.txt
echo "Contratto riservato" > ~/SecureTech/riservato/contratto.txt
```

---

### Parte C — Configurare i permessi (20 min)

#### Windows — Permessi NTFS

**Step 3 — Modificare i permessi dalla GUI**

1. Tasto destro sulla cartella `C:\SecureTech\Riservato` → **Proprietà** → **Sicurezza** → **Modifica**
2. Selezionare il gruppo **"Users"** → cliccare **"Rimuovi"**
3. Solo il gruppo "Administrators" rimane → **Applica** → **OK**

**Step 4 — Verificare da PowerShell**

```powershell
Get-Acl "C:\SecureTech\Riservato" | Format-List

# Provare ad accedere con un account standard:
# Start → Esegui → runas /user:studente_test cmd
# cd C:\SecureTech\Riservato
# → "Accesso negato" ✓
```

---

#### Ubuntu/Linux — Permessi POSIX (chmod)

**Step 3 — Vedere i permessi attuali**

```bash
ls -la ~/SecureTech/riservato/
# Mostra qualcosa come: -rw-rw-r-- (leggibile da tutti)
```

**Step 4 — Restringere i permessi**

```bash
chmod 700 ~/SecureTech/riservato/
ls -la ~/SecureTech/ | grep riservato
# Ora mostra: drwx------ (solo il proprietario può accedere)
```

**Step 5 — Testare l'accesso con un altro utente**

```bash
sudo adduser --disabled-password --gecos "" utente_test
sudo -u utente_test cat ~/SecureTech/riservato/contratto.txt
# Output: Permission denied ✓
```

---

#### macOS — Permessi POSIX + ACL

**Step 3 — Vedere i permessi attuali**

```bash
ls -la ~/SecureTech/riservato/
```

**Step 4 — Restringere i permessi**

```bash
chmod 700 ~/SecureTech/riservato/
ls -la ~/SecureTech/ | grep riservato
# Ora mostra: drwx------ (solo il proprietario)
```

**Step 5 — Testare l'accesso (metodo alternativo, senza creare utente)**

```bash
# macOS: verificare le ACL
ls -le ~/SecureTech/riservato/

# Aggiungere un'ACL di deny per "everyone"
chmod +a "everyone deny read,write,execute" ~/SecureTech/riservato/
ls -le ~/SecureTech/riservato/
# Mostra la ACL di deny ✓
```

Oppure dalla GUI:

1. Tasto destro sulla cartella → **Ottieni informazioni** (Cmd+I)
2. Sezione **Condivisione e permessi** → cliccare il lucchetto per sbloccare
3. Impostare "everyone" su **"Nessun accesso"**

---

### Parte D — Sfida: capire i numeri dei permessi (15 min)

```
chmod 755 = rwxr-xr-x  (proprietario: tutto, altri: leggono ed eseguono)
chmod 700 = rwx------   (proprietario: tutto, altri: niente)
chmod 644 = rw-r--r--   (proprietario: legge/scrive, altri: solo leggono)
chmod 600 = rw-------   (proprietario: legge/scrive, altri: niente)
```

**Esercizio:** quale permesso usereste per questi file? (su Linux/macOS)

| File | Permesso consigliato | Perché |
|------|---------------------|--------|
| Chiave SSH privata (`id_rsa`) | _____ | _____ |
| Script eseguibile condiviso | _____ | _____ |
| File di log leggibile da tutti | _____ | _____ |

---

## Output atteso

- Matrice di classificazione compilata
- Cartelle con permessi differenziati sul proprio OS
- Accesso negato verificato per gli utenti non autorizzati

## Checkpoint

- [ ] Tabella di classificazione compilata per tutti i 5 documenti
- [ ] Cartelle SecureTech create
- [ ] Permessi restrittivi sulla cartella Riservato
- [ ] Accesso negato verificato (con altro utente o ACL)
- [ ] Esercizio numeri permessi completato

## Troubleshooting rapido

| Problema | Soluzione |
|----------|----------|
| Non riesco a modificare i permessi Windows | Verificare di essere admin; tasto destro → "Esegui come amministratore" |
| `chmod: operation not permitted` (Linux) | Usare `sudo chmod` |
| `adduser: command not found` (Linux) | `sudo apt install adduser` |
| macOS: ACL non si applica | Sbloccare il lucchetto in "Ottieni informazioni" o usare `sudo` nel terminale |
| macOS: non posso creare utente da terminale | Usare Impostazioni di Sistema → Utenti e Gruppi → "+" |

## Cleanup obbligatorio

**Windows (PowerShell)**

```powershell
Remove-Item -Recurse -Force "C:\SecureTech"
```

**Ubuntu/Linux**

```bash
rm -rf ~/SecureTech
sudo deluser --remove-home utente_test 2>/dev/null
```

**macOS**

```bash
rm -rf ~/SecureTech
```

## Parole chiave Google (screenshot/guide)

- "Windows folder permissions change security tab"
- "Linux chmod permissions explained"
- "macOS file permissions terminal"
- "data classification levels example"

---

## Soluzioni

<details>
<summary>Soluzione Step 2: classificazione documenti</summary>

| Documento | Livello | Perché |
|-----------|---------|--------|
| Listino prezzi clienti | **3 — Riservato** | Contiene informazioni commerciali che i concorrenti potrebbero sfruttare |
| Buste paga dipendenti | **4 — Strett. riservato** | Dati personali sensibili protetti dal GDPR |
| Comunicato stampa | **1 — Pubblico** | Creato appositamente per essere diffuso al pubblico |
| File con password dei server | **4 — Strett. riservato** | Accesso solo per gli admin autorizzati; compromissione = accesso totale ai sistemi |
| Manuale utente del software | **2 — Interno** | Utile per i dipendenti, ma non sensibile; non va diffuso per evitare di rivelare dettagli tecnici |

> Nota: la classificazione può variare in base al contesto aziendale. L'importante è che ogni documento abbia un livello assegnato e che le regole di accesso siano coerenti.

</details>

<details>
<summary>Soluzione Parte C: verifica permessi per ogni OS</summary>

**Windows**

```powershell
Get-Acl "C:\SecureTech\Riservato" | Format-List
```

Output atteso (semplificato):

```
Path   : C:\SecureTech\Riservato
Owner  : BUILTIN\Administrators
Access : BUILTIN\Administrators Allow  FullControl
         # "Users" NON deve comparire
```

**Ubuntu/Linux**

```bash
ls -la ~/SecureTech/ | grep riservato
```

Output atteso:

```
drwx------  2 studente studente  4096 feb 22 10:00 riservato
```

- `drwx------` → solo il proprietario ha accesso (700)
- Test: `sudo -u utente_test ls ~/SecureTech/riservato/` → "Permission denied"

**macOS**

```bash
ls -la ~/SecureTech/ | grep riservato
```

Output atteso (stesso formato di Linux):

```
drwx------  3 studente  staff  96 feb 22 10:00 riservato
```

Con ACL:

```bash
ls -le ~/SecureTech/riservato/
# 0: everyone deny read,write,execute
```

</details>

<details>
<summary>Soluzione Parte D: esercizio numeri permessi</summary>

| File | Permesso | Perché |
|------|----------|--------|
| Chiave SSH privata (`id_rsa`) | **600** (`rw-------`) | La chiave privata deve essere leggibile SOLO dal proprietario. SSH rifiuta chiavi con permessi troppo aperti! |
| Script eseguibile condiviso | **755** (`rwxr-xr-x`) | Il proprietario può tutto; gli altri possono leggere e eseguire lo script, ma non modificarlo |
| File di log leggibile da tutti | **644** (`rw-r--r--`) | Il proprietario (sysadmin) può scrivere; tutti gli altri possono solo leggere per consultazione |

**Trucco per ricordare:**

- **7** = rwx (lettura + scrittura + esecuzione) = 4+2+1
- **6** = rw- (lettura + scrittura) = 4+2
- **5** = r-x (lettura + esecuzione) = 4+1
- **4** = r-- (solo lettura) = 4
- **0** = --- (nessun accesso) = 0

</details>
