# 🚀 SIGNAL BOUNTIES — INSTRUKCJA INSTALACJI
### Napisana jak dla 5-latka. Krok po kroku. Nic nie pomijaj.

---

# 📋 CO BĘDZIESZ ROBIĆ (skrót)

```
KROK 1 — Zbierz klucze i konta (30 min)
KROK 2 — Wrzuć kontrakt na blockchain przez stronę (20 min)
KROK 3 — Wrzuć backend na VPS przez WinSCP (20 min)
KROK 4 — Wrzuć frontend na Vercel przez GitHub (15 min)
KROK 5 — Połącz wszystko i testuj (20 min)
```

**Razem: około 1,5 godziny**

---

# ═══════════════════════════════════════════
# KROK 1 — ZBIERZ KLUCZE I KONTA
# ═══════════════════════════════════════════

## 1A — Portfel MetaMask dla backendu

> ⚠️ Stwórz NOWY portfel specjalnie do tego projektu. Nie używaj głównego!

1. Otwórz MetaMask w przeglądarce
2. Kliknij swoje imię/adres na górze
3. Kliknij **"+ Add account or hardware wallet"**
4. Kliknij **"Add a new account"**
5. Nazwa: "Signal Bounties Backend"
6. Kliknij **Create**
7. Teraz ZAPISZ klucz prywatny:
   - Kliknij 3 kropki (**⋮**) przy nowym koncie
   - Kliknij **"Account details"**
   - Kliknij **"Show private key"**
   - Wpisz hasło MetaMask
   - Skopiuj klucz (wygląda: `0xabc123...`) — zapisz w Notatniku!

---

## 1B — Dodaj Arc Testnet do MetaMask

1. W MetaMask kliknij nazwę sieci (zazwyczaj "Ethereum Mainnet") u góry
2. Kliknij **"Add a custom network"** (lub "Add network manually")
3. Wypełnij DOKŁADNIE tak:

```
Nazwa sieci:     Arc Testnet
Nowy adres RPC:  https://rpc.testnet.arc.network
ID łańcucha:     5042002
Symbol waluty:   USDC
Adres blockexp:  https://testnet.arcscan.app
```

4. Kliknij **Save / Zapisz**
5. Przełącz na tę sieć klikając ją na liście

---

## 1C — Zdobądź darmowe testowe USDC

> USDC testnet to nie prawdziwe pieniądze — to tylko do testów, dostaniesz je za darmo

1. Wejdź na: **https://faucet.circle.com**
2. Z listy wybierz: **"Arc Testnet"**
3. Wklej adres portfela backend (ten nowy z kroku 1A)
4. Kliknij **"Request"**
5. Poczekaj 30 sekund → dostaniesz ~10 USDC testnet

**Powtórz to samo dla swojego GŁÓWNEGO portfela** (żebyś mógł testować tworzenie tasków)

---

## 1D — Stwórz projekt Firebase

**Po co?** Firebase to baza danych która trzyma taski i synchronizuje je na żywo między backendem a frontendem.

1. Wejdź na: **https://console.firebase.google.com**
2. Kliknij **"Create a project"** (lub "Utwórz projekt")
3. Nazwa projektu: `signal-bounties` → kliknij **Continue**
4. Google Analytics: możesz wyłączyć → kliknij **Create project**
5. Poczekaj chwilę... kliknij **Continue**

**Włącz Firestore (bazę danych):**

6. W lewym menu kliknij **"Firestore Database"** (Build → Firestore)
7. Kliknij **"Create database"**
8. Wybierz **"Start in production mode"** → kliknij **Next**
9. Wybierz lokalizację: **europe-west1** → kliknij **Enable**
10. Poczekaj aż się stworzy (10-20 sekund)

**Ustaw reguły bezpieczeństwa:**

11. Kliknij zakładkę **"Rules"** (u góry w Firestore)
12. Usuń wszystko co tam jest i wklej to:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /tasks/{taskId} {
      allow read: if true;
      allow write: if false;
    }
  }
}
```
13. Kliknij **"Publish"**

**Pobierz klucz Admin SDK (dla backendu):**

14. Kliknij ikonkę koła zębatego ⚙️ → **"Project settings"**
15. Kliknij zakładkę **"Service accounts"**
16. Kliknij niebieski przycisk **"Generate new private key"**
17. Kliknij **"Generate key"** w popup
18. Pobierze się plik JSON — **otwórz go w Notatniku**
19. Zapisz sobie:
    - wartość `"client_email"` (wygląda jak: `firebase-adminsdk-xxxxx@signal-bounties-xxxxx.iam.gserviceaccount.com`)
    - wartość `"private_key"` (długi tekst zaczynający się od `-----BEGIN PRIVATE KEY-----`)

**Pobierz config dla frontendu:**

20. Wróć do Project settings ⚙️
21. Kliknij zakładkę **"General"**
22. Przewiń na dół do sekcji **"Your apps"**
23. Kliknij ikonkę `</>` (Web)
24. Wpisz nazwę aplikacji: `signal-bounties-web` → kliknij **Register app**
25. Zobaczysz kod JavaScript z `firebaseConfig` — **skopiuj go i zapisz w Notatniku!**

Wygląda tak:
```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "signal-bounties-xxxxx.firebaseapp.com",
  projectId: "signal-bounties-xxxxx",
  storageBucket: "signal-bounties-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

---

## 1E — Klucze AI

**Claude (Anthropic):**
1. Wejdź na: **https://console.anthropic.com**
2. Kliknij **"API Keys"** w menu
3. Kliknij **"Create Key"**
4. Skopiuj klucz (zaczyna się od `sk-ant-...`) — zapisz!

**Groq (darmowy, fallback):**
1. Wejdź na: **https://console.groq.com**
2. Zaloguj się (możesz przez Google)
3. Kliknij **"API Keys"** → **"Create API Key"**
4. Skopiuj klucz (zaczyna się od `gsk_...`) — zapisz!

**Gemini (Google, darmowy, fallback):**
1. Wejdź na: **https://aistudio.google.com/app/apikey**
2. Kliknij **"Create API Key"**
3. Skopiuj klucz (zaczyna się od `AIza...`) — zapisz!

---

# ═══════════════════════════════════════════
# KROK 2 — DEPLOY SMART CONTRACTU NA BLOCKCHAIN
# ═══════════════════════════════════════════

> Smart contract to program który żyje na blockchain i trzyma USDC w escrow

## 2A — Otwórz Remix IDE

1. Wejdź na: **https://remix.ethereum.org**
2. Zamknij wszystkie popup/okna powitalne jeśli się pojawią

## 2B — Wklej kontrakt

1. W lewym panelu kliknij ikonkę pliku 📄 (File Explorer)
2. Kliknij przycisk **"+"** (New File)
3. Wpisz nazwę: `SignalBounties.sol` → Enter
4. Otwórz plik `contracts/SignalBounties.sol` z tego ZIP w Notatniku
5. Skopiuj CAŁĄ zawartość (Ctrl+A, Ctrl+C)
6. Wklej do Remix (Ctrl+V)

## 2C — Skompiluj kontrakt

1. Kliknij ikonkę **"Solidity Compiler"** w lewym panelu (wygląda jak S lub ♦)
2. W polu **"Compiler"** wybierz wersję: **0.8.20**
3. Kliknij niebieski przycisk **"Compile SignalBounties.sol"**
4. Poczekaj chwilę
5. ✅ Jeśli widzisz zielony ptaszek — OK!
6. ❌ Jeśli widzisz błędy — sprawdź czy wkleiłeś cały kod

## 2D — Podłącz MetaMask do Remix

1. Kliknij ikonkę **"Deploy & Run Transactions"** w lewym panelu (wygląda jak strzałka w górę)
2. W polu **"ENVIRONMENT"** wybierz: **"Injected Provider - MetaMask"**
3. MetaMask otworzy się i zapyta o połączenie → kliknij **Connect**
4. Upewnij się że MetaMask jest na sieci **Arc Testnet** (sprawdź u góry MetaMask)
5. W polu **"CONTRACT"** wybierz: **SignalBounties**

## 2E — Zadeploy kontrakt

1. Kliknij pomarańczowy przycisk **"Deploy"**
2. MetaMask otworzy się i pokaże transakcję → kliknij **Confirm**
3. Poczekaj 5-10 sekund
4. Na dole Remix zobaczysz nową sekcję **"Deployed Contracts"**
5. Kliknij strzałkę przy nazwie kontraktu
6. Zobaczysz adres — wygląda: `0x1234...abcd`
7. **SKOPIUJ TEN ADRES I ZAPISZ W NOTATNIKU!** → to `CONTRACT_ADDRESS`

---

# ═══════════════════════════════════════════
# KROK 3 — INSTALACJA BACKENDU NA VPS
# ═══════════════════════════════════════════

## 3A — Skopiuj pliki przez WinSCP

1. Otwórz **WinSCP**
2. Połącz się z VPS (Host: IP VPS, Port: 22, User: root)
3. Po prawej stronie (VPS) przejdź do folderu: `/root/`
4. Stwórz nowy folder: `signal-bounties`
5. Wejdź do `/root/signal-bounties/`
6. Skopiuj przez drag & drop z lewej (komputer) na prawą (VPS):
   - Folder `backend/` → do `/root/signal-bounties/backend/`

## 3B — Stwórz plik .env na VPS

1. W WinSCP wejdź do: `/root/signal-bounties/backend/`
2. Kliknij prawym przyciskiem → **"New"** → **"File"**
3. Nazwa: `.env` (z kropką na początku!)
4. Kliknij **Edit**
5. Wklej poniższy tekst i uzupełnij WSZYSTKIE wartości:

```
ARC_RPC_URL=https://rpc.testnet.arc.network
ARC_CHAIN_ID=5042002
CONTRACT_ADDRESS=WKLEJ_ADRES_KONTRAKTU_Z_KROKU_2E

BACKEND_PRIVATE_KEY=WKLEJ_KLUCZ_PRYWATNY_Z_KROKU_1A

ANTHROPIC_API_KEY=WKLEJ_KLUCZ_Z_KROKU_1E
GROQ_API_KEY=WKLEJ_KLUCZ_Z_KROKU_1E
GEMINI_API_KEY=WKLEJ_KLUCZ_Z_KROKU_1E

FIREBASE_PROJECT_ID=WKLEJ_projectId_Z_KROKU_1D
FIREBASE_CLIENT_EMAIL=WKLEJ_client_email_Z_KROKU_1D
FIREBASE_PRIVATE_KEY="WKLEJ_private_key_Z_KROKU_1D"

PORT=3001
FRONTEND_URL=https://signal-bounties.vercel.app
```

> ⚠️ FIREBASE_PRIVATE_KEY musi być w cudzysłowach! Jeśli w środku są znaki `\n` — zostaw je tak jak są.

6. Zapisz plik (Ctrl+S)

## 3C — Zainstaluj i uruchom przez PuTTY

1. Otwórz **PuTTY** i połącz się z VPS
2. Wpisz komendy jedna po drugiej (po każdej Enter):

```bash
cd /root/signal-bounties/backend
```

```bash
npm install
```
> Poczekaj aż się zainstaluje — może potrwać 1-2 minuty

```bash
mkdir -p /root/signal-bounties/logs
```

```bash
pm2 start ecosystem.config.js
```

```bash
pm2 save
```

3. Sprawdź czy działa:
```bash
pm2 logs signal-bounties --lines 20
```

4. Powinieneś zobaczyć coś takiego:
```
🚀 Signal Bounties Backend on port 3001
🔑 Wallet: 0x...
[Chain] Listening for TaskCreated events...
```

5. Jeśli widzisz powyższe → **BACKEND DZIAŁA!** ✅

---

# ═══════════════════════════════════════════
# KROK 4 — FRONTEND NA VERCEL (przez GitHub)
# ═══════════════════════════════════════════

## 4A — Uzupełnij konfigurację w pliku index.html

1. Otwórz plik `frontend/index.html` z ZIP w **Notatniku** (lub VS Code)
2. Znajdź na górze sekcję `const CFG = {` (około linii 350)
3. Uzupełnij wartości:

```javascript
const CFG = {
  BACKEND:  'http://TWOJE_IP_VPS:3001',   // ← np. http://123.45.67.89:3001
  CONTRACT: '0xADRES_Z_KROKU_2E',          // ← adres kontraktu z Remix
  CHAIN_ID: 5042002,                        // ← zostaw bez zmian
  RPC:      'https://rpc.testnet.arc.network', // ← zostaw bez zmian
  USDC:     '0x3600000000000000000000000000000000000000', // ← zostaw
  EXPLORER: 'https://testnet.arcscan.app', // ← zostaw bez zmian
  FB: {
    apiKey:            'WKLEJ_apiKey_Z_KROKU_1D',
    authDomain:        'WKLEJ_authDomain_Z_KROKU_1D',
    projectId:         'WKLEJ_projectId_Z_KROKU_1D',
    storageBucket:     'WKLEJ_storageBucket_Z_KROKU_1D',
    messagingSenderId: 'WKLEJ_messagingSenderId_Z_KROKU_1D',
    appId:             'WKLEJ_appId_Z_KROKU_1D',
  }
};
```

4. Zapisz plik

## 4B — Wrzuć na GitHub

1. Wejdź na: **https://github.com**
2. Kliknij zielony przycisk **"New"** (nowe repozytorium)
3. Nazwa: `signal-bounties` → zaznacz **"Public"** → kliknij **"Create repository"**
4. Na stronie nowego repo kliknij **"uploading an existing file"**
5. Przeciągnij plik `frontend/index.html` do okna uploadu
6. Kliknij zielony przycisk **"Commit changes"**

## 4C — Deploy na Vercel

1. Wejdź na: **https://vercel.com**
2. Kliknij **"Sign up"** → zaloguj się przez GitHub (kliknij "Continue with GitHub")
3. Kliknij **"Add New Project"**
4. Znajdź na liście repo `signal-bounties` → kliknij **"Import"**
5. W ustawieniach:
   - Framework Preset: **Other**
   - Root Directory: `/` (zostaw domyślne)
6. Kliknij **"Deploy"**
7. Poczekaj 1-2 minuty
8. Zobaczysz zielony ptaszek i link: **`signal-bounties.vercel.app`**
9. **KLIKNIJ TEN LINK** → Twoja strona jest live! 🎉

---

# ═══════════════════════════════════════════
# KROK 5 — TEST — CZY WSZYSTKO DZIAŁA
# ═══════════════════════════════════════════

## 5A — Sprawdź backend

W PuTTY wpisz:
```bash
curl http://localhost:3001/api/health
```

Powinno zwrócić:
```json
{"status":"ok","wallet":"0x...","contract":"0x...","chain":"Arc Testnet"}
```

✅ Jeśli tak — backend działa!

## 5B — Przetestuj pełny flow

1. Otwórz **https://signal-bounties.vercel.app** w przeglądarce
2. Kliknij **"CONNECT WALLET"** (prawy górny róg)
3. MetaMask otworzy się → kliknij **Connect**
4. MetaMask zapyta o zmianę sieci na Arc Testnet → kliknij **Switch**
5. Wypełnij formularz po prawej:
   - Tytuł: `Analiza BTC na dziś`
   - Opis: `Przeanalizuj aktualną sytuację BTC/USDT. Podaj poziomy wsparcia i oporu.`
   - Nagroda: `1`
6. Kliknij **"DEPLOY TASK + LOCK USDC"**
7. MetaMask zapyta dwa razy:
   - Pierwsza transakcja: **Approve USDC** → kliknij Confirm
   - Druga transakcja: **Create Task** → kliknij Confirm
8. W terminalu po lewej zobaczysz logi na żywo
9. Po ~10-30 sekundach task zmieni status z **PROCESSING** na **COMPLETED**
10. Kliknij task na liście → zobaczysz wynik agenta AI
11. Kliknij **"APPROVE & RELEASE USDC"** → MetaMask → Confirm
12. Status zmieni się na **APPROVED** ✅

**Gratulacje! Aplikacja działa end-to-end!** 🏆

---

# 🆘 CO ZROBIĆ GDY COŚ NIE DZIAŁA

## Problem: "Failed to fetch" w przeglądarce
→ Backend nie odpowiada. Sprawdź w PuTTY:
```bash
pm2 status
pm2 logs signal-bounties --lines 30
```

## Problem: MetaMask nie łączy się
→ Upewnij się że Arc Testnet jest dodany (Krok 1B)

## Problem: "Insufficient funds"
→ Wróć do faucet: https://faucet.circle.com i pobierz USDC

## Problem: Agent nie przetwarza taska
→ W PuTTY wpisz (zastąp 1 numerem taska):
```bash
curl -X POST http://localhost:3001/api/tasks/1/process
```

## Problem: Firebase "permission denied"
→ Wróć do Kroku 1D punkt 11-13 i sprawdź reguły

---

# 📦 KOMENDY PM2 (codzienne użycie)

```bash
pm2 status                          # czy backend działa?
pm2 logs signal-bounties            # logi na żywo
pm2 reload signal-bounties          # restart (ZAWSZE reload, nie restart!)
pm2 stop signal-bounties            # zatrzymaj
```

---

# 🔗 WSZYSTKIE LINKI W JEDNYM MIEJSCU

| Co | Link |
|----|------|
| Twoja aplikacja | https://signal-bounties.vercel.app |
| Arc Explorer | https://testnet.arcscan.app |
| Darmowe USDC | https://faucet.circle.com |
| Remix (kontrakt) | https://remix.ethereum.org |
| Firebase Console | https://console.firebase.google.com |
| Vercel Dashboard | https://vercel.com/dashboard |
| Submit hackathon | https://forms.gle/ok3Gr9zhmHnApvK48 |
| Canteen Discord | https://discord.gg/TGnyfKh23V |
| Arc Discord | https://discord.com/invite/buildonarc |
| Passphrase wejścia | **SITEx1313** |

---

# ✅ CHECKLIST PRZED SUBMITEM DO HACKATHONU

- [ ] Kontrakt zadeploy owany na Arc Testnet
- [ ] Backend działa na VPS (`pm2 status` pokazuje "online")
- [ ] Frontend dostępny na Vercel
- [ ] End-to-end flow przetestowany (create → agent → approve)
- [ ] Demo video nagrane na Loom (max 3 minuty)
- [ ] GitHub repo publiczne
- [ ] Dołączony do Canteen Discord
- [ ] Dołączony do Arc Discord (z hasłem SITEx1313)
- [ ] Form hackathonu wypełniony: forms.gle/ok3Gr9zhmHnApvK48

---

*Signal Bounties — Agora Agents Hackathon 2026*
*Canteen × Circle × Arc*
