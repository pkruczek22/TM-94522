# 📱 Mobile Automation & Cloud-Ready Testing Suite

**Prowadzący:** mgr Mariusz Dworniczak 
**Student:** Paweł Kruczek
**Numer Albumu:** 94522

---

## 🏗️ Architektura Projektu (Marketing & Tech Stack)
Ten projekt to kompletny ekosystem testowy oparty na podejsciu **Cloud-Ready / Headless**. Zamiast polegać na ciężkich emulatorach, skupiamy się na narzędziach CLI, analizie statycznej, konteneryzacji (Docker) oraz automatyzacji procesów (Pipeline). 

**Główne technologie:**
* **Język:** Python 3.10+
* **Automatyzacja UI:** Appium 2.x (Mobile Engine)
* **Infrastruktura:** Docker & Docker Compose
* **Raportowanie:** Allure Framework
* **Analiza:** MobSF (Static Analysis) & ADB CLI

---

## 📅 PRZEBIEG LABORATORIUM (Kamienie Milowe)

### 🔹 BLOK 1: Tooling & Environment (Infrastruktura)
Przygotowanie bazy narzędziowej w modelu kontenerowym.
* **Co zrobiono:** Pobranie i konfiguracja obrazów `appium`, `android-sdk` oraz `mobsf`.
* **Wniosek:** Wykorzystanie obrazów Docker zapewnia pełną powtarzalność środowiska testowego (ang. idempotency). Eliminuje to problem "u mnie działa", izoluje zależności (różne wersje Android SDK, Node.js dla Appium) od systemu gospodarza i pozwala na błyskawiczne skalowanie infrastruktury oraz łatwą integrację z systemami CI/CD.

### 🔹 BLOK 2: Debugowanie i Analiza Statyczna (MobSF)
Zrozumienie "wnętrza" aplikacji mobilnej przed przystąpieniem do testów.
* **Co zrobiono:** Wykorzystanie MobSF do skanowania plików APK pod kątem podatności i uprawnień.
* **Wniosek:** Analiza statyczna pozwala testerowi zidentyfikować luki bezpieczeństwa (np. twardo zakodowane klucze API, niezabezpieczone uprawnienia w AndroidManifest.xml) oraz poznać strukturę aktywności aplikacji bez konieczności jej uruchamiania. Skraca to czas testów, pozwalając wykryć błędy architektury na bardzo wczesnym etapie (zgodnie z zasadą Shift Left Testing).

### 🔹 BLOK 3-4: Fundamenty Skryptowania (Python for QA)
Budowa logiki testowej w języku Python.
* **Co zrobiono:** 
    * Implementacja skryptu `selector_miner.py` wykorzystującego bibliotekę `xml.etree.ElementTree` do automatycznej ekstrakcji atrybutów (ID, text, class) z plików layoutu XML.
    * Budowa narzędzia `stability_auditor.py` do obliczania Indeksu Dominacji Klas (CDI), co pozwala ocenić stabilność selektorów i unikać kruchych testów opartych na klasach.
    * Tworzenie skryptów weryfikujących determinizm zapytań logicznych (`selector_game.py`) oraz audyt dostępności (`a11y_check.py`) pod kątem brakujących etykiet `contentDescription`.
    * Symulacja cyklu życia testu przy użyciu Mock Drivera, obejmująca walidację unikalności selektorów XPath oraz generowanie logów końcowych i raportów JSON.

### 🔹 BLOK 5-7: Hybrydowe Testowanie API (Requests & Pytest)
Weryfikacja warstwy backendowej aplikacji mobilnej.
* **Co zrobiono:** Testowanie endpointów REST (JSONPlaceholder), obsługa kodów HTTP i asercja danych JSON.
* **Wniosek:** Testowanie API pozwala wyłapać błędy zanim uruchomimy ciężkie testy UI.

### 🔹 BLOK 8: Appium UI Automation (Deep Dive)
Automatyzacja interakcji z interfejsem użytkownika.
* **Co zrobiono:** 
    * Implementacja skryptu `81_manifest_scanner.py` do audytu pliku `AndroidManifest.xml`, pozwalającego na wykrycie niebezpiecznych uprawnień (np. `READ_CONTACTS`, `RECORD_AUDIO`) oraz krytycznej flagi `debuggable="true"`[cite: 2195, 2201].
    * Stworzenie narzędzia `82_secrets_finder.py` wykorzystującego wyrażenia regularne (RegEx) do wykrywania twardo zakodowanych danych (Hardcoded Secrets), takich jak klucze API, adresy IP czy zahartowane hasła w plikach zasobów `strings.xml`[cite: 2299, 2312].
    * Przeprowadzenie analizy łańcucha dostaw (SCA) za pomocą skryptu `83_library_audit.py`, który identyfikuje znane podatności (CVE) w bibliotekach zewnętrznych (np. OkHttp, com.google.android.gms) i klasyfikuje ich groźność w skali CVSS[cite: 2383, 2397].
    * Opracowanie algorytmu scoringowego w skrypcie `84_security_scorer.py`, który automatycznie oblicza końcową ocenę bezpieczeństwa aplikacji (Security Score) na podstawie wag przypisanych do znalezionych błędów[cite: 2416, 2495].
    * Przygotowanie profesjonalnego raportu zbiorczego `85_final_audit.md` (Executive Summary) zawierającego ocenę końcową, kluczowe obszary ryzyka oraz rekomendacje naprawcze (Remediation Roadmap)[cite: 2535, 2537].

### 🔹 BLOK 9: Konteneryzacja Serwera (Docker Compose)
Izolacja silnika Appium od systemu operacyjnego.
* **Co zrobiono:** Stworzenie pliku `docker-compose.yml` zarządzającego serwerem Appium i sterownikami.

### 🔹 BLOK 10: MASTER PIPELINE (Capstone Project) 🏆
Finałowa automatyzacja całego procesu testowego.
* **Co zrobiono:** Stworzenie skryptu `pipeline.py`, który w jednym cyklu:
1. Rezerwuje zasoby i stawia infrastrukturę Docker.
2. Wykonuje testy hybrydowe (API + UI).
3. Generuje profesjonalny raport Allure z metadanymi.
4. Czyści środowisko po zakończonej pracy.

---

## 📊 Raportowanie Wyników (Allure)
Projekt wykorzystuje zaawansowane raportowanie Allure, które pozwala na:
* Śledzenie kroków testowych (`@allure.step`).
* Analizę błędów wraz z załącznikami (zrzuty ekranu, logi JSON).
* Dokumentowanie środowiska wykonawczego w sekcji **Environment**.

![dashboard allure](image.png)

---

## 🚀 Jak uruchomić cały proces?
```bash
# Wejdź do folderu finałowego
cd Artefakt10

# Uruchom wszystko jednym poleceniem
python3 pipeline.py

# Po zakończeniu zobacz raport
allure serve allure-results

 