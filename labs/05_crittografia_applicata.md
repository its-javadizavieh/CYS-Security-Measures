# Lab 05 - Crittografia Applicata

## Obiettivo

Cifrare un disco/volume con **BitLocker** (Windows), **LUKS** (Linux), **FileVault** (macOS) e usare **GPG** per cifrare e firmare file su tutti e tre gli OS, ragionando anche su recovery key, verifica firme e scelte operative corrette.

## Durata

105-120 minuti

## Prerequisiti

- **Windows 10/11 Pro** (BitLocker), oppure **Ubuntu 22.04**, oppure **macOS Ventura+**
- Per GPG:
  - Linux: `gpg --version` (preinstallato)
  - macOS: `brew install gnupg` oppure preinstallato su alcune versioni
  - Windows: installare [Gpg4win](https://gpg4win.org/) oppure usare Git Bash (include GPG)

## Scenario

L'azienda richiede che tutti i laptop abbiano il disco cifrato e che i documenti riservati inviati via email siano cifrati con GPG.

---

## Step

### Parte A - Cifratura Disco (25 min)

Scegliete la sezione del vostro OS.

#### Windows - BitLocker

**Step 1 - Verificare se BitLocker è disponibile**

```powershell
Get-BitLockerVolume -MountPoint "C:"
```

> BitLocker è disponibile solo su Windows 10/11 **Pro/Enterprise**. Se avete Home, leggete la sezione e osservate il docente.

**Step 2 - Attivare BitLocker su C:**

1. Pannello di Controllo → Sistema e sicurezza → Crittografia unità BitLocker
2. Cliccare "Attiva BitLocker" accanto a C:
3. Scegliere dove salvare la chiave di recupero:
   - ✅ Salva nel tuo account Microsoft
   - ✅ Salva su file (su USB o percorso di rete)
   - ✅ Stampa la chiave
4. Scegliere "Cifra l'intera unità"
5. Avviare la crittografia (può richiedere tempo)

**Step 3 - Verificare lo stato**

```powershell
manage-bde -status C:
# Protection Status: Protection On
# Encryption Method: XTS-AES 128 / XTS-AES 256
```

---

#### Ubuntu/Linux - LUKS (volume virtuale)

**Step 1 - Creare un file che simula un disco**

```bash
dd if=/dev/zero of=~/disco_test.img bs=1M count=50
```

**Step 2 - Associarlo, formattare con LUKS e montare**

> **Errore `Device or resource busy`?** Su molte installazioni Ubuntu (soprattutto con Snap), `/dev/loop0`–`/dev/loop18` sono già occupati. Non scegliere un numero a caso: trovate il primo loop libero e usate **sempre lo stesso device** in tutti i comandi del lab.
>
> ```bash
> losetup -a              # elenca i loop già in uso
> sudo losetup -f           # primo loop libero, es. /dev/loop19
> sudo losetup /dev/loop19 ~/disco_test.img
> losetup -a | grep disco_test   # conferma l'associazione
> ```
>
> Se `losetup -f` restituisce `/dev/loop19`, sostituite `/dev/loop10` con `/dev/loop19` nei comandi sotto.

```bash
sudo losetup /dev/loop10 ~/disco_test.img
sudo cryptsetup luksFormat /dev/loop10
# Confermare con YES (maiuscolo!), inserire una passphrase

sudo cryptsetup luksOpen /dev/loop10 vault_test
sudo mkfs.ext4 /dev/mapper/vault_test
sudo mkdir -p /mnt/vault
sudo mount /dev/mapper/vault_test /mnt/vault
```

**Step 3 - Scrivere un file e verificare la cifratura**

```bash
echo "Dati super segreti nel volume cifrato!" | sudo tee /mnt/vault/segreto.txt
cat /mnt/vault/segreto.txt

# Smontare e verificare che i dati NON sono leggibili dal file immagine
sudo umount /mnt/vault
sudo cryptsetup luksClose vault_test
sudo strings ~/disco_test.img | grep "segreti"
# Nessun risultato → i dati sono cifrati!
```

---

#### macOS - FileVault

**Step 1 - Verificare lo stato di FileVault**

```bash
sudo fdesetup status
# Output: "FileVault is On." oppure "FileVault is Off."
```

**Step 2 - Attivare FileVault (se Off)**

1. Impostazioni di Sistema → Privacy e Sicurezza → FileVault → **Attiva**
2. Scegliere il metodo di recupero:
   - ✅ Consenti al mio account iCloud di sbloccare il disco
   - ✅ Crea una chiave di recupero (annotarla in un luogo sicuro!)
3. Il Mac inizierà a cifrare il disco (avviene in background)

Oppure da terminale:

```bash
sudo fdesetup enable
# Mostra la chiave di recupero → SALVARLA in un luogo sicuro!
```

**Step 3 - Verificare dopo l'attivazione**

```bash
sudo fdesetup status
# Output: "FileVault is On."
# "Encryption in progress" se sta ancora cifrando
```

---

### Parte B - GPG: Cifratura e Firma (35 min)

I comandi GPG sono **identici su tutti gli OS** dopo l'installazione.

**Step 4 - Installare GPG (se necessario)**

**Ubuntu/Linux**: preinstallato (`gpg --version`)

**macOS**:

```bash
brew install gnupg
gpg --version
```

**Windows**: scaricare e installare [Gpg4win](https://gpg4win.org/), oppure usare Git Bash (include GPG).

```cmd
gpg --version
```

---

**Step 5 - Generare le chiavi GPG**

```bash
gpg --full-generate-key
# Selezionare:
# Tipo: 1 (RSA and RSA)
# Lunghezza: 2048
# Scadenza: 0 (non scade - per il lab va bene)
# Nome e email: usare dati di test
# Passphrase: inventarne una
```

**Step 6 - Vedere le chiavi**

```bash
gpg --list-keys
gpg --list-secret-keys
```

---

**Step 7 - Cifrare un file con GPG**

```bash
echo "Documento riservato per il capo" > documento.txt
gpg --encrypt --recipient "email_di_test@example.com" documento.txt
# Crea: documento.txt.gpg

cat documento.txt.gpg
# Output: illeggibile → cifrato!
```

**Step 8 - Decifrare il file**

```bash
gpg --decrypt documento.txt.gpg > documento_decifrato.txt
cat documento_decifrato.txt
# Output: "Documento riservato per il capo"
```

---

**Step 9 - Firmare un file e verificare la firma**

```bash
# Firmare
gpg --detach-sign documento.txt
# Crea: documento.txt.sig

# Verificare
gpg --verify documento.txt.sig documento.txt
# Output: "Good signature from ..."

# Modificare il file e ri-verificare
echo "modifica non autorizzata" >> documento.txt
gpg --verify documento.txt.sig documento.txt
# Output: "BAD signature" → il file è stato modificato!
```

---

### Parte C - Decisioni operative e gestione incidenti (20 min)

**Step 10 - Pianificare dove salvare la recovery key**

Compilate una tabella con almeno un'opzione accettabile e una non accettabile per la recovery key del vostro OS.

| Opzione                              | Accettabile? | Perché                                          |
| ------------------------------------ | ------------ | ----------------------------------------------- |
| Password manager                     | Sì           | Separato dal device e facile da recuperare      |
| Chiavetta USB in cassaforte          | Sì           | Supporto separato e fisicamente protetto        |
| File sul desktop dello stesso laptop | No           | Se perdo il laptop, perdo anche la recovery key |

Aggiungete una quarta riga con una vostra proposta motivata.

---

**Step 11 - Scegliere la protezione corretta in tre scenari**

Per ogni scenario indicate lo strumento principale e la prima azione corretta:

| Scenario                                    | Strumento/controllo principale | Prima azione corretta                                                                 |
| ------------------------------------------- | ------------------------------ | ------------------------------------------------------------------------------------- |
| Laptop aziendale smarrito                   | BitLocker / LUKS / FileVault   | Verificare che la cifratura sia attiva e usare la procedura aziendale di segnalazione |
| Documento riservato da inviare a un collega | GPG                            | Cifrare per il destinatario corretto e verificare l'identità della chiave             |
| Firma digitale risultata `BAD signature`    | Verifica integrità             | Fermarsi e non fidarsi del file finché non si chiarisce l'origine della modifica      |

---

**Step 12 - Checklist finale di risposta operativa**

Scrivete una checklist da 5 punti che risponda a una di queste situazioni:

- il laptop cifrato viene perso o rubato
- ricevete un file con firma digitale fallita

La checklist deve contenere almeno una azione tecnica e una azione di escalation/comunicazione.

---

## Output atteso

- Disco/volume cifrato verificato (BitLocker / LUKS / FileVault)
- File GPG cifrato e decifrato correttamente
- Firma digitale creata, verificata, e "BAD signature" dopo modifica
- Tabella recovery key completata con scelte motivate
- Tabella scenari/strumenti completata
- Checklist finale di risposta operativa pronta

## Checkpoint

- [ ] Cifratura disco verificata (BitLocker / LUKS volume / FileVault)
- [ ] Chiave di recupero salvata in un luogo sicuro
- [ ] Chiavi GPG generate
- [ ] File cifrato con GPG e decifrato
- [ ] Firma "Good signature" verificata
- [ ] Firma "BAD signature" dopo modifica del file
- [ ] Piano di salvataggio recovery key completato
- [ ] Scelta corretta degli strumenti nei 3 scenari
- [ ] Checklist finale pronta

## Troubleshooting rapido

| Problema                                 | Soluzione                                                                                        |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------ |
| BitLocker non disponibile (Windows Home) | BitLocker richiede Pro/Enterprise. Osservare il docente o usare VeraCrypt (alternativa gratuita) |
| `cryptsetup: command not found` (Linux)  | `sudo apt install cryptsetup`                                                                    |
| `losetup: Device or resource busy` (Linux) | `losetup -a` per vedere i loop occupati; `sudo losetup -f` per il primo libero (es. `/dev/loop19`); poi `sudo losetup /dev/loop19 ~/disco_test.img` e usate quel device in tutti i comandi successivi |
| `gpg: command not found` (macOS)         | `brew install gnupg`                                                                             |
| `gpg: command not found` (Windows)       | Installare Gpg4win o usare Git Bash                                                              |
| GPG si blocca alla generazione chiavi    | Muovere il mouse / digitare per generare entropia                                                |
| FileVault: "not supported"               | Verificare di avere macOS 10.10+ su hardware supportato                                          |
| Non so dove mettere la recovery key      | Scegliere un posto separato dal device: password manager, USB dedicata o cassaforte              |
| `BAD signature` su un file urgente       | Non aprire o distribuire il file: verificare mittente, integrità e canale di consegna            |

## Cleanup obbligatorio

**Ubuntu/Linux (LUKS)**

```bash
sudo cryptsetup luksClose vault_test 2>/dev/null
sudo losetup -d /dev/loop10 2>/dev/null
rm -f ~/disco_test.img
sudo rmdir /mnt/vault 2>/dev/null
```

**Tutti gli OS (GPG)**

```bash
rm -f documento.txt documento.txt.gpg documento.txt.sig documento_decifrato.txt
# Opzionale: rimuovere chiavi GPG di test
# gpg --delete-secret-and-public-key "email_di_test@example.com"
```

**Windows (PowerShell)**

```powershell
Remove-Item documento.txt, documento.txt.gpg, documento.txt.sig, documento_decifrato.txt -ErrorAction SilentlyContinue
# BitLocker: NON disattivare dopo il lab (è un miglioramento di sicurezza)
```

## Parole chiave Google (screenshot/guide)

- "BitLocker enable Windows 11 Pro"
- "LUKS encrypt partition Ubuntu tutorial"
- "macOS FileVault enable terminal"
- "GPG encrypt file tutorial"
- "Gpg4win install Windows"

---

## Soluzioni

<details>
<summary>Soluzione Parte A: verifica cifratura disco per ogni OS</summary>

**Windows - BitLocker**

```powershell
manage-bde -status C:
```

Output atteso:

```
Volume C: []
    Conversion Status:    Fully Encrypted
    Encryption Method:    XTS-AES 128
    Protection Status:    Protection On
    Lock Status:          Unlocked
    Key Protectors:       TPM, Recovery Password
```

**Ubuntu/Linux - LUKS**

```bash
# Dopo aver montato il volume:
sudo cryptsetup luksDump /dev/loop10
```

Output atteso (prime righe):

```
LUKS header information
Version:        2
Epoch:          3
Metadata area:  16384 [bytes]
Cipher name:    aes
Cipher mode:    xts-plain64
```

Verifica che i dati siano cifrati:

```bash
sudo strings ~/disco_test.img | grep "segreti"
# Nessun risultato = cifratura funzionante ✓
```

**macOS - FileVault**

```bash
sudo fdesetup status
# Output: "FileVault is On."

# Per vedere gli utenti abilitati allo sblocco:
sudo fdesetup list
```

</details>

<details>
<summary>Soluzione Step 9: firma digitale - Good vs BAD signature</summary>

**Good signature (file originale non modificato):**

```bash
gpg --verify documento.txt.sig documento.txt
```

Output:

```
gpg: Signature made gio 22 feb 2026 10:30:00 CET
gpg: using RSA key ABCDEF1234567890
gpg: Good signature from "Nome Test <email_di_test@example.com>"
```

**BAD signature (file modificato):**

```bash
echo "modifica non autorizzata" >> documento.txt
gpg --verify documento.txt.sig documento.txt
```

Output:

```
gpg: Signature made gio 22 feb 2026 10:30:00 CET
gpg: using RSA key ABCDEF1234567890
gpg: BAD signature from "Nome Test <email_di_test@example.com>"
```

**Perché è importante?**

- La firma digitale garantisce **autenticazione** (chi lo ha mandato) e **integrità** (non è stato modificato)
- Se la firma è "BAD", qualcuno ha modificato il file dopo la firma → non fidatevi del contenuto!

</details>

<details>
<summary>Soluzione: dove salvare la chiave di recupero</summary>

**Regola d'oro: MAI salvare la chiave di recupero sullo stesso dispositivo cifrato!**

| Metodo                                            | Sicurezza  | Praticità  |
| ------------------------------------------------- | ---------- | ---------- |
| Password manager (Bitwarden/KeePassXC)            | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐   |
| Chiavetta USB dedicata in cassaforte              | ⭐⭐⭐⭐   | ⭐⭐⭐     |
| Foglio stampato in cassaforte                     | ⭐⭐⭐     | ⭐⭐       |
| Account Microsoft/Apple (per BitLocker/FileVault) | ⭐⭐⭐     | ⭐⭐⭐⭐⭐ |

**Metodi da NON usare:**

- ❌ File sul desktop dello stesso PC cifrato
- ❌ Email a sé stessi (potrebbe essere compromessa)
- ❌ Post-it attaccato al monitor

</details>

<details>
<summary>Soluzione Step 11: scelta corretta negli scenari</summary>

Una risposta corretta segue questa logica:

| Scenario             | Scelta corretta                    | Motivo                                                                           |
| -------------------- | ---------------------------------- | -------------------------------------------------------------------------------- |
| Laptop smarrito      | **Cifratura disco + segnalazione** | La cifratura riduce l'impatto, ma va comunque attivata la procedura di incidente |
| Documento da inviare | **GPG**                            | Protegge il contenuto per un destinatario specifico                              |
| Firma fallita        | **Bloccare il flusso**             | `BAD signature` significa che autenticità o integrità non sono affidabili        |

</details>

<details>
<summary>Soluzione Step 12: esempio di checklist finale</summary>

Esempio corretto per "firma digitale fallita":

```text
1. Non apro ulteriormente il file e non lo inoltro.
2. Verifico il mittente con un canale separato.
3. Confronto nome file, hash o nuova copia se disponibile.
4. Segnalo il caso al referente IT o al docente.
5. Documento l'evento e la decisione presa.
```

</details>
