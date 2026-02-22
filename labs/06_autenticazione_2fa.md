# Lab 06 — Autenticazione a Due Fattori (2FA)

## Obiettivo

Configurare il 2FA con un'app TOTP su un account di test, capire i codici di recupero e provare WebAuthn. Le istruzioni sono valide per **Windows**, **Ubuntu/Linux** e **macOS** (il lab è prevalentemente basato su browser e smartphone).

## Durata

60 minuti

## Prerequisiti

- Smartphone con app TOTP:
  - **Android**: Google Authenticator o Aegis Authenticator (Play Store)
  - **iOS**: Google Authenticator o Raivo OTP (App Store)
- Account **GitHub** di test (creare un account gratuito se non ne avete uno)
- Browser aggiornato (qualsiasi OS)

## Scenario

La scuola ha deciso che tutti gli account devono avere il 2FA attivato. Dovete configurarlo e testarlo.

---

## Step

### Step 1 — Installare l'app TOTP sul telefono (10 min)

- **Android**: Play Store → cercare "Google Authenticator" o "Aegis Authenticator" → Installa
- **iOS**: App Store → cercare "Google Authenticator" → Installa

> Aegis (Android) è open-source e permette di esportare i codici come backup – consigliato!

---

### Step 2 — Attivare il 2FA su GitHub (25 min)

**Questi passaggi sono identici su Windows, Ubuntu e macOS (si usa il browser).**

**2a. Accedere a GitHub**

- Andare su <https://github.com> e fare login

**2b. Aprire le impostazioni di sicurezza**

- Cliccare sulla foto profilo (in alto a destra) → **Settings**
- Menu a sinistra: **Password and authentication**
- Sezione: Two-factor authentication → Cliccare **"Enable"**

**2c. Scansionare il QR code**

- Selezionare "Set up using an app"
- Aprire l'app TOTP sul telefono → scansionare il QR code mostrato sullo schermo
- L'app inizierà a generare codici da 6 cifre che cambiano ogni 30 secondi

**2d. Inserire il codice**

- Copiare il codice da 6 cifre dall'app → inserirlo su GitHub
- Se il codice è corretto → 2FA attivato! ✓

**2e. Salvare i codici di recupero**

- GitHub mostra dei **recovery codes** — codici monouso per accedere se perdete il telefono
- **Copiarli e salvarli in un posto sicuro:**

| Dove salvarli | ✅ / ❌ |
|---------------|---------|
| Password manager (Bitwarden/KeePassXC) | ✅ Consigliato |
| Foglio stampato in cassaforte | ✅ OK |
| File di testo sul PC | ⚠️ Accettabile se il disco è cifrato |
| Solo sul telefono | ❌ Se perdete il telefono, perdete anche i codici! |

---

### Step 3 — Testare il 2FA (15 min)

**3a. Logout e login con TOTP**

1. Fare logout da GitHub
2. Fare login con username + password
3. GitHub chiederà il codice TOTP → inserirlo dall'app
4. Accesso riuscito ✓

**3b. Testare un codice di recupero**

1. Fare logout di nuovo
2. Al momento dell'autenticazione, cliccare **"Use a recovery code"**
3. Inserire uno dei codici salvati al punto 2e
4. Accesso riuscito ✓
5. ⚠️ Quel codice ora è **usato** — non funzionerà più!

---

### Step 4 — Provare WebAuthn (10 min)

**Tutti gli OS (browser):**

1. Andare su: <https://webauthn.io>
2. Inserire un nome di test → cliccare **"Register"**
3. Il browser chiederà di confermare con:

| OS | Metodo di verifica |
|----|-------------------|
| 🪟 Windows | Windows Hello (PIN, impronta, riconoscimento facciale) |
| 🐧 Ubuntu/Linux | PIN del browser o chiave hardware se disponibile |
| 🍎 macOS | Touch ID o password del sistema |

1. Dopo la registrazione → cliccare **"Authenticate"**
2. Login riuscito senza password! ✓

> WebAuthn/FIDO2 è il futuro dell'autenticazione: niente password, niente phishing.

---

## Output atteso

- App TOTP installata e funzionante
- 2FA attivato su GitHub con codice TOTP
- Recovery code testato (monouso)
- Demo WebAuthn completata

## Checkpoint

- [ ] App TOTP installata sul telefono
- [ ] 2FA attivato su GitHub
- [ ] Login con TOTP verificato
- [ ] Recovery codes salvati in posto sicuro
- [ ] Un recovery code testato e usato
- [ ] Demo WebAuthn su webauthn.io completata

## Troubleshooting rapido

| Problema | Soluzione |
|----------|----------|
| Codice TOTP "non valido" | Verificare che l'ora del telefono sia automatica (Impostazioni → Data e ora → Automatica) |
| QR code non si scansiona | Usare l'opzione "inserisci codice manualmente" (inserire la chiave segreta a mano) |
| Recovery code rifiutato | Controllare di non aver inserito spazi extra; i codici sono monouso |
| WebAuthn non funziona (Linux) | Provare con Chrome (supporto migliore); su Firefox serve about:config → `security.webauth.webauthn` = true |
| WebAuthn non funziona (Windows) | Verificare che Windows Hello sia configurato (Impostazioni → Account → Opzioni di accesso) |
| WebAuthn non funziona (macOS) | Verificare di usare Safari o Chrome aggiornato; Touch ID deve essere configurato |

## Cleanup obbligatorio

```
# Se volete disattivare il 2FA dopo il lab:
# GitHub → Settings → Password and authentication → Disable 2FA
#
# Consiglio: LASCIATE il 2FA attivo su GitHub! È un miglioramento di sicurezza.
# Eliminate la voce di test da WebAuthn.io (nessuna azione necessaria — è solo una demo).
```

## Parole chiave Google (screenshot/guide)

- "GitHub enable two factor authentication"
- "Google Authenticator setup guide"
- "WebAuthn demo try passwordless"
- "Windows Hello setup"
- "macOS Touch ID for web authentication"

---

## Soluzioni

<details>
<summary>Soluzione Step 2e: dove salvare i recovery codes</summary>

**Metodo consigliato: Password Manager**

1. Aprire Bitwarden o KeePassXC
2. Cercare la voce "GitHub" (o crearla)
3. Nelle note o in un campo personalizzato, incollare tutti i recovery codes
4. Salvare

**Esempio di recovery codes (finti):**

```
abc12-def34
ghi56-jkl78
mno90-pqr12
stu34-vwx56
```

**Regole:**

- Ogni codice funziona **una sola volta**
- Dopo averlo usato, è "bruciato" — cancellatelo dalla lista
- Se li esaurite tutti → generate dei nuovi da GitHub (Settings → 2FA → View recovery codes)

</details>

<details>
<summary>Soluzione Step 3b: cosa succede se si perde il telefono senza recovery codes</summary>

**Scenari possibili:**

| Hai... | Puoi accedere? | Come? |
|--------|---------------|-------|
| Recovery codes salvati | ✅ Sì | Usare un recovery code al login |
| Altro dispositivo con stessa app TOTP | ✅ Sì | Usare il codice dall'altro dispositivo |
| Niente di tutto ciò | ❌ Molto difficile | Contattare il supporto GitHub con prove d'identità (può richiedere settimane) |

**Lezione:** salvare SEMPRE i recovery codes al momento dell'attivazione del 2FA!

</details>

<details>
<summary>Soluzione Step 4: confronto metodi 2FA per sicurezza</summary>

| Metodo | Sicurezza | Resistente al phishing? | Praticità |
|--------|-----------|------------------------|-----------|
| SMS (codice via messaggio) | ⭐⭐ | ❌ No (SIM swapping) | ⭐⭐⭐⭐⭐ |
| App TOTP (Google Authenticator) | ⭐⭐⭐⭐ | ❌ No (si può copiare) | ⭐⭐⭐⭐ |
| Chiave hardware FIDO2 (YubiKey) | ⭐⭐⭐⭐⭐ | ✅ Sì | ⭐⭐⭐ |
| WebAuthn/Passkey (biometria) | ⭐⭐⭐⭐⭐ | ✅ Sì | ⭐⭐⭐⭐ |

**Perché FIDO2/WebAuthn sono i più sicuri?**

- La chiave privata non lascia mai il dispositivo
- L'autenticazione è legata al dominio del sito → se un sito di phishing cercasse di usarla, **non funzionerebbe**
- Nessun codice da copiare = nessun codice da rubare

</details>
