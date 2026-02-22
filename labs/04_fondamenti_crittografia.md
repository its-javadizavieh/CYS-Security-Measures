# Lab 04 — Fondamenti di Crittografia

## Obiettivo

Sperimentare hashing (SHA-256), cifratura simmetrica (AES) e cifratura asimmetrica (RSA) su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

60 minuti

## Prerequisiti

- **Ubuntu/Linux** o **macOS**: terminale aperto (openssl e shasum sono preinstallati)
- **Windows**: PowerShell oppure installare Git Bash / WSL per avere openssl

> **Nota Windows**: se non avete openssl, potete usare CertUtil (integrato) per gli hash e le alternative PowerShell indicate sotto.

## Scenario

Il vostro capo vi chiede di dimostrare che sapete cifrare un file riservato e verificare l'integrità di un download. Userete strumenti da riga di comando su tutti e tre gli OS.

---

## Step

### Parte A — Hashing con SHA-256 (15 min)

**Step 1 — Creare un file di test**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
echo "Questo è un messaggio segreto" > messaggio.txt
cat messaggio.txt
```

**🪟 Windows (PowerShell)**

```powershell
Set-Content -Path messaggio.txt -Value "Questo è un messaggio segreto"
Get-Content messaggio.txt
```

---

**Step 2 — Calcolare l'hash SHA-256**

**🐧 Ubuntu/Linux**

```bash
sha256sum messaggio.txt
```

**🍎 macOS**

```bash
shasum -a 256 messaggio.txt
```

**🪟 Windows (PowerShell)**

```powershell
Get-FileHash messaggio.txt -Algorithm SHA256
```

> Annotate l'hash. È una stringa di 64 caratteri esadecimali.

---

**Step 3 — Modificare il file e ricalcolare**

Aggiungete un singolo punto al messaggio e ricalcolate l'hash:

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
echo "Questo è un messaggio segreto." > messaggio.txt
sha256sum messaggio.txt        # Linux
shasum -a 256 messaggio.txt    # macOS
```

**🪟 Windows**

```powershell
Set-Content -Path messaggio.txt -Value "Questo è un messaggio segreto."
Get-FileHash messaggio.txt -Algorithm SHA256
```

> L'hash è **completamente diverso** anche con una sola modifica!

---

### Parte B — Cifratura Simmetrica con AES (20 min)

**Step 4 — Cifrare un file con AES-256**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
echo "Piano strategico riservato" > segreto.txt
openssl enc -aes-256-cbc -salt -pbkdf2 -in segreto.txt -out segreto.enc
# Vi chiederà una password — inventatene una e ricordatela!
```

**🪟 Windows (con openssl via Git Bash o WSL)**

```bash
echo "Piano strategico riservato" > segreto.txt
openssl enc -aes-256-cbc -salt -pbkdf2 -in segreto.txt -out segreto.enc
```

**🪟 Windows (alternativa PowerShell senza openssl)**

> Se non avete openssl, potete usare 7-Zip per cifrare con AES:
>
> 1. Tasto destro su `segreto.txt` → 7-Zip → Aggiungi all'archivio
> 2. Impostare "Metodo crittografia: AES-256" e inserire una password

---

**Step 5 — Verificare che il file cifrato non è leggibile**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
cat segreto.enc
# Output: caratteri illeggibili → il file è cifrato!
```

**🪟 Windows**

```powershell
Get-Content segreto.enc
# Output: caratteri illeggibili → il file è cifrato!
```

---

**Step 6 — Decifrare il file**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in segreto.enc -out rivelato.txt
# Inserire la stessa password usata al punto 4
cat rivelato.txt
# Output: "Piano strategico riservato"
```

**🪟 Windows (con openssl)**

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in segreto.enc -out rivelato.txt
cat rivelato.txt
```

---

### Parte C — Cifratura Asimmetrica con RSA (25 min)

**Step 7 — Generare una coppia di chiavi RSA**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
openssl genrsa -out chiave_privata.pem 2048
openssl rsa -in chiave_privata.pem -pubout -out chiave_pubblica.pem
```

**🪟 Windows (con openssl via Git Bash/WSL)**

```bash
openssl genrsa -out chiave_privata.pem 2048
openssl rsa -in chiave_privata.pem -pubout -out chiave_pubblica.pem
```

**🪟 Windows (alternativa PowerShell nativa)**

```powershell
# PowerShell può generare chiavi RSA tramite .NET:
$rsa = [System.Security.Cryptography.RSA]::Create(2048)
$rsa.ExportRSAPublicKeyPem() | Set-Content chiave_pubblica.pem
$rsa.ExportRSAPrivateKeyPem() | Set-Content chiave_privata.pem
```

---

**Step 8 — Vedere le chiavi**

**Tutti gli OS**

```bash
cat chiave_pubblica.pem    # Linux/macOS
# oppure:
Get-Content chiave_pubblica.pem    # Windows PowerShell
```

> La chiave pubblica potete condividerla con chiunque.
> La chiave privata è **SEGRETA** — non condividerla MAI!

---

**Step 9 — Cifrare con la chiave pubblica e decifrare con la privata**

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
echo "Messaggio per il destinatario" > msg.txt
openssl pkeyutl -encrypt -pubin -inkey chiave_pubblica.pem -in msg.txt -out msg.enc

# Decifrare
openssl pkeyutl -decrypt -inkey chiave_privata.pem -in msg.enc -out msg_decifrato.txt
cat msg_decifrato.txt
# Output: "Messaggio per il destinatario"
```

**🪟 Windows (con openssl)**

Stessi comandi — openssl è identico su tutti gli OS.

---

## Output atteso

- Hash SHA-256 calcolato e confrontato (diverso con qualsiasi modifica)
- File cifrato con AES e decifrato correttamente
- Chiavi RSA generate, file cifrato con la pubblica e decifrato con la privata

## Checkpoint

- [ ] `sha256sum` / `shasum` / `Get-FileHash` restituisce hash diversi per file diversi
- [ ] File cifrato con AES (`segreto.enc`) non è leggibile
- [ ] File decifrato (`rivelato.txt`) contiene il testo originale
- [ ] Chiavi RSA generate (`chiave_privata.pem` e `chiave_pubblica.pem`)
- [ ] Cifratura/decifratura RSA funzionante

## Troubleshooting rapido

| Problema | Soluzione |
|----------|----------|
| `openssl: command not found` (Linux) | `sudo apt install openssl` |
| `openssl: command not found` (macOS) | Preinstallato; se manca: `brew install openssl` |
| `openssl: command not found` (Windows) | Installare Git Bash (include openssl) o usare WSL |
| Errore "bad decrypt" | Avete inserito la password sbagliata al momento della decifratura |
| Errore RSA "data too large" | RSA cifra solo dati piccoli (max ~245 byte per 2048-bit). Per file grandi usare AES |
| `shasum` non trovato (macOS) | `brew install coreutils` per avere `gsha256sum` |

## Cleanup obbligatorio

**🐧 Ubuntu/Linux e 🍎 macOS**

```bash
rm -f messaggio.txt segreto.txt segreto.enc rivelato.txt
rm -f chiave_privata.pem chiave_pubblica.pem
rm -f msg.txt msg.enc msg_decifrato.txt
```

**🪟 Windows (PowerShell)**

```powershell
Remove-Item messaggio.txt, segreto.txt, segreto.enc, rivelato.txt -ErrorAction SilentlyContinue
Remove-Item chiave_privata.pem, chiave_pubblica.pem -ErrorAction SilentlyContinue
Remove-Item msg.txt, msg.enc, msg_decifrato.txt -ErrorAction SilentlyContinue
```

## Parole chiave Google (screenshot/guide)

- "openssl aes encrypt file tutorial"
- "sha256sum linux example"
- "PowerShell Get-FileHash SHA256"
- "openssl RSA encrypt decrypt tutorial"
- "install openssl on Windows"

---

## Soluzioni

<details>
<summary>Soluzione Step 3: perché l'hash cambia completamente</summary>

**Concetto chiave — Effetto valanga (Avalanche Effect)**

Anche modificando un solo bit dell'input, l'hash cambia completamente. Esempio:

```
"Questo è un messaggio segreto"  → a1b2c3d4...  (hash A)
"Questo è un messaggio segreto." → 9f8e7d6c...  (hash B)
```

I due hash non hanno **nessuna somiglianza**. Questo è fondamentale per:

- **Integrità dei download**: se l'hash del file scaricato non corrisponde a quello pubblicato dal sito, il file è stato modificato (forse da un malware)
- **Memorizzazione password**: i siti non salvano la password in chiaro, ma il suo hash

**Comandi riassuntivi per ogni OS:**

| OS | Comando |
|----|---------|
| 🐧 Ubuntu/Linux | `sha256sum nomefile.txt` |
| 🍎 macOS | `shasum -a 256 nomefile.txt` |
| 🪟 Windows | `Get-FileHash nomefile.txt -Algorithm SHA256` |

</details>

<details>
<summary>Soluzione Step 6: cifratura/decifratura AES completa</summary>

**Cifrare:**

```bash
openssl enc -aes-256-cbc -salt -pbkdf2 -in segreto.txt -out segreto.enc
```

Parametri:

- `enc` → modalità cifratura
- `-aes-256-cbc` → algoritmo AES con chiave da 256 bit, modalità CBC
- `-salt` → aggiunge dati random per proteggere da attacchi dizionario
- `-pbkdf2` → deriva la chiave dalla password in modo sicuro
- `-in` → file di input, `-out` → file di output

**Decifrare:**

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in segreto.enc -out rivelato.txt
```

Il flag `-d` indica la decifratura. La password deve essere **identica** a quella usata per cifrare.

**🪟 Alternativa Windows senza openssl (7-Zip):**

1. Tasto destro sul file → 7-Zip → Aggiungi all'archivio
2. Formato: 7z, Crittografia: AES-256, inserire password
3. Per decifrare: doppio click sul `.7z` → inserire password

</details>

<details>
<summary>Soluzione Step 9: cifratura asimmetrica RSA — flusso completo</summary>

**Flusso:**

1. **Alice** genera la coppia di chiavi (pubblica + privata)
2. Alice **condivide la chiave pubblica** con Bob
3. **Bob** cifra il messaggio con la chiave pubblica di Alice
4. Bob invia il messaggio cifrato ad Alice
5. **Alice** decifra con la sua chiave privata → legge il messaggio

**Perché è sicuro?**

- Anche se un attaccante intercetta il messaggio cifrato E la chiave pubblica, **non può decifrare** senza la chiave privata
- La chiave privata non viene mai condivisa

**Comandi (identici su Linux, macOS, Windows con openssl):**

```bash
# Generare chiavi
openssl genrsa -out chiave_privata.pem 2048
openssl rsa -in chiave_privata.pem -pubout -out chiave_pubblica.pem

# Cifrare
openssl pkeyutl -encrypt -pubin -inkey chiave_pubblica.pem -in msg.txt -out msg.enc

# Decifrare
openssl pkeyutl -decrypt -inkey chiave_privata.pem -in msg.enc -out msg_decifrato.txt
```

**Limite di RSA**: può cifrare solo dati piccoli (~245 byte con chiave da 2048 bit). Per file grandi, si usa **cifratura ibrida**: RSA cifra una chiave AES, poi AES cifra i dati (è quello che fa HTTPS!).

</details>
