# Lab 08 — Strumenti DLP e Backup

## Obiettivo

Scrivere regole regex per rilevare dati sensibili, comprendere il blocco USB e configurare un backup su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

60 minuti

## Prerequisiti

- PC con **Windows 10/11**, **Ubuntu 22.04** o **macOS**
- Browser con accesso a <https://regex101.com/>
- Terminale aperto

## Scenario

L'azienda vi ha chiesto di creare regole DLP per rilevare dati sensibili nelle email, e di configurare un backup per i file importanti.

---

## Step

### Parte A — Regex per DLP (20 min)

**Tutti gli OS (browser — regex101.com)**

**Step 1 — Aprire Regex101**

- Andare su: <https://regex101.com/>
- Selezionare **"Python"** come linguaggio (a sinistra)

**Step 2 — Testare il pattern per carte di credito**

Nel campo "Regular Expression" incollare:

```
\b\d{4}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}\b
```

Nel campo "Test String" incollare:

```
Il numero della carta è 4532-1234-5678-9012 e la prossima scadenza è 12/25.
Altra carta: 5555 6666 7777 8888
Questo non è un numero: 123456789
```

Verificare che solo i numeri di carta di credito vengono evidenziati ✓

**Step 3 — Testare il pattern per il codice fiscale italiano**

Nuovo pattern:

```
\b[A-Z]{6}\d{2}[A-EHLMPR-ST]\d{2}[A-Z]\d{3}[A-Z]\b
```

Test string:

```
Il codice fiscale è RSSMRA85M01H501Z e va verificato.
Questo non è un CF: ABC123
```

**Step 4 — Sfida: creare un pattern per numeri di telefono**

- Formato da rilevare: `+39 333 1234567` oppure `3331234567`
- Provate a scriverlo voi! (suggerimento sotto nelle soluzioni)

---

### Parte B — Simulazione Blocco USB (15 min)

**Windows — Group Policy (dimostrazione)**

```powershell
# Vedere i dispositivi USB collegati
Get-PnpDevice -Class USB | Select Status, FriendlyName

# In azienda, il blocco USB avviene tramite Group Policy:
# gpedit.msc → Computer Configuration → Administrative Templates
# → System → Removable Storage Access
# → "All Removable Storage classes: Deny all access" → Enabled
```

> ⚠️ **NON attivare questa policy** sul PC del lab! È solo per capire come funziona.

**Ubuntu/Linux — Regola udev (dimostrazione)**

```bash
# Vedere i dispositivi USB collegati
lsusb

# In azienda, l'IT crea regole udev come questa:
cat << 'EOF'
# File: /etc/udev/rules.d/99-block-usb-storage.rules
# Questa regola disabilita i dispositivi di storage USB
ACTION=="add", SUBSYSTEMS=="usb", DRIVERS=="usb-storage", ATTR{authorized}="0"
EOF
```

> ⚠️ **NON attivare questa regola** sulla VM del lab! È solo per capire come funziona.

**macOS — Profilo di configurazione (dimostrazione)**

```bash
# Vedere i dispositivi USB collegati
system_profiler SPUSBDataType

# In azienda, il blocco USB su macOS avviene tramite MDM (Mobile Device Management)
# come Jamf o Mosyle, oppure con un profilo di configurazione .mobileconfig
# che disabilita "Removable Media"
```

> Su macOS aziendale, il reparto IT usa profili MDM per bloccare le USB centralmente.

**Concetto chiave:** in tutti e tre gli OS, l'azienda può bloccare le USB a livello centralizzato per prevenire furti di dati.

---

### Parte C — Backup (25 min)

**Step 5 — Creare una cartella con documenti da proteggere**

**Ubuntu/Linux e macOS**

```bash
mkdir -p ~/documenti_importanti
echo "Tesi capitolo 1" > ~/documenti_importanti/tesi_cap1.txt
echo "Appunti lezione" > ~/documenti_importanti/appunti.txt
echo "Progetto finale" > ~/documenti_importanti/progetto.txt
ls ~/documenti_importanti/
```

**Windows (PowerShell)**

```powershell
New-Item -ItemType Directory -Path "$HOME\documenti_importanti" -Force
Set-Content "$HOME\documenti_importanti\tesi_cap1.txt" "Tesi capitolo 1"
Set-Content "$HOME\documenti_importanti\appunti.txt" "Appunti lezione"
Set-Content "$HOME\documenti_importanti\progetto.txt" "Progetto finale"
Get-ChildItem "$HOME\documenti_importanti"
```

---

**Step 6 — Creare il primo backup**

**Ubuntu/Linux e macOS (rsync)**

```bash
mkdir -p ~/backup
rsync -av ~/documenti_importanti/ ~/backup/documenti_importanti/
# -a = archivio (mantiene permessi e date), -v = verbose
```

**Windows (robocopy — integrato)**

```powershell
mkdir "$HOME\backup\documenti_importanti" -Force
robocopy "$HOME\documenti_importanti" "$HOME\backup\documenti_importanti" /MIR /V
# /MIR = mirror (copia e sincronizza), /V = verbose
```

---

**Step 7 — Verificare il backup**

**Ubuntu/Linux e macOS**

```bash
ls ~/backup/documenti_importanti/
diff ~/documenti_importanti/ ~/backup/documenti_importanti/
# Nessun output di diff → i file sono identici ✓
```

**Windows**

```powershell
Get-ChildItem "$HOME\backup\documenti_importanti"
# Confrontare manualmente o:
Compare-Object (Get-Content "$HOME\documenti_importanti\tesi_cap1.txt") (Get-Content "$HOME\backup\documenti_importanti\tesi_cap1.txt")
# Nessun output → identici ✓
```

---

**Step 8 — Backup incrementale (solo i file modificati)**

**Ubuntu/Linux e macOS**

```bash
echo "Aggiunte nuove al capitolo 1" >> ~/documenti_importanti/tesi_cap1.txt
rsync -av ~/documenti_importanti/ ~/backup/documenti_importanti/
# rsync copia SOLO i file modificati → veloce!
```

**Windows**

```powershell
Add-Content "$HOME\documenti_importanti\tesi_cap1.txt" "Aggiunte nuove al capitolo 1"
robocopy "$HOME\documenti_importanti" "$HOME\backup\documenti_importanti" /MIR /V
# robocopy copia SOLO i file modificati
```

---

**Step 9 — Simulare un disastro e ripristinare**

**Ubuntu/Linux e macOS**

```bash
# "Disastro": cancellare un file importante
rm ~/documenti_importanti/progetto.txt
ls ~/documenti_importanti/
# progetto.txt è sparito!

# Ripristinare dal backup
rsync -av ~/backup/documenti_importanti/ ~/documenti_importanti/
ls ~/documenti_importanti/
# progetto.txt è tornato! ✓
```

**Windows**

```powershell
# "Disastro": cancellare un file
Remove-Item "$HOME\documenti_importanti\progetto.txt"
Get-ChildItem "$HOME\documenti_importanti"
# progetto.txt è sparito!

# Ripristinare dal backup
robocopy "$HOME\backup\documenti_importanti" "$HOME\documenti_importanti" /MIR /V
Get-ChildItem "$HOME\documenti_importanti"
# progetto.txt è tornato! ✓
```

---

## Output atteso

- Pattern regex funzionanti per carte di credito e codice fiscale su regex101
- Comprensione del blocco USB (meccanismo diverso per ogni OS)
- Backup creato, verificato, backup incrementale, e ripristino dopo "disastro"

## Checkpoint

- [ ] Regex carta di credito funziona su regex101
- [ ] Regex codice fiscale funziona su regex101
- [ ] Capito il meccanismo di blocco USB per il proprio OS
- [ ] Backup creato (rsync / robocopy)
- [ ] Backup incrementale funzionante (solo file modificati)
- [ ] Ripristino dopo cancellazione riuscito

## Troubleshooting rapido

| Problema | Soluzione |
|----------|----------|
| `rsync: command not found` (Linux) | `sudo apt install rsync` |
| `rsync: command not found` (macOS) | Preinstallato; se manca: `brew install rsync` |
| `robocopy` non trovato (Windows) | È integrato da Windows Vista in poi; aprire cmd o PowerShell come admin |
| Regex non evidenzia nulla | Verificare di aver copiato il pattern correttamente; controllare spazi e maiuscole |
| `diff` mostra differenze | Ripetere `rsync` o `robocopy` per sincronizzare |

## Cleanup obbligatorio

**Ubuntu/Linux e macOS**

```bash
rm -rf ~/documenti_importanti ~/backup
```

**Windows**

```powershell
Remove-Item -Recurse -Force "$HOME\documenti_importanti", "$HOME\backup"
```

## Parole chiave Google (screenshot/guide)

- "regex101 tutorial beginner"
- "regex credit card number pattern"
- "rsync backup tutorial Linux macOS"
- "robocopy backup Windows tutorial"
- "Windows group policy block USB"
- "macOS MDM block USB storage"

---

## Soluzioni

<details>
<summary>Soluzione Step 4: regex per numeri di telefono italiani</summary>

**Pattern:**

```
(?:\+39[\s-]?)?3\d{2}[\s-]?\d{6,7}
```

**Spiegazione:**

- `(?:\+39[\s-]?)?` → prefisso internazionale opzionale (+39, seguito opzionalmente da spazio o trattino)
- `3\d{2}` → le prime 3 cifre iniziano con "3" (numeri mobili italiani: 3xx)
- `[\s-]?` → spazio o trattino opzionale
- `\d{6,7}` → le restanti 6 o 7 cifre

**Test string per verificare:**

```
Chiamami al +39 333 1234567 oppure al 3331234567.
Il numero fisso 02 12345678 NON deve essere evidenziato.
Altro cellulare: 347-9876543
```

**Risultato atteso:** evidenziati solo `+39 333 1234567`, `3331234567` e `347-9876543`.

</details>

<details>
<summary>Soluzione Step 6-9: comandi backup completi per ogni OS</summary>

**Ubuntu/Linux e macOS — rsync**

```bash
# Primo backup
rsync -av ~/documenti_importanti/ ~/backup/documenti_importanti/

# Backup incrementale (dopo modifiche)
rsync -av ~/documenti_importanti/ ~/backup/documenti_importanti/

# Ripristino
rsync -av ~/backup/documenti_importanti/ ~/documenti_importanti/
```

Opzioni utili di rsync:

- `-a` = archivio (mantiene permessi, date, link simbolici)
- `-v` = verbose (mostra cosa copia)
- `--delete` = rimuove dal backup i file cancellati nella sorgente
- `--dry-run` = simula senza copiare (utile per verificare)

**Windows — robocopy**

```powershell
# Primo backup
robocopy "$HOME\documenti_importanti" "$HOME\backup\documenti_importanti" /MIR /V

# Backup incrementale (dopo modifiche) — stesso comando!
robocopy "$HOME\documenti_importanti" "$HOME\backup\documenti_importanti" /MIR /V

# Ripristino (invertire sorgente e destinazione)
robocopy "$HOME\backup\documenti_importanti" "$HOME\documenti_importanti" /MIR /V
```

Opzioni utili di robocopy:

- `/MIR` = mirror (copia + cancella file non più nella sorgente)
- `/V` = verbose
- `/L` = list only (simula senza copiare, come `--dry-run`)
- `/LOG:file.log` = salva il log su file

</details>

<details>
<summary>Soluzione Parte B: confronto meccanismi blocco USB per OS</summary>

| OS | Strumento | Chi lo gestisce | Come funziona |
|----|-----------|----------------|---------------|
| Windows | Group Policy (gpedit.msc) o MDM (Intune) | Amministratore IT | Policy centralizzata che disabilita classi di dispositivi removibili |
| Ubuntu/Linux | Regole udev | Amministratore di sistema | Regola in `/etc/udev/rules.d/` che intercetta l'aggiunta di dispositivi USB storage |
| macOS | Profili di configurazione MDM (Jamf, Mosyle) | Amministratore IT | Profilo `.mobileconfig` distribuito centralmente che disabilita media rimovibili |

**Perché bloccare le USB?**

- Prevenire il furto di dati (un dipendente copia file riservati su chiavetta)
- Prevenire l'infezione da malware (una chiavetta infetta inserita nel PC)
- Conformità aziendale e normative (GDPR, ISO 27001)

</details>
