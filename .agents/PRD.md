```markdown
# Dokument wymagań produktu (PRD) - EggCounter

## 1. Przegląd produktu

EggCounter to aplikacja mobilna (Android/iOS) służąca do automatycznego zliczania jaj na zdjęciach w środowisku produkcyjnym (fermowym, edukacyjnym lub hobbystycznym). Aplikacja analizuje obraz z kamery w czasie rzeczywistym, detektuje obiekty typu „jajko” na podstawie wbudowanego modelu AI (YOLOv8n, INT8) i umożliwia natychmiastowy zapis wyniku wraz z obsługą ręcznych korekt. Cała analiza odbywa się lokalnie na urządzeniu zapewniając prywatność, niezawodność i szybkie działanie (<=200 ms do stabilizacji). Produkt obsługuje główne scenariusze pracy: przy jajach na tacach, indywidualnych gniazdach oraz w warunkach mieszanych oświetleniowo (zgodnie z profilami A–D). Kluczowymi wartościami EggCounter są szybkość działania, prostota UX oraz łatwe raportowanie (eksport CSV/PDF), przy minimalnym narzucie na personel terenowy.

## 2. Problem użytkownika

Zliczanie dużej liczby jaj w produkcji, edukacji i pracach inwentaryzacyjnych jest zadaniem żmudnym i podatnym na błędy, szczególnie w warunkach słabego oświetlenia, zmiennego rozmieszczenia, częściowych zakryć oraz konieczności szybkiej rejestracji wyników. Tradycyjne metody (ręczne liczenie, notowanie, praca z tabelami) są czasochłonne, nieoptymalne i obarczone ryzykiem pomyłek, zwłaszcza w środowisku terenowym pod presją czasu. Użytkownicy potrzebują narzędzia skracającego proces do kilku sekund, minimalizującego pomyłki, umożliwiającego szybkie korekty oraz generowanie wymaganej dokumentacji bez posiłkowania się dodatkowymi systemami lub chmurą.

## 3. Wymagania funkcjonalne

1. Zliczanie jaj z wykorzystaniem modelu AI (YOLOv8n INT8) na urządzeniu (on-device inference).
2. Live view z automatyczną detekcją i licznikiem obiektów:
    - Overlay ramek detekcji.
    - Wyraźny licznik, wskaźnik jakości detekcji.
3. Mechanizm stabilizacji licznika (EMA, histereza, freeze po 5–8 stabilnych klatkach).
4. Obsługa auto-freeze z opcjonalnym przełącznikiem na ręczne „tap-to-capture”.
5. Haptyka przy auto-stabilizacji i zapisie wyniku.
6. Możliwość ręcznych korekt wyniku (±1, ±5) zarówno z poziomu UI jak i przycisków głośności (konfigurowalne).
7. Automatyczne włączanie latarki w słabych warunkach oświetleniowych (z zachowaniem bezpieczeństwa baterii i kompatybilności systemowej).
8. Wsparcie trybu wysokiego kontrastu (zmiana kolorystyki interfejsu).
9. Blokada usypiania ekranu podczas aktywnej sesji detekcji.
10. Zapis wyniku jako „sesja” z pełnymi metadanymi:
    - Data, godzina, lokalizacja Miejsce > Strefa > Rząd lub tagi (autouzupełnianie, szybki wybór ostatnich).
    - Liczba detekcji AI, korekty ręczne, liczba finalna, notatka użytkownika, wersja modelu/aplikacji, device_id_hash.
    - Opcjonalna miniatura (podgląd, rozmiar możliwy do konfiguracji/wyłączenia).
11. Historia sesji z możliwością filtrowania po dacie/lokalizacji, statystykami dziennymi/tygodniowymi.
12. Eksport raportów: CSV (zgodność z RFC4180, UTF-8, quoting) i PDF (PL/EN, watermark dla Free).
    - Eksport pojedynczych i batch (dla Pro).
    - Generowanie w tle, automatyczna paginacja powyżej 1k rekordów.
    - Share sheet systemowy.
    - Nazewnictwo plików: YYYYMMDD_Farm_Sessions_v2.csv/pdf z inkrementacją.
13. Ustawienia aplikacji:
    - Threshold detekcji (czułość).
    - Tryb auto-freeze/tap-to-capture.
    - Latarka z automatem/opcja ręczna.
    - Język PL/EN.
    - Prywatność: miniatury (on/off), wyczyszczenie danych, polityka offline.
    - Mapowanie przycisków głośności.
14. Model monetyzacyjny i limity:
    - Free: max. 30 sesji lifetime na urządzenie.
    - Pro/Solo: nielimitowane użytkowanie (IAP).
    - Farm: aktywacja offline (tokeny) do 5 urządzeń.
15. Mechanizmy prywatności i bezpieczeństwa:
    - Przetwarzanie danych wyłącznie lokalnie.
    - Brak przechowywania pełnych zdjęć (poza opcjonalnymi miniaturami lokalnie).
    - Device_id hashowane z lokalną solą, przechowywane bez możliwości odwrócenia.
    - Indywidualna zgoda na analitykę/crash-reporting.
    - Funkcja „wyczyść dane” całościowo usuwa lokalną bazę i miniatury (>90 dni automatyczny purge).
16. Obsługa wydajności:
    - Proces inferencji w osobnym izolacie (Flutter).
    - Adaptacyjny pipeline: rozdzielczość, liczba detekcji, throttling FPS (low: 12–15, mid: 20–30, high: 30–60).
    - NNAPI/Metal, monitorowanie temperatury urządzenia.
    - Warm-up modelu <1s.
17. Dostępność:
    - Duże hit-targey (praca w rękawicach), obsługa przycisków fizycznych, tryb wysokiego kontrastu.
18. Języki: PL i EN na start.
19. Feedback oraz diagnostyka:
    - Formularz offline (pilot/QA), eksport logów działania (bez PII).

## 4. Granice produktu

- Aplikacja nie wspiera:
    - Kont użytkownika, synchronizacji chmurowej, historii w chmurze, automatycznego backupu.
    - Rozpoznawania innych klas obiektów niż „jajko” (multi-klasowość).
    - Przetwarzania wsadowego obrazów/wideo na start (tylko pojedyncza sesja, zdjęcie).
    - Automatycznej aktualizacji modelu (brak OTA w MVP, update razem z wersją aplikacji).
    - Skalowania na tablety/desktop poza responsywnym layoutem.
    - Zaawansowanej obsługi AR, pomiaru wymiarów czy fotogrametrii.
    - Integracji z ERP/WMS.
    - Wsparcia lokalizacji innych niż PL/EN w MVP.
    - Zaawansowanych mechanizmów dostępności (poza kontrastem i dużymi hitboxami).
- Liczba obsługiwanych urządzeń Farm ograniczona do 5 na token.
- Eksport PDF ograniczony do trybu „Pro” (w wersji Free znak wodny) i ręcznego generowania (nie automatyzacja).
- Brak przechowywania pełnych zdjęć po stronie aplikacji (tylko opcjonalne miniatury, domyślnie on-device, purge >90 dni, możliwe wyłączenie).
- Wersja Free: 30 sesji lifetime, po przekroczeniu tylko podgląd i eksport wcześniejszych sesji, bez nowych pomiarów.
- Brak zaawansowanego screen readera na MVP, jedynie wysoki kontrast i duże hit-targey.
- Historia edycji sesji (audit trail) wyłącznie na poziomie sesji (nie wersjonujemy każdego mikrokroku).

## 5. Historyjki użytkowników

### Aktywności główne

#### US-001: Rozpoczęcie detekcji i liczenie w live view
- Tytuł: Detekcja jaj w czasie rzeczywistym
- Opis: Jako operator, chcę zobaczyć automatycznie liczony wynik jaj w podglądzie kamery, aby skrócić czas inwentaryzacji.
- Kryteria akceptacji:
    - Podgląd kamery z nakładką detekcji.
    - Licznik automatycznie aktualizowany.
    - Licznik freezuje się po uzyskaniu stabilizacji (≤200 ms, min. 5–8 klatek stabilnych).
    - Haptyka/komunikat wizualny po zamrożeniu wyniku.

#### US-002: Korekta wyniku (ręczna, ±1, ±5)
- Tytuł: Ręczna korekta wyniku
- Opis: Jako operator, chcę mieć możliwość korekty wyniku liczenia (dodanie/odjęcie 1 lub 5), aby uwzględnić niewidoczne lub nieprawidłowo zliczone jaja.
- Kryteria akceptacji:
    - Przycisk ±1/±5 widoczny i aktywny po zamrożeniu wyniku.
    - Długie przytrzymanie = ±5, pojedyncze tapnięcie = ±1.
    - Odzwierciedlenie zmian w wartości finalnej.

#### US-003: Użycie fizycznych przycisków głośności
- Tytuł: Korekta lub zapis przez przyciski fizyczne
- Opis: Jako operator, chcę korzystać z przycisków głośności do korekt/zapisu wyniku, aby wygodniej obsługiwać aplikację w rękawicach.
- Kryteria akceptacji:
    - Możliwość przypisania funkcji przyciskom w Ustawieniach.
    - Brak kolizji z regulacją głośności/systemowymi funkcjami (platformowo).
    - Zmiana/aktywacja poparta komunikatem w aplikacji.

#### US-004: Automatyczne włączenie latarki w słabym świetle
- Tytuł: Self-adaptive torch
- Opis: Jako operator, chcę auto-latarkę, gdy światło jest niewystarczające, by uzyskać czytelny wynik.
- Kryteria akceptacji:
    - Latarka aktywuje się, gdy lux < określonego poziomu/przy niskim kontrastcie.
    - Komunikat o aktywacji latarki.
    - Wyłączenie latarki po zakończeniu sekwencji lub manualnie przez użytkownika.

#### US-005: Zapis sesji wyniku
- Tytuł: Zapis pomiaru
- Opis: Jako operator, chcę łatwo zapisać wynik pomiaru wraz z lokalizacją oraz notatką, aby mieć raport do dalszego użytku.
- Kryteria akceptacji:
    - Formularz zapisu zawiera: lokalizację (z autosugestią), liczbę AI, korektę ręczną, liczbę final, notatkę, datę/czas, wersje soft/hardware.
    - Opcja dołączenia/wyłączenia miniatury.
    - Zapis w lokalnej bazie, natychmiastowy feedback o sukcesie.
    - Powrót do ekranu głównego po zapisie.

#### US-006: Historia sesji i filtrowanie
- Tytuł: Przegląd i filtrowanie historii
- Opis: Jako operator/manager, chcę przeglądać zapisane sesje, filtrować je po dacie/lokalizacji oraz wyświetlać podsumowania.
- Kryteria akceptacji:
    - Interfejs listy z szybkim filtrem.
    - Szczegóły i statystyki na sesję/dzień/tydzień.
    - Wgląd do notatek i metadanych każdej sesji.

#### US-007: Eksport danych do CSV/PDF
- Tytuł: Eksport raportów
- Opis: Jako manager, chcę eksportować dane z wybranego zakresu do CSV lub PDF, aby prowadzić ewidencję i raportowanie offline.
- Kryteria akceptacji:
    - Eksport jednego lub wielu wybranych (batch) sesji.
    - Pliki CSV zgodne z RFC4180, PDF w PL/EN z czytelnym podsumowaniem.
    - W trybie Free: PDF z watermarkiem.
    - Udostępnianie przez share sheet systemowy.

#### US-008: Zarządzanie lokalizacjami (Miejsce/Strefa/Rząd)
- Tytuł: Szybkie wybieranie lub dodawanie lokalizacji
- Opis: Jako operator, chcę szybko wybrać, dodać lub edytować lokalizację dla sesji bez opuszczania ekranu.
- Kryteria akceptacji:
    - Dropdown/autouzupełnianie, dostęp do ostatnio używanych lokalizacji.
    - Możliwość szybkiej edycji/dodania nowej lokalizacji inline.
    - Zmiana lokalizacji zapisuje się do historii.

#### US-009: Ustawienia prywatności
- Tytuł: Zarządzanie ustawieniami prywatności i danych
- Opis: Jako użytkownik, chcę konfigurować, czy aplikacja zapisuje miniatury, wyczyść dane i kontrolować zgodę na analitykę, by zarządzać swoją prywatnością.
- Kryteria akceptacji:
    - Włącznik/wyłącznik miniatur.
    - Przycisk „wyczyść dane” z potwierdzeniem.
    - Jasny komunikat dot. przetwarzania/analityki.
    - Brak zdjęć przetwarzanych poza urządzeniem.

#### US-010: Zmiana czułości, trybu detekcji i języka
- Tytuł: Zaawansowane ustawienia sesji
- Opis: Jako użytkownik, chcę dostosować threshold detekcji, tryb stabilizacji (auto-freeze/tap) i język interfejsu.
- Kryteria akceptacji:
    - Dedykowane sekcje w Ustawieniach aplikacji.
    - Zmiany odzwierciedlają się natychmiast w działaniu kamery/interfejsu.
    - Komunikaty o skutkach zmiany trybu.

#### US-011: Upgrade do Pro/Solo lub aktywacja Farm
- Tytuł: Rozszerzenie funkcjonalności i zarządzanie licencjami
- Opis: Jako użytkownik, chcę kupić Pro, przywrócić zakup lub aktywować licencję Farm (do 5 urządzeń).
- Kryteria akceptacji:
    - Widoczny przycisk „Ulepsz/Twój plan/Przywróć zakup/Podaj kod Farm”.
    - Informacja o stanie licencji i liczbie powiązanych urządzeń (Farm).
    - Po aktywacji: brak ograniczeń funkcji, batch eksport, rozszerzone PDF itd.

#### US-012: Komunikacja limitu wersji Free i reakcja na osiągnięcie limitu
- Tytuł: Obsługa limitu 30 sesji w Free
- Opis: Jako użytkownik Free, chcę wiedzieć ile sesji mi pozostało i co się stanie, gdy wyczerpię limit.
- Kryteria akceptacji:
    - Baner/pasek zliczający pozostałe sesje.
    - Komunikat/przekierowanie do zakupu Pro po osiągnięciu limitu.
    - Blokada nowych sesji po 30 (podgląd i eksport dalej dostępne).

#### US-013: Zgodność sprzętowa i adaptacja wydajnościowa (low/mid/high)
- Tytuł: Płynność na różnych urządzeniach
- Opis: Jako użytkownik, chcę płynną obsługę zarówno na słabszych jak i mocnych urządzeniach, by nie doświadczać opóźnień lub wyłączeń.
- Kryteria akceptacji:
    - Adaptacyjne ustawienia rozdzielczości/FPS/throttling.
    - Brak przegrzewania, degradacji detekcji lub zacięć (>99,5% crash free).
    - Warm-up modelu nie przekracza 1 sekundy.

#### US-014: Mechanizmy bezpieczeństwa i audytu
- Tytuł: Audyt edycji i bezpieczeństwo danych
- Opis: Jako manager, chcę mieć możliwość prześledzenia zmian korekt i edycji sesji, by zapewnić integralność danych.
- Kryteria akceptacji:
    - Log historii edycji na poziomie sesji z datą, polem, wartością.
    - Dostęp do historii z poziomu szczegółów sesji.

#### US-015: Dostępność — tryb wysokiego kontrastu i duże hit-targey
- Tytuł: Obsługa w wymagających warunkach
- Opis: Jako operator w rękawicach lub z wadą wzroku, chcę korzystać z dużych przycisków i kontrastowego UI, by nie popełniać błędów.
- Kryteria akceptacji:
    - Przełącznik trybu wysokiego kontrastu.
    - Przycisk akcji ≥56 dp.
    - Bezproblemowa obsługa fizycznych przycisków oraz dotyku.

#### US-016: Zapewnienie bezpiecznego dostępu i autoryzacji (model Farm)
- Tytuł: Ograniczenie dostępu przez aktywację licencji Farm
- Opis: Jako administrator Farmy, chcę mieć kontrolę nad urządzeniami korzystającymi z licencji Farm, aby zapobiec nadużyciom.
- Kryteria akceptacji:
    - Aktywacja odbywa się offline przez token, sprawdzanie zużycia limitu urządzeń.
    - Wgląd do stanu urządzeń powiązanych z tokenem.
    - Operacja rebind wyłącznie przez kontakt ze wsparciem.

#### US-017: Feedback techniczny i wsparcie QA/pilot
- Tytuł: Proste zgłaszanie problemów i diagnostyka
- Opis: Jako użytkownik (pilot/QA), chcę mieć możliwość łatwego złożenia feedbacku oraz eksportu diagnostyki, by pomóc w rozwoju aplikacji.
- Kryteria akceptacji:
    - Formularz feedback offline.
    - Eksport krótkich logów działania (bez PII).
    - Dostęp z głównego menu/aplikacji.

## 6. Metryki sukcesu

- Jakość detekcji:
    - Protokół testowy: ≥50 sesji na każdy ze scenariuszy A–D (tace, gniazda, mieszane światło, niski kontrast/zabrudzenia).
    - Błąd liczony per sesję vs ręczny ground truth (teren).
    - Sukces: >=80% sesji bez ręcznej korekty na A–C, >=70% na D.
    - MAE (median absolute error) <=±2 dla wyników do 100 jaj.
    - mAP50/95, recall po quantyzacji INT8: A/B ≥0,90; C ≥0,85; D ≥0,80.
- Wydajność:
    - Mediana czasu do stabilizacji wyniku ≤200 ms (cel 150 ms).
    - FPS: low 12–15, mid 20–30, high 30–60; CPU/GPU <60%; pobór baterii ≤0,7%/min.
    - Start inferencji <1 s; brak crashy lub degradacji po długim użyciu.
- Niezawodność aplikacji:
    - Crash rate ≤0,5% dla użytkowników ze zgodą na Crashlytics.
    - Brak problemów z resume i obsługą kamery/latarki.
    - Utrzymanie konsystencji danych (brak utraty sesji, integralność audytu).
- Biznes i doświadczenie użytkownika:
    - Odsetek sesji zakończonych bez korekty jako główny KPI UX.
    - Udział sesji zakończonych eksportem (PDF/CSV).
    - Czas do wygenerowania eksportu <2 s dla 100 sesji; batch export skalowalny do 1k.
    - Konwersja Free→Pro oraz aktywacja Farm.
    - Pozytywny feedback pilota (>90% zadowolenia technicznego i ergonomicznego).
- Rozwinięcie produktu:
    - Udany rollout pilotażowy (≥200 sesji/scenariusz w 2 tyg.), stabilne wyniki i spełnienie powyższych KPI.
    - Przygotowany plan aktualizacji modelu i proces walidacji regresji przed kaźdą kolejną wersją.
```
