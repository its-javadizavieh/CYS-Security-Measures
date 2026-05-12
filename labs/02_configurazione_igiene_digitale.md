# Lab 02 - Configurazione Sicura e Igiene Digitale

## Obiettivo

Configurare il browser in modo sicuro, riconoscere email sospette, fare un audit di estensioni e permessi e attivare gli aggiornamenti automatici su **Windows**, **Ubuntu/Linux** e **macOS**.

## Durata

105-120 minuti

## Prerequisiti

- PC con **Chrome** o **Firefox** installato (qualsiasi OS)
- Accesso a Internet

## Scenario

Dovete preparare il vostro PC per l'uso quotidiano sicuro: browser protetto, aggiornamenti attivi, consapevolezza sulle email.

---

## Step

### Step 1 - Verificare la versione del browser (5 min)

Le istruzioni sono **uguali su tutti gli OS** (il browser è lo stesso):

**Chrome**

- Menu (⋮) → Guida → Informazioni su Google Chrome → verificare che sia aggiornato

**Firefox**

- Menu (☰) → Guida → Informazioni su Firefox → verificare che sia aggiornato

---

### Step 2 - Configurare la privacy del browser (10 min)

**Chrome (tutti gli OS)**

- Impostazioni → Privacy e sicurezza → Cookie di terze parti → **Blocca cookie di terze parti**
- Impostazioni → Privacy e sicurezza → Sicurezza → **Protezione avanzata** (attivare)

**Firefox (tutti gli OS)**

- Impostazioni → Privacy e sicurezza → Protezione antitracciamento → **Restrittiva**
- Impostazioni → Privacy e sicurezza → sezione "Protezione ingannevole" → tutto attivo

---

### Step 3 - Verificare HTTPS (5 min)

Su qualsiasi OS e browser:

1. Visitare `https://www.google.com` → notare il lucchetto 🔒 nella barra degli indirizzi
2. Visitare `http://neverssl.com` → notare l'assenza del lucchetto e l'avviso "Non sicuro"

> Il lucchetto indica che la connessione è crittografata (HTTPS/TLS). NON indica che il sito sia sicuro al 100%.

---

### Step 4 - Riconoscere email sospette (20 min)

**Google Phishing Quiz (tutti gli OS)**

- Andare su: <https://phishingquiz.withgoogle.com/>
- Completare il quiz (circa 10 minuti)
- Annotare il punteggio finale

**Analisi di email di esempio**

Analizzate questi scenari e decidete se sono phishing o legittimi:

| Scenario | Mittente                  | Oggetto                            | Phishing? | Perché? |
| -------- | ------------------------- | ---------------------------------- | --------- | ------- |
| A        | `security@amaz0n.com`     | "Il tuo ordine è stato bloccato"   | **\_**    | **\_**  |
| B        | `noreply@github.com`      | "New login from Chrome on Windows" | **\_**    | **\_**  |
| C        | `it-support@goooogle.com` | "URGENTE: Cambia la password ORA"  | **\_**    | **\_**  |

> **Suggerimento:** guardate attentamente il dominio del mittente e il tono del messaggio.

---

### Step 5 - Aggiornamenti automatici (20 min)

**Windows**

1. Impostazioni → Windows Update → Opzioni avanzate
2. Verificare che "Ricevi aggiornamenti per altri prodotti Microsoft" sia **attivo**
3. Cliccare "Controlla aggiornamenti" e installare tutto

Oppure da PowerShell (Amministratore):

```powershell
# Verificare lo stato degli aggiornamenti
Get-WindowsUpdate
# Se il modulo non è installato:
Install-Module PSWindowsUpdate -Force
Get-WindowsUpdate
```

**Ubuntu/Linux**

```bash
# Installare e configurare aggiornamenti automatici
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure unattended-upgrades
# Selezionare "Sì" quando richiesto

# Verificare che sia attivo
cat /etc/apt/apt.conf.d/20auto-upgrades
# Deve mostrare: APT::Periodic::Unattended-Upgrade "1";
```

**macOS**

1. Impostazioni di Sistema → Generali → Aggiornamento Software
2. Cliccare "Automatico" → attivare:
   - "Cerca aggiornamenti"
   - "Scarica nuovi aggiornamenti quando disponibili"
   - "Installa aggiornamenti macOS"

Oppure da terminale:

```bash
# Verificare lo stato degli aggiornamenti automatici
defaults read /Library/Preferences/com.apple.SoftwareUpdate AutomaticCheckEnabled
# Output: 1 = attivo

# Attivare aggiornamenti automatici
sudo defaults write /Library/Preferences/com.apple.SoftwareUpdate AutomaticCheckEnabled -bool true
sudo defaults write /Library/Preferences/com.apple.SoftwareUpdate AutomaticDownload -bool true
sudo defaults write /Library/Preferences/com.apple.SoftwareUpdate AutomaticallyInstallMacOSUpdates -bool true
```

---

### Step 6 - Audit di estensioni, password salvate e permessi siti (20 min)

Obiettivo: ridurre i rischi creati da plugin inutili, password nel browser e permessi troppo generosi.

**Chrome (tutti gli OS)**

1. Aprire `chrome://extensions/`
2. Disattivare o rimuovere le estensioni che non usate oppure che non ricordate di avere installato
3. Aprire Impostazioni → Password Manager → verificare se il browser sta salvando password
4. Aprire Impostazioni → Privacy e sicurezza → Impostazioni sito → controllare Camera, Microfono, Notifiche e Posizione

**Firefox (tutti gli OS)**

1. Aprire `about:addons`
2. Rimuovere o disattivare estensioni inutili
3. Impostazioni → Privacy e sicurezza → Accessi e password → verificare se i login vengono salvati
4. Impostazioni → Privacy e sicurezza → Permessi → controllare Notifiche, Posizione, Camera e Microfono

Compilate questa tabella:

| Elemento controllato | Stato attuale | Rischio | Azione |
| -------------------- | ------------- | ------- | ------ |
| Estensione 1         | **\_**        | **\_**  | **\_** |
| Estensione 2         | **\_**        | **\_**  | **\_** |
| Password nel browser | **\_**        | **\_**  | **\_** |
| Permesso sito        | **\_**        | **\_**  | **\_** |

---

### Step 7 - Scenario Wi-Fi pubblico e permessi app (15 min)

Per ogni scenario decidete se l'azione è sicura, da evitare o da fare con una precauzione specifica.

| Scenario                                                           | Scelta | Motivazione | Alternativa sicura |
| ------------------------------------------------------------------ | ------ | ----------- | ------------------ |
| Home banking su Wi-Fi aeroporto senza VPN                          | **\_** | **\_**      | **\_**             |
| App torcia che chiede accesso a contatti e microfono               | **\_** | **\_**      | **\_**             |
| Download di un PDF da un portale ufficiale in HTTPS                | **\_** | **\_**      | **\_**             |
| Connessione automatica a una rete Wi-Fi aperta già vista una volta | **\_** | **\_**      | **\_**             |

> L'obiettivo non è solo dire "sì" o "no", ma spiegare il motivo tecnico e il comportamento corretto.

---

### Step 8 - Checklist personale di igiene digitale (15 min)

Create un piano personale in due parti:

1. **Tre azioni immediate** da applicare oggi sul vostro dispositivo
2. **Una routine settimanale** di massimo 5 controlli da ripetere

Usate questo schema:

| Tipo      | Azione | Frequenza   | Perché |
| --------- | ------ | ----------- | ------ |
| Immediata | **\_** | Oggi        | **\_** |
| Immediata | **\_** | Oggi        | **\_** |
| Immediata | **\_** | Oggi        | **\_** |
| Routine   | **\_** | Settimanale | **\_** |
| Routine   | **\_** | Settimanale | **\_** |

Consegna finale del lab:

- Tabella email dello Step 4 compilata
- Audit estensioni/password/permessi dello Step 6 compilato
- Scenario Wi-Fi e permessi dello Step 7 compilato
- Checklist personale dello Step 8 compilata
- Almeno 2 evidenze raccolte (screenshot o note)

---

## Output atteso

- Browser configurato con cookie di terze parti bloccati e protezione anti-phishing
- Google Phishing Quiz completato
- Aggiornamenti automatici attivati sul proprio OS
- Audit di estensioni, password salvate e permessi siti completato
- Decisioni motivate su scenari Wi-Fi pubblico e permessi app
- Checklist personale di igiene digitale pronta all'uso

## Checkpoint

- [ ] Cookie di terze parti bloccati nel browser
- [ ] Protezione anti-phishing attiva (Chrome: avanzata / Firefox: restrittiva)
- [ ] Differenza HTTP vs HTTPS compresa
- [ ] Google Phishing Quiz completato (annotare punteggio: \_\_/8)
- [ ] Tabella email analizzata e compilata
- [ ] Aggiornamenti automatici attivi (Windows / Ubuntu / macOS)
- [ ] Audit estensioni/password/permessi completato
- [ ] Scenario Wi-Fi e permessi compilato
- [ ] Checklist personale completata

## Troubleshooting rapido

| Problema                                       | Soluzione                                                                       |
| ---------------------------------------------- | ------------------------------------------------------------------------------- |
| Non trovo le impostazioni privacy del browser  | Cercare "privacy" nella barra di ricerca delle impostazioni                     |
| Non trovo la gestione estensioni               | Chrome: `chrome://extensions/` · Firefox: `about:addons`                        |
| `unattended-upgrades` non si installa (Ubuntu) | Verificare connessione Internet: `ping google.com`                              |
| macOS: `defaults read` dà errore               | Il valore non è ancora impostato; usare il comando `write` per configurarlo     |
| Chrome non si aggiorna                         | Chiudere e riaprire Chrome; gli aggiornamenti richiedono un riavvio del browser |

## Cleanup obbligatorio

```
# Le impostazioni del browser sono miglioramenti di sicurezza: non serve annullarle.
# Gli aggiornamenti automatici sono best practice: lasciarli attivi.
# Nessun cleanup necessario per questo lab.
```

## Parole chiave Google (screenshot/guide)

- "Chrome block third party cookies"
- "Firefox strict tracking protection"
- "Google phishing quiz"
- "Ubuntu unattended upgrades setup"
- "macOS enable automatic updates terminal"

---

## Soluzioni

<details>
<summary>Soluzione Step 4: analisi email sospette</summary>

| Scenario | Mittente                  | Phishing? | Spiegazione                                                                                                                         |
| -------- | ------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| A        | `security@amaz0n.com`     | ✅ **Sì** | Il dominio usa il numero "0" al posto della lettera "o" → `amaz0n` invece di `amazon`. Inoltre l'oggetto crea urgenza.              |
| B        | `noreply@github.com`      | ❌ **No** | Il dominio `github.com` è corretto. L'oggetto è informativo, non urgente. Questo tipo di email è un avviso di sicurezza legittimo.  |
| C        | `it-support@goooogle.com` | ✅ **Sì** | Il dominio ha troppe "o" → `goooogle` invece di `google`. L'oggetto usa "URGENTE" per creare panico → classico segnale di phishing. |

**Come riconoscere il phishing - regole d'oro:**

1. Controllare il dominio del mittente (lettera per lettera!)
2. Diffidare dei messaggi con tono urgente ("ORA!", "IMMEDIATAMENTE!")
3. Non cliccare link senza prima passarci sopra con il mouse per vedere l'URL reale
4. Le aziende serie non chiedono mai password via email

</details>

<details>
<summary>Soluzione Step 5: verifica aggiornamenti automatici per ogni OS</summary>

**Windows**

- Percorso: Impostazioni → Windows Update → Opzioni avanzate
- "Ricevi aggiornamenti per altri prodotti Microsoft" → **Attivo**
- "Scarica aggiornamenti con connessioni a consumo" → **Attivo** (opzionale)
- Verifica: tornare su Windows Update → deve dire "Aggiornato" o mostrare aggiornamenti in corso

**Ubuntu/Linux**

```bash
cat /etc/apt/apt.conf.d/20auto-upgrades
# Output atteso:
# APT::Periodic::Update-Package-Lists "1";
# APT::Periodic::Unattended-Upgrade "1";
```

Se il file non esiste o i valori sono "0", riconfigurare:

```bash
sudo dpkg-reconfigure unattended-upgrades
```

**macOS**

```bash
# Verificare tutti i parametri
defaults read /Library/Preferences/com.apple.SoftwareUpdate AutomaticCheckEnabled
# Output: 1

defaults read /Library/Preferences/com.apple.SoftwareUpdate AutomaticDownload
# Output: 1

defaults read /Library/Preferences/com.apple.SoftwareUpdate AutomaticallyInstallMacOSUpdates
# Output: 1
```

Oppure: Impostazioni di Sistema → Generali → Aggiornamento Software → il toggle "Automatico" deve essere attivo con tutte le sotto-opzioni selezionate.

</details>

<details>
<summary>Soluzione Step 6: criteri per audit di estensioni e permessi</summary>

Segnali di rischio da cercare:

- Estensioni che non ricordate di aver installato
- Estensioni con accesso a "tutti i siti" senza una ragione chiara
- Notifiche autorizzate a siti sconosciuti o invadenti
- Password salvate nel browser senza un password manager dedicato
- Permessi come microfono, camera o posizione concessi a siti non necessari

Azioni consigliate:

- Rimuovere o disattivare plugin inutili
- Ridurre i permessi dei siti al minimo necessario
- Spostare gradualmente le password verso un password manager dedicato
- Lasciare attive solo estensioni affidabili, aggiornate e realmente utili

</details>

<details>
<summary>Soluzione Step 7: decisioni corrette sugli scenari</summary>

| Scenario                                                           | Scelta consigliata      | Motivazione                                                                     | Alternativa sicura                            |
| ------------------------------------------------------------------ | ----------------------- | ------------------------------------------------------------------------------- | --------------------------------------------- |
| Home banking su Wi-Fi aeroporto senza VPN                          | **Da evitare**          | Rete pubblica esposta a intercettazioni e attacchi di rete                      | Usare dati mobili o VPN affidabile            |
| App torcia che chiede accesso a contatti e microfono               | **Da evitare**          | I permessi richiesti non sono coerenti con la funzione dell'app                 | Cercare un'app diversa o negare i permessi    |
| Download di un PDF da un portale ufficiale in HTTPS                | **Sicuro con verifica** | HTTPS e fonte ufficiale riducono il rischio, ma va controllato anche il dominio | Scaricare solo dal sito ufficiale             |
| Connessione automatica a una rete Wi-Fi aperta già vista una volta | **Da evitare**          | Una rete aperta può essere falsificata facilmente                               | Disattivare auto-join e scegliere manualmente |

</details>

<details>
<summary>Soluzione Step 8: esempio di checklist personale</summary>

| Tipo      | Azione                                    | Frequenza   | Perché                                                  |
| --------- | ----------------------------------------- | ----------- | ------------------------------------------------------- |
| Immediata | Rimuovere 2 estensioni inutili            | Oggi        | Riduce la superficie di attacco del browser             |
| Immediata | Attivare aggiornamenti automatici         | Oggi        | Chiude vulnerabilità note senza dipendere dalla memoria |
| Immediata | Revocare notifiche a siti sconosciuti     | Oggi        | Riduce phishing e pop-up malevoli                       |
| Routine   | Controllare estensioni e download recenti | Settimanale | Verifica che non si accumulino componenti indesiderati  |
| Routine   | Verificare update OS e browser            | Settimanale | Mantiene il dispositivo allineato con le patch          |

</details>
