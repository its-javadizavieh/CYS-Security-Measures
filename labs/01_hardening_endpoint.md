# Lab 01 - Hardening Endpoint

## Obiettivo

Applicare le tecniche di hardening di base su **Windows**, **Ubuntu/Linux** e **macOS**, svolgere un audit rapido della superficie di attacco e definire le priorità di messa in sicurezza.

## Durata

105-120 minuti

## Prerequisiti

- PC / VM con **Windows 10/11**, **Ubuntu 22.04** o **macOS Ventura+**
- Accesso amministratore / sudo / admin

## Scenario

Avete ricevuto un nuovo PC da configurare in modo sicuro prima di consegnarlo ad un dipendente. Dovete verificare firewall, aggiornamenti, antivirus e account.

---

## Step

### Step 1 - Verificare l'account (5 min)

**Windows**

```powershell
whoami
# Controllare di NON essere "Administrator"
```

**Ubuntu/Linux**

```bash
whoami
# Non deve essere "root"
```

**macOS**

```bash
whoami
# Non deve essere "root"
```

> Su tutti e tre i sistemi: **non usare mai l'account admin/root per le attività quotidiane.**

---

### Step 2 - Creare un utente standard di test (5 min)

**Windows (cmd come Amministratore)**

```cmd
net user studente_test Password1! /add
```

**Ubuntu/Linux**

```bash
sudo adduser --disabled-password --gecos "" studente_test
```

**macOS**

```bash
sudo dscl . -create /Users/studente_test
sudo dscl . -create /Users/studente_test UserShell /bin/zsh
sudo dscl . -create /Users/studente_test RealName "Studente Test"
sudo dscl . -create /Users/studente_test UniqueID 1050
sudo dscl . -create /Users/studente_test PrimaryGroupID 20
sudo dscl . -create /Users/studente_test NFSHomeDirectory /Users/studente_test
sudo mkdir -p /Users/studente_test
sudo chown studente_test:staff /Users/studente_test
```

> Oppure: Preferenze di Sistema → Utenti e Gruppi → "+" per aggiungere un utente Standard.

---

### Step 3 - Attivare il firewall (10 min)

**Windows**

1. Impostazioni → Privacy e sicurezza → Sicurezza di Windows → Firewall
2. Verificare che sia **Attivo** su tutti i profili: Dominio, Privato, Pubblico

Oppure da PowerShell (Amministratore):

```powershell
Get-NetFirewallProfile | Select Name, Enabled
# Tutti devono mostrare "True"
```

**Ubuntu/Linux**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose
```

**macOS**

1. Impostazioni di Sistema → Rete → Firewall → **Attiva**
2. Cliccare "Opzioni…" → attivare "Blocca tutte le connessioni in entrata" (opzionale, per massima sicurezza)

Oppure da terminale:

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
# Output: "Firewall is enabled"
```

---

### Step 4 - Verificare/installare gli aggiornamenti (10 min)

**Windows**

- Impostazioni → Windows Update → Controlla aggiornamenti → Installare eventuali aggiornamenti

**Ubuntu/Linux**

```bash
sudo apt update && sudo apt upgrade -y
```

**macOS**

```bash
softwareupdate --list
# Se ci sono aggiornamenti:
softwareupdate --install --all
```

Oppure: Impostazioni di Sistema → Generali → Aggiornamento Software

---

### Step 5 - Antivirus / protezione (10 min)

**Windows**

- Impostazioni → Sicurezza di Windows → Protezione da virus e minacce
- Verificare che la protezione in tempo reale sia **Attiva**

**Ubuntu/Linux - Installare fail2ban**

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo fail2ban-client status
```

> Linux non ha un antivirus tradizionale integrato, ma fail2ban protegge da attacchi brute-force via SSH.

**macOS - Verificare Gatekeeper e XProtect**

```bash
# Gatekeeper (blocca app non firmate)
spctl --status
# Output atteso: "assessments enabled"

# XProtect è l'antimalware integrato di macOS, si aggiorna automaticamente
```

---

### Step 6 - Sfida: verifica completa (20 min)

Compilate questa checklist per il vostro sistema operativo:

| Controllo                    | Windows | Ubuntu | macOS |
| ---------------------------- | ------- | ------ | ----- |
| Account non-admin?           | ☐       | ☐      | ☐     |
| Firewall attivo?             | ☐       | ☐      | ☐     |
| Aggiornamenti installati?    | ☐       | ☐      | ☐     |
| Antivirus/protezione attiva? | ☐       | ☐      | ☐     |

> **Sfida extra**: provate a eseguire i passaggi su un sistema operativo diverso da quello che usate di solito.

---

### Step 7 - Audit della superficie di attacco (15 min)

Obiettivo: capire quali servizi sono in ascolto sulla macchina e decidere se sono davvero necessari.

**Windows**

```powershell
Get-NetTCPConnection -State Listen | Sort-Object LocalPort | Select-Object -First 15 LocalAddress, LocalPort, OwningProcess
# Se volete identificare un PID specifico:
Get-Process -Id <PID>
```

**Ubuntu/Linux**

```bash
ss -tulpen
```

**macOS**

```bash
sudo lsof -iTCP -sTCP:LISTEN -n -P
```

Compilate questa tabella con almeno 3 righe reali del vostro sistema:

| Porta/Servizio | Esposto solo in locale o in rete? | Mi serve? | Azione |
| -------------- | --------------------------------- | --------- | ------ |
| **\_**         | **\_**                            | **\_**    | **\_** |
| **\_**         | **\_**                            | **\_**    | **\_** |
| **\_**         | **\_**                            | **\_**    | **\_** |

> Non disattivate servizi a caso: prima identificate il processo e discutete col docente se ha senso lasciarlo attivo.

---

### Step 8 - Verificare controlli avanzati (15 min)

**Windows - UAC e cifratura disco**

```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" | Select-Object EnableLUA, ConsentPromptBehaviorAdmin
manage-bde -status C:
```

**Ubuntu/Linux - Persistenza del firewall e stato dell'account root**

```bash
sudo systemctl is-enabled ufw
sudo passwd -S root
```

**macOS - Stealth Mode e FileVault**

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getstealthmode
fdesetup status
```

Annotate i risultati:

| Controllo avanzato | Stato osservato | Azione consigliata |
| ------------------ | --------------- | ------------------ |
| **\_**             | **\_**          | **\_**             |
| **\_**             | **\_**          | **\_**             |
| **\_**             | **\_**          | **\_**             |

---

### Step 9 - Piano di hardening e report finale (15 min)

Compilate questa tabella di priorità. Per ogni scenario scegliete le **3 misure più urgenti** tra quelle viste nel lab.

| Scenario                                                    | Priorità 1 | Priorità 2 | Priorità 3 | Motivazione |
| ----------------------------------------------------------- | ---------- | ---------- | ---------- | ----------- |
| Laptop personale usato su Wi-Fi pubblici                    | **\_**     | **\_**     | **\_**     | **\_**      |
| PC di laboratorio condiviso da più studenti                 | **\_**     | **\_**     | **\_**     | **\_**      |
| Notebook dirigente con file riservati e trasferte frequenti | **\_**     | **\_**     | **\_**     | **\_**      |

Consegna finale del lab:

- Checklist dello Step 6 compilata
- Tabella porte/servizi dello Step 7 compilata
- Tabella controlli avanzati dello Step 8 compilata
- Piano di hardening dello Step 9 compilato
- Almeno 3 evidenze raccolte (screenshot oppure output di comandi)

---

## Output atteso

- Firewall attivo su tutti i profili/interfacce
- Aggiornamenti installati
- Protezione attiva (Defender / fail2ban / Gatekeeper+XProtect)
- Utente standard creato
- Audit di almeno 3 servizi/porte in ascolto con decisione motivata
- Verifica di controlli avanzati coerenti con il sistema operativo usato
- Piano finale di priorità per tre scenari diversi

## Checkpoint

- [ ] Account non è admin/root
- [ ] Firewall attivo (tutti i profili su Windows, ufw su Linux, firewall su macOS)
- [ ] Aggiornamenti installati
- [ ] Protezione attiva
- [ ] Audit di porte/servizi completato
- [ ] Controlli avanzati verificati
- [ ] Piano di hardening finale compilato

## Troubleshooting rapido

| Problema                                  | Soluzione                                       |
| ----------------------------------------- | ----------------------------------------------- |
| `ufw: command not found`                  | `sudo apt install ufw`                          |
| Firewall Windows non si modifica          | Verificare di avere privilegi admin             |
| fail2ban non parte                        | `sudo journalctl -u fail2ban`                   |
| `ss: command not found`                   | `sudo apt install iproute2`                     |
| macOS: `spctl --status` → "disabled"      | `sudo spctl --master-enable`                    |
| macOS firewall non si attiva da terminale | Usare Impostazioni di Sistema → Rete → Firewall |

## Cleanup obbligatorio

**Windows**

```cmd
net user studente_test /delete
```

**Ubuntu/Linux**

```bash
sudo deluser --remove-home studente_test 2>/dev/null
# Le modifiche UFW e fail2ban migliorano la sicurezza: possono restare
```

**macOS**

```bash
sudo dscl . -delete /Users/studente_test
sudo rm -rf /Users/studente_test
```

## Parole chiave Google (screenshot/guide)

- "Windows 11 enable firewall all profiles"
- "Ubuntu ufw setup beginner"
- "macOS enable firewall terminal"
- "fail2ban SSH setup Ubuntu"
- "macOS Gatekeeper check status"

---

## Soluzioni

<details>
<summary>Soluzione Step 3: comandi firewall per ogni OS</summary>

**Windows (PowerShell Amministratore)**

```powershell
# Verificare stato di tutti i profili
Get-NetFirewallProfile | Select Name, Enabled

# Se un profilo è disabilitato, attivarlo:
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Verifica finale
Get-NetFirewallProfile | Select Name, Enabled
# Output: tutti "True"
```

**Ubuntu/Linux**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose
# Output atteso:
# Status: active
# Default: deny (incoming), allow (outgoing)
# 22/tcp ALLOW IN Anywhere
```

**macOS**

```bash
# Attivare il firewall
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on
# Verificare
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate
# Output: "Firewall is enabled. (State = 1)"
```

</details>

<details>
<summary>Soluzione Step 5: verifica antivirus/protezione</summary>

**Windows**

- Percorso: Impostazioni → Sicurezza di Windows → Protezione da virus e minacce
- "Protezione in tempo reale" deve essere **Attiva** (interruttore verde)
- Se disattivata: cliccare l'interruttore per riattivarla

**Ubuntu/Linux**

```bash
sudo systemctl status fail2ban
# Output atteso:
# Active: active (running)

sudo fail2ban-client status
# Output atteso:
# Number of jail: 1
# Jail list: sshd
```

**macOS**

```bash
# Gatekeeper
spctl --status
# Output: "assessments enabled" → OK

# XProtect si aggiorna da solo. Per verificare la versione:
system_profiler SPInstallHistoryDataType | grep -A 2 "XProtect"
```

</details>

<details>
<summary>Soluzione Step 6: checklist compilata (risposte attese)</summary>

| Controllo                 | Windows                                             | Ubuntu                               | macOS                                                    |
| ------------------------- | --------------------------------------------------- | ------------------------------------ | -------------------------------------------------------- |
| Account non-admin?        | ✅ `whoami` ≠ Administrator                         | ✅ `whoami` ≠ root                   | ✅ `whoami` ≠ root                                       |
| Firewall attivo?          | ✅ Tutti i profili "True"                           | ✅ `ufw status` → active             | ✅ `socketfilterfw --getglobalstate` → enabled           |
| Aggiornamenti installati? | ✅ Windows Update → nessun aggiornamento in sospeso | ✅ `apt upgrade` → 0 newly installed | ✅ `softwareupdate --list` → "No new software available" |
| Antivirus/protezione?     | ✅ Defender real-time ON                            | ✅ fail2ban active (running)         | ✅ Gatekeeper enabled, XProtect aggiornato               |

</details>

<details>
<summary>Soluzione Step 7: come leggere i servizi in ascolto</summary>

Non esiste un elenco identico per tutti i PC: l'obiettivo è imparare a **interpretare** ciò che vedete.

Regole rapide:

- Se il servizio ascolta su `127.0.0.1`, `::1` o `localhost`, è esposto solo localmente
- Se ascolta su `0.0.0.0`, `*` o sull'IP reale della macchina, può ricevere connessioni di rete
- Una porta aperta non è automaticamente un problema, ma va sempre giustificata

Esempi di ragionamento:

| Caso osservato                                            | Interpretazione                                              | Azione                                       |
| --------------------------------------------------------- | ------------------------------------------------------------ | -------------------------------------------- |
| DNS locale su `127.0.0.53:53` (Ubuntu)                    | Servizio locale di risoluzione nomi, non esposto all'esterno | Tenere                                       |
| SSH su `0.0.0.0:22`                                       | Accesso remoto disponibile da rete                           | Tenere solo se davvero necessario e protetto |
| RDP `3389`, SMB `445` o altri servizi remoti non previsti | Aumentano la superficie di attacco                           | Verificare col docente e chiudere se inutili |

</details>

<details>
<summary>Soluzione Step 8: risultati attesi dei controlli avanzati</summary>

**Windows**

- `EnableLUA = 1` → UAC attivo
- `ConsentPromptBehaviorAdmin` tipicamente vale `5` oppure un valore che mantiene il prompt di conferma attivo
- `manage-bde -status C:` dovrebbe mostrare se BitLocker è attivo, in corso o non disponibile

**Ubuntu/Linux**

- `sudo systemctl is-enabled ufw` → output atteso: `enabled`
- `sudo passwd -S root` → output atteso con `L` dopo `root`, che indica account root bloccato per login diretto

**macOS**

- `--getstealthmode` → output atteso: stealth mode enabled
- `fdesetup status` → output desiderato: `FileVault is On.`

Se un controllo non è attivo, la colonna "Azione consigliata" deve indicare il passo successivo per abilitarlo o verificarlo meglio.

</details>

<details>
<summary>Soluzione Step 9: priorità consigliate per i tre scenari</summary>

| Scenario                                                    | Priorità 1        | Priorità 2               | Priorità 3                                  | Motivazione                                                                                  |
| ----------------------------------------------------------- | ----------------- | ------------------------ | ------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Laptop personale usato su Wi-Fi pubblici                    | Firewall attivo   | Aggiornamenti automatici | Account standard                            | Riduce esposizione di rete, corregge vulnerabilità note e limita i danni di un errore utente |
| PC di laboratorio condiviso da più studenti                 | Account non-admin | Firewall attivo          | Verifica servizi/porte in ascolto           | Un PC condiviso ha più rischio di uso improprio e più superficie di attacco locale           |
| Notebook dirigente con file riservati e trasferte frequenti | Cifratura disco   | Account standard         | Aggiornamenti + protezioni integrate attive | In caso di furto o smarrimento, la cifratura è la misura più critica per proteggere i dati   |

> Le priorità possono cambiare leggermente in base al contesto, ma devono sempre essere motivate in modo coerente con il rischio.

</details>

---

## Riferimenti utili

- [WikiHow - Come controllare le impostazioni del firewall (Windows, macOS, Linux)](https://www.wikihow.com/Check-Your-Firewall-Settings) - guida visuale passo-passo per navigare le impostazioni del firewall su tutti e tre i sistemi operativi
- [Microsoft - Come funziona UAC](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/how-it-works) - spiegazione ufficiale dei livelli di elevazione UAC
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks) - guide di hardening per Windows, Linux e macOS
