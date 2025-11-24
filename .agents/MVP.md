## Dane Aplikacji

Aplikacja: EggCounter
Platforma: Mobile app

## Generalna idea
EggCounter to mobilna aplikacja, która w czasie rzeczywistym zlicza jaja w kurniku za pomocą kamery i lekkiego modelu AI działającego offline na urządzeniu. Użytkownik kieruje aparat na gniazda lub tace, a aplikacja wyświetla duży, stabilizowany licznik oraz ramki wykrytych jaj. Jednym tapnięciem zapisuje wynik jako “sesję” z datą, lokalizacją (np. Kurnik A, rząd 3) i opcjonalną notatką. Prosty interfejs i gotowe wzorce UX pozwalają na szybkie wdrożenie oraz natychmiastowe użycie w terenie.

### Stack technologiczny
- Frontend: Flutter (Dart) + pakiety: camera, tflite_flutter, flutter_isolate (odciążenie wątku UI), provider/riverpod (stan), sqflite (lokalna baza).
- AI on-device: TFLite (YOLOv8n/EfficientDet-Lite, INT8, NMS), delegaty: NNAPI (Android), Metal (iOS).
- Backend (MVP): Brak serwera. Dane lokalne w SQLite. (Opcjonalnie analityka/awarie: Firebase Analytics/Crashlytics).
- Dystrybucja modelu: Jako asset w aplikacji (aktualizacja przez update w sklepach w MVP).

### Ogólny pomysł
Aplikacja do ultraszybkiego, wiarygodnego zliczania jaj w kurnikach bez ręcznego dotykania tac i notowania wyników na kartce. Działa offline, w trudnych warunkach oświetleniowych, zapewniając stabilny licznik i możliwość ręcznej korekty wyniku. Sesje są zapisywane lokalnie i można je eksportować do CSV/PDF. Całość opiera się na gotowych patternach: Live View z licznikem, duży przycisk “Zapisz”, lista historii i prosty ekran ustawień.

### Docelowi użytkownicy
- Mikro i mali hodowcy drobiu (od kilkunastu do kilku tysięcy kur), farmy rodzinne, spółdzielnie.
- Kierownicy zmian i pracownicy zbierający jaja w większych zakładach.
- Dostawcy sprzętu drobiarskiego (resellerzy) szukający prostej aplikacji dodanej do oferty.
- Rynek: agtech/food production, segment narzędzi do inwentaryzacji i kontroli produkcji.

### Problem użytkownika
- Problemem jest czasochłonność i podatność na błędy przy ręcznym liczeniu oraz przepisywaniu wyników (papier, notatniki, zdjęcia bez automatycznego zliczania).
- Różne warunki oświetlenia, brud, częściowe przysłonięcia utrudniają dokładne liczenie, a raportowanie bywa niespójne.
- EggCounter rozwiązuje to przez on-device AI: w czasie rzeczywistym wykrywa jaja i pokazuje stabilny wynik, pozwala jednym tapnięciem zapisać sesję, a prosty CSV/PDF eliminuje przepisywanie. Ręczna korekta (+/–) domyka 100% dokładności wtedy, gdy AI się myli.

### Zakres MVP
**Zawiera:**
- Podgląd obrazu z aparatu (live view) z licznikem jaj w czasie rzeczywistym.
- Wykrywanie i zliczanie jaj na pojedynczej klatce (zliczenie “na zdjęciu” po tapnięciu), stabilizacja wyniku (uśrednianie/filtracja i próg ufności).
- Ręczna korekta wyniku (+/–) przed zapisaniem.
- Zapis “sesji” lokalnie: liczba jaj, data i czas, nazwa lokalizacji/kurnika, opcjonalna notatka, miniatura kadru (opcjonalnie).
- Historia sesji (lista, filtrowanie po dacie/lokalizacji), podstawowe statystyki dzienne/tygodniowe.
- Prosty export CSV (data, kurnik, liczba, notatka). Udostępnianie systemowe.
- Wskazówki w kadrze (overlay): stabilizuj ujęcie, włącz latarkę, trzymaj 30–80 cm od tac.
- Ustawienia: czułość modelu (threshold), latarka/torch, język PL/EN, prywatność (domyślnie: nie zachowuj zdjęć – tylko miniatury lub wyłącz).
- Działanie offline w 100%.

**Nie zawiera:**
- Brak logowania, kont użytkowników, chmury i synchronizacji między urządzeniami.
- Brak zdalnego zarządzania flotą urządzeń i multi-user roles.
- Brak rozpoznawania pęknięć/jakości skorupek/kolorów/klas wagowych.
- Brak śledzenia obiektów między klatkami podczas panoramowania (liczenie zapisywane jest z pojedynczego ujęcia).
- Brak integracji z kamerami CCTV/IoT, wagami, ERP.
- Brak geolokalizacji GPS i map.
- Brak zaawansowanych raportów, wykresów trendów, alertów i powiadomień.
- Brak aktualizacji modelu “over-the-air” (w MVP model aktualizowany z wersją aplikacji).

### Monetyzacja
- Model: freemium + subskrypcja.
  - Free: pełna detekcja na żywo, zapisywanie do 30 sesji, podstawowy export CSV (pojedyncze dni), znak wodny w PDF.
  - Pro (subskrypcja miesięczna): nielimitowane sesje, batch export (zakres dat), eksport PDF bez znaku wodnego, nazwy wielu kurników/profilów, priorytetowe aktualizacje modeli.
- Rekomendacja: subskrypcja z prostymi progami:
  - Solo: 4,99–7,99 EUR/mies. (1 urządzenie).
  - Farm: 14,99–19,99 EUR/mies. (do 5 urządzeń).
- Uzasadnienie: koszty utrzymania i doskonalenia modeli AI, wsparcie techniczne oraz różne profile gospodarstw. Jednorazowa opłata nie pokrywa iteracji modeli i testów w terenie.

### Finalizacja
EggCounter to szybkie w implementacji, offline-first narzędzie do zliczania jaj z kamerą i AI, które redukuje błędy i skraca czas pracy w kurniku. MVP skupia się na jednym, kluczowym zadaniu: stabilnym liczeniu na pojedynczym ujęciu i prostym zapisaniu wyniku. Bez chmury i kont – minimalne tarcie wdrożenia, maksymalna użyteczność w terenie. Dalszy rozwój może obejmować chmurę, zaawansowane raporty i aktualizacje modelu OTA, ale rdzeń pozostaje prosty i gotowy do szybkiej produkcji.