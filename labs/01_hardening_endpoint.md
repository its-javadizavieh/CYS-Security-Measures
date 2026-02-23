# Lab 01 — Hardening Endpoint

## Obiettivo

Applicare le tecniche di hardening di base su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

60 minuti

## Prerequisiti

- PC / VM con **Windows 10/11**, **Ubuntu 22.04** o **macOS Ventura+**
- Accesso amministratore / sudo / admin

## Scenario

Avete ricevuto un nuovo PC da configurare in modo sicuro prima di consegnarlo ad un dipendente. Dovete verificare firewall, aggiornamenti, antivirus e account.

---

## Step

### Step 1 — Verificare l'account (5 min)

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

### Step 2 — Creare un utente standard di test (5 min)

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

### Step 3 — Attivare il firewall (10 min)

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

### Step 4 — Verificare/installare gli aggiornamenti (10 min)

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

### Step 5 — Antivirus / protezione (10 min)

**Windows**

- Impostazioni → Sicurezza di Windows → Protezione da virus e minacce
- Verificare che la protezione in tempo reale sia **Attiva**

**Ubuntu/Linux — Installare fail2ban**

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo fail2ban-client status
```

> Linux non ha un antivirus tradizionale integrato, ma fail2ban protegge da attacchi brute-force via SSH.

**macOS — Verificare Gatekeeper e XProtect**

```bash
# Gatekeeper (blocca app non firmate)
spctl --status
# Output atteso: "assessments enabled"

# XProtect è l'antimalware integrato di macOS, si aggiorna automaticamente
```

---

### Step 6 — Sfida: verifica completa (20 min)

Compilate questa checklist per il vostro sistema operativo:

| Controllo | Windows | Ubuntu | macOS |
|-----------|---------|--------|-------|
| Account non-admin? | ☐ | ☐ | ☐ |
| Firewall attivo? | ☐ | ☐ | ☐ |
| Aggiornamenti installati? | ☐ | ☐ | ☐ |
| Antivirus/protezione attiva? | ☐ | ☐ | ☐ |

> **Sfida extra**: provate a eseguire i passaggi su un sistema operativo diverso da quello che usate di solito.

---

## Output atteso

- Firewall attivo su tutti i profili/interfacce
- Aggiornamenti installati
- Protezione attiva (Defender / fail2ban / Gatekeeper+XProtect)
- Utente standard creato

## Checkpoint

- [ ] Account non è admin/root
- [ ] Firewall attivo (tutti i profili su Windows, ufw su Linux, firewall su macOS)
- [ ] Aggiornamenti installati
- [ ] Protezione attiva

## Troubleshooting rapido

| Problema | Soluzione |
|----------|----------|
| `ufw: command not found` | `sudo apt install ufw` |
| Firewall Windows non si modifica | Verificare di avere privilegi admin |
| fail2ban non parte | `sudo journalctl -u fail2ban` |
| macOS: `spctl --status` → "disabled" | `sudo spctl --master-enable` |
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

| Controllo | Windows | Ubuntu | macOS |
|-----------|---------|--------|-------|
| Account non-admin? | ✅ `whoami` ≠ Administrator | ✅ `whoami` ≠ root | ✅ `whoami` ≠ root |
| Firewall attivo? | ✅ Tutti i profili "True" | ✅ `ufw status` → active | ✅ `socketfilterfw --getglobalstate` → enabled |
| Aggiornamenti installati? | ✅ Windows Update → nessun aggiornamento in sospeso | ✅ `apt upgrade` → 0 newly installed | ✅ `softwareupdate --list` → "No new software available" |
| Antivirus/protezione? | ✅ Defender real-time ON | ✅ fail2ban active (running) | ✅ Gatekeeper enabled, XProtect aggiornato |

</details>

---

## Riferimenti utili

- [WikiHow — Come controllare le impostazioni del firewall (Windows, macOS, Linux)](https://www.wikihow.com/Check-Your-Firewall-Settings) — guida visuale passo-passo per navigare le impostazioni del firewall su tutti e tre i sistemi operativi
- [Microsoft — Come funziona UAC](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/user-account-control/how-it-works) — spiegazione ufficiale dei livelli di elevazione UAC
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks) — guide di hardening per Windows, Linux e macOS
