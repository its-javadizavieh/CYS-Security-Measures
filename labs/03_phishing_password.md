# Lab 03 - Phishing e Password

## Obiettivo

Imparare a riconoscere le email di phishing, capire il rischio del riuso password e configurare un password manager su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

105-120 minuti

## Prerequisiti

- PC con browser aggiornato (qualsiasi OS)
- Accesso a Internet

## Scenario

Il docente vi ha chiesto di verificare la sicurezza delle vostre password e di imparare a proteggervi dal phishing.

---

## Step

### Step 1 - Google Phishing Quiz (10 min)

**Tutti gli OS (browser)**

- Andare su: <https://phishingquiz.withgoogle.com/>
- Completare il quiz e annotare il punteggio

---

### Step 2 - Analisi di un'email sospetta (10 min)

Leggete questa email e rispondete alle domande:

> **Da:** `support@paypa1.com`
> **Oggetto:** "Attività sospetta sul tuo account - Azione richiesta"
> **Testo:** "Gentile utente, abbiamo rilevato attività sospetta. Clicca qui per verificare la tua identità: `http://paypal-verify.freehosting.com/login`"

Domande:

1. Il dominio del mittente è corretto?
2. Il link punta al sito reale di PayPal?
3. Che tono usa l'email?

---

### Step 3 - Sfida: create il vostro phishing (10 min)

- Inventate un'email di phishing credibile per una piattaforma che usate (Instagram, Netflix, ecc.)
- Scambiatela con un compagno e fatevi analizzare l'email a vicenda
- Annotate i segnali di phishing trovati dal compagno

---

### Step 4 - Testare la forza delle password (10 min)

**Tutti gli OS (browser)**

- Andare su: <https://www.security.org/how-secure-is-my-password/>
- Provare queste password di esempio (**NON** usatele davvero):

| Password                  | Tempo stimato | Perché |
| ------------------------- | ------------- | ------ |
| `123456`                  | **\_**        | **\_** |
| `Password1!`              | **\_**        | **\_** |
| `IlMioCane$Mangia3Pizze!` | **\_**        | **\_** |

Poi create la vostra **passphrase** sicura:

- Pensate a una frase che ricordate facilmente
- Aggiungete numeri e simboli
- Esempio: `IlGatto$Sale2Volte!SulTetto` (27 caratteri)

---

### Step 5 - Installare un Password Manager (15 min)

#### Opzione A - Bitwarden (cloud, consigliato)

**Tutti gli OS:**

1. Andare su <https://bitwarden.com> → Crea account gratuito
2. Installare l'estensione per browser (Chrome/Firefox/Safari)
3. Impostare la master password (usate la passphrase del punto 4!)

#### Opzione B - KeePassXC (offline)

**Windows**

- Scaricare da <https://keepassxc.org/download/#windows>
- Installare con il wizard (Next → Next → Install)
- Aprire → Database → Nuovo database → impostare master password

**Ubuntu/Linux**

```bash
sudo apt install keepassxc -y
```

- Aprire KeePassXC → Database → Nuovo database → impostare master password

**macOS**

```bash
# Con Homebrew:
brew install --cask keepassxc
```

Oppure scaricare da <https://keepassxc.org/download/#mac>

- Aprire KeePassXC → Database → Nuovo database → impostare master password

---

### Step 6 - Usare il password manager (10 min)

**Tutti gli OS (identico):**

1. Aggiungere almeno **3 account** nel password manager (es. email, social, scuola)
2. Per ogni account, usare il **generatore di password integrato** (lunghezza ≥ 16 caratteri)
3. Provare il **riempimento automatico** sul browser

---

### Step 7 - Controllare data breach (5 min)

**Tutti gli OS (browser):**

- Andare su: <https://haveibeenpwned.com/>
- Inserire la vostra email (quella che usate davvero)
- Se appare in un breach → **cambiate subito la password** per quel servizio!

---

### Step 8 - Mappa del riuso password e rischio domino (15 min)

Per evitare di scrivere password vere, usate etichette fittizie come `A`, `B`, `C` per indicare se più servizi condividono la stessa password.

| Servizio                 | Etichetta password | È riusata? | Rischio se il servizio subisce un breach |
| ------------------------ | ------------------ | ---------- | ---------------------------------------- |
| Email personale          | **\_**             | **\_**     | **\_**                                   |
| Social network           | **\_**             | **\_**     | **\_**                                   |
| Marketplace / e-commerce | **\_**             | **\_**     | **\_**                                   |
| GitHub / account tecnico | **\_**             | **\_**     | **\_**                                   |
| Academy / università     | **\_**             | **\_**     | **\_**                                   |

> Obiettivo: vedere subito se una sola password compromessa può aprire la strada a più account.

---

### Step 9 - Piano di risposta a phishing o breach (15 min)

Per ogni scenario indicate le **prime 3 azioni corrette**.

| Scenario                                                      | Azione 1 | Azione 2 | Azione 3 |
| ------------------------------------------------------------- | -------- | -------- | -------- |
| Hai cliccato un link phishing ma non hai inserito credenziali | **\_**   | **\_**   | **\_**   |
| Hai inserito password su un sito phishing                     | **\_**   | **\_**   | **\_**   |
| Have I Been Pwned mostra un account coinvolto in un breach    | **\_**   | **\_**   | **\_**   |

Consegna finale del lab:

- Analisi email del passo 2 completata
- Tabella password del passo 4 compilata
- Password manager installato e con almeno 3 voci reali o di test
- Mappa riuso password del passo 8 compilata
- Piano di risposta del passo 9 compilato

---

## Output atteso

- Capacità di riconoscere email di phishing
- Password manager installato e funzionante con almeno 3 password salvate
- Controllo email su Have I Been Pwned completato
- Riuso password visualizzato in una mappa semplice e comprensibile
- Piano operativo di risposta a phishing o breach pronto all'uso

## Checkpoint

- [ ] Google Phishing Quiz completato (punteggio: \_\_/8)
- [ ] Analisi email PayPal completata (3 domande risposte)
- [ ] Passphrase personale creata
- [ ] Password manager installato (Bitwarden o KeePassXC)
- [ ] Almeno 3 password salvate nel vault
- [ ] Have I Been Pwned controllato
- [ ] Mappa del riuso password compilata
- [ ] Piano di risposta a phishing o breach compilato

## Troubleshooting rapido

| Problema                              | Soluzione                                                                                       |
| ------------------------------------- | ----------------------------------------------------------------------------------------------- |
| Bitwarden: estensione non si installa | Verificare compatibilità con il browser in uso                                                  |
| KeePassXC non si apre (Linux)         | `keepassxc --version` per verificare; reinstallare con `sudo apt install keepassxc`             |
| KeePassXC non si apre (macOS)         | Aprirlo da Applicazioni; se bloccato → Impostazioni → Privacy e sicurezza → "Apri comunque"     |
| KeePassXC non si apre (Windows)       | Eseguire come Amministratore; verificare che il download sia stato completato                   |
| Have I Been Pwned: errore             | Provare con un'altra email o riprovare più tardi                                                |
| Non so se sto riusando password       | Usare etichette fittizie A/B/C e ragionare per categorie di servizi, non scrivere password vere |

## Cleanup obbligatorio

```
# Il password manager è un miglioramento di sicurezza: NON disinstallare!
# Continuare ad usarlo per tutti i vostri account.
# Nessun cleanup necessario.
```

## Parole chiave Google (screenshot/guide)

- "Bitwarden setup guide beginner"
- "KeePassXC tutorial italiano"
- "KeePassXC install macOS"
- "how to spot phishing email"
- "have i been pwned how to use"

---

## Soluzioni

<details>
<summary>Soluzione Step 2: analisi email PayPal</summary>

1. **Il dominio del mittente è corretto?**
   - ❌ **No** - Il dominio è `paypa1.com` con il **numero "1"** invece della lettera **"l"**. Il dominio reale di PayPal è `paypal.com`.

2. **Il link punta al sito reale di PayPal?**
   - ❌ **No** - L'URL `http://paypal-verify.freehosting.com/login` è su `freehosting.com`, non su `paypal.com`. Inoltre usa `http` (senza la "s"), quindi non è nemmeno crittografato.

3. **Che tono usa l'email?**
   - ⚠️ **Urgenza** - "Attività sospetta" + "Azione richiesta" → crea paura e fretta per spingervi a cliccare senza pensare. Questo è il classico segnale numero uno del phishing.

</details>

<details>
<summary>Soluzione Step 4: risultati test password</summary>

| Password                  | Tempo stimato        | Spiegazione                                                                            |
| ------------------------- | -------------------- | -------------------------------------------------------------------------------------- |
| `123456`                  | **Istantaneo**       | È la password più comune al mondo, presente in tutti i dizionari di attacco            |
| `Password1!`              | **Pochi secondi**    | Segue un pattern prevedibile (maiuscola + numero + simbolo alla fine)                  |
| `IlMioCane$Mangia3Pizze!` | **Trilioni di anni** | Lunga (24+ caratteri), con mix di lettere, numeri e simboli, senza pattern prevedibili |

**Regole per una password forte:**

- Minimo 16 caratteri (meglio 20+)
- Usare una **passphrase** (frase con parole casuali) invece di una singola parola modificata
- Aggiungere numeri e simboli all'interno, non solo alla fine
- **Mai** riusare la stessa password su più siti

</details>

<details>
<summary>Soluzione Step 5: installazione KeePassXC - verifica per ogni OS</summary>

**Windows**

1. Scaricare l'installer `.msi` da `keepassxc.org/download`
2. Eseguire il file → Next → Next → Install → Finish
3. Aprire KeePassXC dal menu Start
4. Database → Nuovo database → scegliere nome file (es. `passwords.kdbx`) → impostare master password
5. Verifica: l'interfaccia si apre e mostra un database vuoto pronto per aggiungere password

**Ubuntu/Linux**

```bash
sudo apt install keepassxc -y
keepassxc --version
# Output: KeePassXC 2.x.x
```

Aprire da terminale con `keepassxc &` oppure dal menu delle applicazioni.

**macOS**

```bash
brew install --cask keepassxc
# Oppure: scaricare il .dmg da keepassxc.org e trascinare in Applicazioni
```

Se macOS blocca l'apertura con "app non verificata":

- Impostazioni di Sistema → Privacy e sicurezza → scorrere in basso → cliccare "Apri comunque" accanto a KeePassXC

</details>

<details>
<summary>Soluzione Step 8: come leggere la mappa del riuso password</summary>

Esempio:

| Servizio                 | Etichetta password | È riusata? | Rischio                                                   |
| ------------------------ | ------------------ | ---------- | --------------------------------------------------------- |
| Email personale          | A                  | Sì         | Altissimo: compromette reset password degli altri account |
| Social network           | B                  | No         | Medio                                                     |
| Marketplace / e-commerce | A                  | Sì         | Alto: stesso segreto dell'email                           |
| GitHub / account tecnico | C                  | No         | Alto ma isolato                                           |
| Academy / università     | A                  | Sì         | Alto: effetto domino                                      |

Interpretazione:

- Se la stessa etichetta compare in più righe, avete riuso password
- Se l'etichetta dell'email compare altrove, il rischio è ancora più grave
- L'obiettivo finale è arrivare a una tabella in cui quasi ogni riga ha un'etichetta diversa

</details>

<details>
<summary>Soluzione Step 9: prime azioni corrette dopo phishing o breach</summary>

| Scenario                                                      | Azione 1                                  | Azione 2                                      | Azione 3                                  |
| ------------------------------------------------------------- | ----------------------------------------- | --------------------------------------------- | ----------------------------------------- |
| Hai cliccato un link phishing ma non hai inserito credenziali | Chiudere la pagina                        | Eseguire controllo browser/dispositivo        | Segnalare il messaggio e cancellarlo      |
| Hai inserito password su un sito phishing                     | Cambiare subito la password sul sito vero | Revocare sessioni attive e controllare il 2FA | Cambiare anche eventuali password riusate |
| Have I Been Pwned mostra un account coinvolto in un breach    | Cambiare password del servizio            | Verificare se era riusata altrove             | Attivare o controllare il 2FA             |

</details>
