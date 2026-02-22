# Lab 09 — Manutenzione e Smaltimento Endpoint

## Obiettivo

Configurare un backup, verificare il ripristino e praticare la cancellazione sicura dei dati su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

60 minuti

## Prerequisiti

- PC con **Windows 10/11**, **Ubuntu 22.04** o **macOS**
- Terminale / PowerShell aperto con accesso admin/sudo

## Scenario

Un vecchio PC della scuola deve essere dismesso. Dovete prima fare un backup dei dati importanti, poi cancellare in modo sicuro tutti i dati dal disco prima di consegnarlo al riciclaggio.

---

## Step

### Parte A — Backup e Ripristino (25 min)

**Step 1 — Creare dei file da proteggere**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
mkdir -p ~/dati_scuola
echo "Registro presenze - classe 3A" > ~/dati_scuola/registro.txt
echo "Voti primo quadrimestre" > ~/dati_scuola/voti.txt
echo "Programmazione annuale" > ~/dati_scuola/programmazione.txt
ls ~/dati_scuola/
```

**🪟 Windows (PowerShell)**

```powershell
New-Item -ItemType Directory -Path "$HOME\dati_scuola" -Force
Set-Content "$HOME\dati_scuola\registro.txt" "Registro presenze - classe 3A"
Set-Content "$HOME\dati_scuola\voti.txt" "Voti primo quadrimestre"
Set-Content "$HOME\dati_scuola\programmazione.txt" "Programmazione annuale"
Get-ChildItem "$HOME\dati_scuola"
```

---

**Step 2 — Fare il backup**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
mkdir -p ~/backup_scuola
rsync -av ~/dati_scuola/ ~/backup_scuola/
ls ~/backup_scuola/
```

**🪟 Windows**

```powershell
mkdir "$HOME\backup_scuola" -Force
robocopy "$HOME\dati_scuola" "$HOME\backup_scuola" /MIR /V
Get-ChildItem "$HOME\backup_scuola"
```

---

**Step 3 — Verificare il backup**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
diff -r ~/dati_scuola/ ~/backup_scuola/
# Nessun output → le cartelle sono identiche ✓
```

**🪟 Windows**

```powershell
$original = Get-ChildItem "$HOME\dati_scuola" | ForEach-Object { $_.Name }
$backup = Get-ChildItem "$HOME\backup_scuola" | ForEach-Object { $_.Name }
Compare-Object $original $backup
# Nessun output → identici ✓
```

---

**Step 4 — Simulare un problema e ripristinare**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
# "Disastro": cancellare i file
rm -rf ~/dati_scuola/*
ls ~/dati_scuola/
# Vuoto!

# Ripristinare dal backup
rsync -av ~/backup_scuola/ ~/dati_scuola/
cat ~/dati_scuola/registro.txt
# Tutto ripristinato! ✓
```

**🪟 Windows**

```powershell
# "Disastro": cancellare i file
Remove-Item "$HOME\dati_scuola\*" -Force
Get-ChildItem "$HOME\dati_scuola"
# Vuoto!

# Ripristinare dal backup
robocopy "$HOME\backup_scuola" "$HOME\dati_scuola" /MIR /V
Get-Content "$HOME\dati_scuola\registro.txt"
# Tutto ripristinato! ✓
```

---

### Parte B — Cancellazione Sicura (20 min)

**Step 5 — Capire perché "Elimina" non basta**

> Quando "eliminate" un file normalmente (Cestino → Svuota), il sistema operativo rimuove solo il **riferimento** al file. I dati restano sul disco finché non vengono sovrascritti! Questo significa che con tool appositi (Recuva, photorec) i file possono essere **recuperati**.

---

**Step 6 — Cancellazione sicura**

**🐧 Ubuntu/Linux — shred**

```bash
# Creare un file segreto
echo "Password del server: SuperSecreta2026!" > ~/file_segreto.txt
cat ~/file_segreto.txt

# Cancellare in modo sicuro con shred
shred -vfz -n 3 ~/file_segreto.txt
# -v: mostra il progresso
# -f: forza la scrittura
# -z: sovrascrive con zeri alla fine
# -n 3: tre passaggi di sovrascrittura

cat ~/file_segreto.txt
# Solo caratteri incomprensibili!

rm ~/file_segreto.txt
```

**🍎 macOS — rm -P (o gshred)**

```bash
# Creare un file segreto
echo "Password del server: SuperSecreta2026!" > ~/file_segreto.txt
cat ~/file_segreto.txt

# Metodo 1: rm con sovrascrittura (disponibile su macOS più vecchi)
rm -P ~/file_segreto.txt
# -P: sovrascrive 3 volte prima di cancellare

# Metodo 2: installare gshred (più affidabile)
brew install coreutils
echo "Password del server: SuperSecreta2026!" > ~/file_segreto.txt
gshred -vfz -n 3 ~/file_segreto.txt
rm ~/file_segreto.txt
```

> **Nota su SSD e macOS:** i Mac moderni con SSD usano TRIM e hanno il T2/M1/M2 chip con cifratura hardware. Per la dismissione completa, il metodo migliore è: FileVault (già visto nel Lab 05) + "Inizializza tutti i contenuti e le impostazioni" da Impostazioni di Sistema.

**🪟 Windows — cipher /w (o SDelete)**

```powershell
# Creare un file segreto
Set-Content "$HOME\file_segreto.txt" "Password del server: SuperSecreta2026!"
Get-Content "$HOME\file_segreto.txt"

# Eliminare normalmente
Remove-Item "$HOME\file_segreto.txt"

# Sovrascrivere lo spazio libero dove si trovava il file
cipher /w:"$HOME"
# cipher /w sovrascrive lo spazio libero 3 volte (può richiedere tempo)
```

Per cancellazione sicura di file specifici, scaricare **SDelete** (Microsoft Sysinternals):

```powershell
# Scaricare SDelete da Microsoft
# https://learn.microsoft.com/en-us/sysinternals/downloads/sdelete

# Uso:
# sdelete -p 3 "$HOME\file_segreto.txt"
# -p 3 = tre passaggi di sovrascrittura
```

---

**Step 7 — Cancellazione sicura di una cartella intera**

**🐧 Ubuntu/Linux**

```bash
mkdir ~/cartella_segreta
echo "File riservato 1" > ~/cartella_segreta/file1.txt
echo "File riservato 2" > ~/cartella_segreta/file2.txt

find ~/cartella_segreta -type f -exec shred -vfz -n 3 {} \;
rm -rf ~/cartella_segreta
```

**🍎 macOS**

```bash
mkdir ~/cartella_segreta
echo "File riservato 1" > ~/cartella_segreta/file1.txt
echo "File riservato 2" > ~/cartella_segreta/file2.txt

find ~/cartella_segreta -type f -exec gshred -vfz -n 3 {} \;
rm -rf ~/cartella_segreta
```

**🪟 Windows**

```powershell
New-Item -ItemType Directory -Path "$HOME\cartella_segreta" -Force
Set-Content "$HOME\cartella_segreta\file1.txt" "File riservato 1"
Set-Content "$HOME\cartella_segreta\file2.txt" "File riservato 2"

# Eliminare i file
Remove-Item "$HOME\cartella_segreta" -Recurse -Force

# Sovrascrivere lo spazio libero
cipher /w:"$HOME"
```

---

### Parte C — Certificato di Dismissione (15 min)

**Step 8 — Compilare un certificato di dismissione**

Compilate questo modulo (su carta o in un file di testo):

```
=== CERTIFICATO DI DISMISSIONE DISPOSITIVO ===

Data: ________
Responsabile: ________

Dispositivo:
- Tipo: ________ (es. laptop, desktop, server)
- Marca/Modello: ________
- Numero di serie: ________
- Sistema operativo: ________ (Windows / Ubuntu / macOS)

Dati:
- Classificazione dati presenti: ________ (Pubblico/Interno/Riservato/Strett. riservato)
- Backup effettuato: SI / NO
- Destinazione backup: ________

Sanitizzazione:
- Metodo usato: ________ (es. shred, cipher/w, SDelete, DBAN, Inizializza macOS,
                          distruzione fisica)
- Numero passaggi: ________
- Verifica completata: SI / NO

Destinazione dispositivo:
- [ ] Riciclaggio (RAEE)
- [ ] Riutilizzo interno
- [ ] Vendita/donazione
- [ ] Distruzione fisica

Firma responsabile: ________
```

---

**Step 9 — Sfida: quando usare quale metodo?**

| Situazione | Metodo consigliato | OS |
|-----------|-------------------|-----|
| Cancellare pochi file riservati | _____ | _____ |
| Dismissione completa di un PC | _____ | _____ |
| Dati top-secret / militari | _____ | _____ |
| PC con disco cifrato (BitLocker/FileVault/LUKS) | _____ | _____ |

---

## Output atteso

- Backup creato e ripristinato con successo su tutti gli OS
- Cancellazione sicura eseguita con lo strumento appropriato per il proprio OS
- Certificato di dismissione compilato

## Checkpoint

- [ ] Backup completato (rsync o robocopy)
- [ ] Ripristino dopo "disastro" riuscito
- [ ] Cancellazione sicura eseguita (shred / gshred / cipher /w)
- [ ] Differenza tra "elimina" e "cancellazione sicura" compresa
- [ ] Certificato di dismissione compilato
- [ ] Tabella "quale metodo usare" completata

## Troubleshooting rapido

| Problema | Soluzione |
|----------|----------|
| `shred: command not found` (Linux) | `sudo apt install coreutils` (quasi sempre già installato) |
| `gshred: command not found` (macOS) | `brew install coreutils` |
| `rm -P` non funziona (macOS recente) | Apple ha rimosso `-P` sulle versioni più recenti; usare `gshred` |
| `rsync: command not found` | Linux: `sudo apt install rsync`; macOS: preinstallato |
| `cipher /w` impiega molto tempo | È normale — sovrascrive TUTTO lo spazio libero del disco |
| shred non funziona bene su SSD | Su SSD usare la cifratura (BitLocker/FileVault/LUKS) + wipe; shred è progettato per HDD |

## Cleanup obbligatorio

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
rm -rf ~/dati_scuola ~/backup_scuola
rm -f ~/file_segreto.txt
rm -rf ~/cartella_segreta
```

**🪟 Windows**

```powershell
Remove-Item -Recurse -Force "$HOME\dati_scuola", "$HOME\backup_scuola" -ErrorAction SilentlyContinue
Remove-Item "$HOME\file_segreto.txt" -ErrorAction SilentlyContinue
Remove-Item -Recurse -Force "$HOME\cartella_segreta" -ErrorAction SilentlyContinue
```

## Parole chiave Google (screenshot/guide)

- "shred command Linux tutorial"
- "macOS secure erase file gshred"
- "Windows cipher /w secure delete"
- "SDelete Microsoft Sysinternals"
- "rsync backup restore"
- "robocopy backup Windows"
- "IT asset disposal certificate template"

---

## Soluzioni

<details>
<summary>Soluzione Step 6: confronto strumenti cancellazione sicura per OS</summary>

| OS | Strumento | Comando | Tipo |
|----|-----------|---------|------|
| 🐧 Ubuntu/Linux | `shred` | `shred -vfz -n 3 file.txt` | File singolo; sovrascrive con dati random + zeri |
| 🍎 macOS | `gshred` (via coreutils) | `gshred -vfz -n 3 file.txt` | Identico a shred di Linux |
| 🍎 macOS (alternativa) | Inizializza Mac | Impostazioni → Generali → Trasferisci o inizializza | Wipe completo del dispositivo |
| 🪟 Windows | `cipher /w` | `cipher /w:C:\` | Sovrascrive spazio libero (non file specifici) |
| 🪟 Windows | `SDelete` (Sysinternals) | `sdelete -p 3 file.txt` | File singolo |
| Tutti | DBAN (Darik's Boot and Nuke) | Boot da USB → wipe intero disco | Dismissione completa |

**Nota importante per SSD:**
Gli SSD gestiscono la scrittura in modo diverso dagli HDD (wear leveling, TRIM). Per gli SSD il metodo più sicuro è:

1. **Cifrare** il disco (BitLocker/FileVault/LUKS)
2. **Cancellare la chiave** di cifratura
3. I dati cifrati rimangono ma sono **illeggibili** senza chiave

</details>

<details>
<summary>Soluzione Step 4: verifica ripristino completa</summary>

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
# Dopo il ripristino, verificare:
diff -r ~/dati_scuola/ ~/backup_scuola/
# Nessun output → identici ✓

# Controllare che tutti i file siano presenti
ls -la ~/dati_scuola/
# registro.txt, voti.txt, programmazione.txt

# Verificare il contenuto
cat ~/dati_scuola/registro.txt
# "Registro presenze - classe 3A"
```

**🪟 Windows**

```powershell
# Dopo il ripristino, verificare:
Get-ChildItem "$HOME\dati_scuola"
# registro.txt, voti.txt, programmazione.txt

# Verificare il contenuto
Get-Content "$HOME\dati_scuola\registro.txt"
# "Registro presenze - classe 3A"

# Confrontare con l'originale
$orig = Get-Content "$HOME\backup_scuola\registro.txt"
$rest = Get-Content "$HOME\dati_scuola\registro.txt"
Compare-Object $orig $rest
# Nessun output → identici ✓
```

</details>

<details>
<summary>Soluzione Step 9: quale metodo per quale situazione</summary>

| Situazione | Metodo consigliato | OS |
|-----------|-------------------|-----|
| Cancellare pochi file riservati | **shred** / **gshred** / **SDelete** | Linux / macOS / Windows |
| Dismissione completa di un PC | **DBAN** (boot da USB) oppure **Inizializza** (macOS) oppure **Reset PC** (Windows) | Tutti |
| Dati top-secret / militari | **Distruzione fisica** (trapano, tritatutto industriale, degaussing) | N/A — hardware |
| PC con disco cifrato (BitLocker/FileVault/LUKS) | **Cifratura + distruzione chiave** → i dati restano ma sono illeggibili | Tutti |

**Best practice aziendale:**

1. **Classificare** i dati presenti sul dispositivo
2. **Fare backup** dei dati necessari
3. **Scegliere il metodo** di sanitizzazione in base alla classificazione
4. **Eseguire** la sanitizzazione
5. **Verificare** che i dati non siano recuperabili
6. **Documentare** tutto nel certificato di dismissione
7. **Smaltire** il dispositivo secondo le normative RAEE

</details>
