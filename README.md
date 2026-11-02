
#  FaceBlur (Multi-Face Tracking & Anonymizer)

[![GitHub](https://img.shields.io/badge/GitHub-marek7400-blue?style=flat&logo=github)](https://github.com/marek7400?tab=repositories)
[![Release](https://img.shields.io/badge/Wersja-1.36-success?style=flat)](https://github.com/marek7400)
[![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat&logo=windows)](https://github.com/marek7400)
[![Python](https://img.shields.io/badge/Python-3.8%2B-brightgreen?style=flat&logo=python)](https://www.python.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-DNN%20%7C%20CSRT-orange?style=flat&logo=opencv)](https://opencv.org/)
[![FFmpeg](https://img.shields.io/badge/Audio-FFmpeg%20Pass--Through-red?style=flat&logo=ffmpeg)](https://ffmpeg.org/)

Nowoczesne, lekkie i w 100% lokalne narzędzie dla systemu Windows do automatycznego i ręcznego zamazywania/anonimizacji **wielu twarzy jednocześnie na wideo oraz statycznych zdjęciach**.

Aplikacja została zoptymalizowana pod kątem pracy na standardowych procesorach (CPU) bez potrzeby posiadania dedykowanej karty graficznej. Posiada wbudowany bufor trajektorii (brak zacięć przy cofaniu wideo), 4 zaawansowane silniki detekcji AI ONNX, płynny zoom z przesuwaniem kadru (Pan), miękkie wtapianie krawędzi (Feathering), pełne zachowanie oryginalnego dźwięku (FFmpeg), tryb ciemny/jasny oraz możliwość tworzenia masek prostokątnych, owalnych i wielokątnych (`LINE`).

<img width="1920" height="1159" alt="FaceBlur" src="https://github.com/user-attachments/assets/79e161cd-97b7-4bf5-aa00-5fb8b593494a" />

---

## Spis treści
1. [Nowości w wersji 1.36](#-nowości-w-wersji-136)
2. [Główne cechy](#-główne-cechy)
3. [Wymagania i integracja z FFmpeg](#-wymagania-i-integracja-z-ffmpeg-dźwięk-w-wideo)
4. [Pobieranie i uruchomienie (.EXE)](#-pobieranie-i-uruchomienie-exe)
5. [Uruchomienie ze źródeł (Python)](#-uruchomienie-ze-źródeł-python)
6. [Szczegółowy opis interfejsu](#-szczegółowy-opis-interfejsu)
7. [Metody zaznaczania i edycji masek](#-metody-zaznaczania-i-edycji-masek)
8. [Skróty klawiszowe i sterowanie myszą](#-skróty-klawiszowe-i-sterowanie-myszą)
9. [Przegląd modeli AI (Detektory twarzy)](#-przegląd-modeli-ai-detektory-twarzy)
10. [Tryby zamazywania i kształty](#-tryby-zamazywania-i-kształty)
11. [Rozwiązywanie problemów (FAQ)](#-rozwiązywanie-problemów-faq)

---

##  Nowości w wersji 1.36

*  **Pełne zachowanie ścieżki dźwiękowej w filmach:** Dzięki integracji z narzędziem **FFmpeg**, eksportowane wideo zachowuje 100% oryginalnego dźwięku (`audio pass-through` bez utraty jakości).
*  **Wtapianie krawędzi maski (Feathering):** Płynne, zmiękczone przejście tonalne między rozmytym obiektem a tłem. Sterowane dedykowanym suwakiem w zakresie 0–100%.
*  **Przełącznik motywu Jasny / Ciemny (Dark Mode):** Nowy przycisk ` / ` w górnym pasku dynamicznie dopasowuje paletę barw całego interfejsu.
*  **Dedykowane okno postępu eksportu:** Procentowy licznik (`%`), pasek postępu oraz informacja o aktualnie renderowanej klatce w osobnym oknie modalnym.
*  **Wszystkie okna i komunikaty wycentrowane:** Komunikaty informacyjne, pytania, błędy oraz pasek eksportu otwierają się idealnie na środku ekranu.
*  **Ulepszone suwaki i pamięć ustawień:**
  * Wartość liczbowa oraz nazwa wyświetlane są bezpośrednio **nad suwakiem** (wzorem osi czasu).
  * Tolerancja skoku zwiększona do **300 px**.
  * Wydłużone suwaki dla większej precyzji.
  * Automatyczny zapis ustawień do pliku `settings.json` i ich przywracanie przy starcie.
*  **Uporządkowany układ:** Przełączniki kształtów (`CIRCLE`, `BLACKBOX`, `LINE`, `PIXEL MODE`) zostały przeniesione do dolnego panelu parametrów.

---

## Główne cechy

* **Wsparcie dla wideo i zdjęć:** Anonimizacja plików filmowych (`.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`) oraz fotografii (`.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`).
* **Śledzenie wielu twarzy naraz (Multi-Face):** Niezależne śledzenie wielu osób w kadrze z osobnymi identyfikatorami (Twarz 1, Twarz 2 itd.).
* **Brak sztucznych obwódek:** Zamazane obszary wtapiają się w otoczenie bez widocznych kolorowych obrysów na finalnym materiale.
* **Hybrydowe oznaczanie (AUTO + MANUAL na każdej klatce):** 
  * Kliknięcie zielonej ramki AI $\rightarrow$ natychmiastowe śledzenie automatyczne.
  * Przeciągnięcie lewym przyciskiem myszy $\rightarrow$ ręczne dodanie własnej ramki.
  * Tryb **LINE** $\rightarrow$ swobodne rysowanie wielokątów kliknięcie po kliknięciu.
* **Elastyczna korekta (Handover per twarz):** Kliknięcie nowej pozycji śledzonej twarzy automatycznie kończy jej stary segment i płynnie wznawia nowy.
* **Pamięć trajektorii (Trajectory Caching):** Błyskawiczne przewijanie osi czasu wstecz bez ponownego przeliczania trackerów i bez zamrażania programu.
* **Pauza śledzenia (PAUSE / RESUME):** Możliwość czasowego przerwania zamazywania (np. gdy postać chowa się za przeszkodą) i wznowienia go w dowolnej klatce.
* **Wbudowany podgląd z Zoomem (do 12x):** Przybliżanie kółkiem myszy i przesuwanie kadru (Pan) prawym przyciskiem myszy. Kontrolki zachowują stały, poręczny rozmiar na ekranie.

---

## Wymagania i integracja z FFmpeg (Dźwięk w wideo)

Aby program mógł scalić oryginalną ścieżkę dźwiękową z wyeksportowanym wideo, potrzebuje narzędzia **FFmpeg**:

1. Program przy starcie automatycznie sprawdza, czy w jego katalogu znajduje się plik `ffmpeg.exe` (lub czy FFmpeg jest zainstalowany w systemowej zmiennej `PATH`).
2. Jeśli pliku brakuje, wyświetli się okno z bezpośrednimi odnośnikami do pobrania:
   * [Oficjalna paczka GPL (BtbN Releases)](https://github.com/BtbN/FFmpeg-Builds/releases/download/latest/ffmpeg-master-latest-win64-gpl.zip)
   * [Alternatywna paczka Gyan.dev](https://www.gyan.dev/ffmpeg/builds/ffmpeg-git-full.7z)
3. **Instalacja:** Wystarczy rozpakować archiwum i skopiować pojedynczy plik **`ffmpeg.exe`** bezpośrednio do folderu z programem `FaceBlurTool.exe`.
4. *Uwaga:* W przypadku braku FFmpeg program nadal wyeksportuje zamazany plik wideo, lecz będzie on pozbawiony ścieżki dźwiękowej.

---

## Pobieranie i uruchomienie (.EXE)

Aplikacja jest dystrybuowana jako **gotowy plik wykonywalny Windows**:

1. Pobierz plik **`FaceBlur.exe`** z zakładki **Releases**.
2. Umieść plik w dowolnym folderze na dysku (zalecane umieszczenie obok pliku `ffmpeg.exe`).
3. Uruchom program dwuklikiem – silniki AI są wbudowane lub ładowane z folderu programu, program nie wymaga instalowania środowiska Python.

---

## Uruchomienie ze źródeł (Python)

Jeśli wolisz uruchomić aplikację w czystym środowisku Pythona (zalecany Python 3.10 – 3.13):

```bash
# 1. Sklonuj repozytorium
git clone https://github.com/marek7400/face-blur-tool.git
cd face-blur-tool

# 2. Zainstaluj zależności
pip install -r requirements.txt

# 3. Uruchom aplikację
python face_blur_app.py
```

Zawartość `requirements.txt`:
```text
opencv-contrib-python>=4.8.0
Pillow>=9.0.0
numpy
```

---

## Szczegółowy opis interfejsu

### 1. Górny pasek narzędzi
* **Wybierz wideo:** Otwiera plik filmowy, ładuje klatki i konfiguruje oś czasu.
* **Wybierz zdjęcie:** Wycisza oś czasu i przełącza program w tryb obróbki pojedynczego zdjęcia.
* **Model (Lista rozwijana):** Przełączanie w locie silnika AI (YuNet, SCRFD 500M, SCRFD 2.5G, YOLO-Face Nano).
* **Wskaż...:** Pozwala wskazać plik `.onnx` z dysku.
* **Zamaż wszystkie zielone:** Zaczyna śledzić wszystkie twarze wykryte przez AI na bieżącej klatce jednym kliknięciem.
* ** PAUSE:** Kończy śledzenie twarzy na poprzedniej klatce (wyłącza maskowanie od bieżącej klatki).
* ** RESUME:** Wznawia śledzenie twarzy od bieżącej klatki.
* **Stop gdy zgubi:** Zatrzymuje śledzenie danej twarzy w razie gwałtownego skoku lub opuszczenia kadru.
* ** ZOOM 1:1:** Resetuje powiększenie i wyśrodkowuje widok.
* **Cofnij:** Cofa ostatnio utworzony segment śledzenia.
* **Wyczyść:** Resetuje wszystkie trackery i czyści oś czasu.
* ** / :** Natychmiastowe przełączenie motywu ciemnego/jasnego.
* **Eksportuj / Zapisz:** Uruchamia proces renderowania z oknem postępu.

### 2. Dolny panel kontrolny
* **Oś czasu:** Płynna nawigacja po klatkach (`|<< Początek`, suwak z numerem klatki, `Koniec >>|`, pole bezpośredniego wpisu).
* **Suwak „Rozmycie” (0–100):** Regulacja siły rozmycia Gaussa lub rozmiaru bloków mozaiki.
* **Suwak „Wtapianie” (0–100):** Płynne zmiękczanie krawędzi maski (brak ostrych odcięć).
* **Suwak „Tolerancja skoku” (0–300):** Maksymalny dopuszczalny ruch obiektu w pikselach między klatkami (zabezpieczenie przed dryfem trackera CSRT).
* **Przełączniki kształtów i trybów:**
  * `CIRCLE (owal)` – maska w kształcie elipsy.
  * `BLACKBOX` – pełna cenzura czarną plamą.
  * `LINE (wielokąt)` – ręczne wyznaczanie punktów nieregularnego kształtu.
  * `PIXEL MODE` – zamiana rozmycia Gaussa na pikselozę retro.

### 3. Prawy panel boczny
* Status aktywnego silnika AI oraz parametry wczytanego pliku.
* Przewijalna lista segmentów (`Twarz X [ID Y]`):
  * **`IDŹ`** – przenosi oś czasu do klatki startowej wybranego segmentu.
  * **`X`** – bezpowrotnie usuwa dany segment śledzenia.

---

## Metody zaznaczania i edycji masek

```
-¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¬
- • AUTO: Kliknij lewym przyciskiem myszy w zieloną ramkę AI             -
- • MANUAL: Przeciągnij lewym przyciskiem, tworząc prostokąt/owal        -
- • LINE: Zaznacz 'LINE', klikaj punkty, zakończ 2x kliknięciem          -
- • PRZESUNIĘCIE: Chwyć za żółtą ikonę celownika na środku ramki         -
- • ROZMIAR: Chwyć za krawędź lub narożnik ramki                         -
L¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦-
```

* **Korekta pozycji (Handover):** Jeśli tracker zjedzie z twarzy na dalszych klatkach, wystarczy kliknąć nową zieloną ramkę lub narysować nową. Program zarchiwizuje dotychczasową trasę i płynnie podejmie śledzenie od nowej pozycji.
* **Interaktywne uchwyty:** Na aktywnej ramce dostępne są punkty zmiany rozmiaru oraz centralny uchwyt przesuwania – działają one niezależnie od powiększenia (Zoom).

---

## Skróty klawiszowe i sterowanie myszą

| Klawisz / Przycisk | Akcja |
| :--- | :--- |
| **Lewy przycisk myszy (LPM)** | Kliknięcie w ramkę AI (AUTO) / Przeciągnięcie (MANUAL) |
| **LPM (środek ramki)** | Przesunięcie położenia maski |
| **LPM (krawędź / narożnik)** | Zmiana szerokości i wysokości maski |
| **Podwójny klik (LPM)** | Zamknięcie wielokąta w trybie `LINE` |
| **Prawy przycisk myszy (klik)** | Usunięcie maski pod kursorem / Anulowanie punktów `LINE` |
| **Prawy przycisk myszy (przeciągnięcie)** | **Przesuwanie widoku (Pan)** przy powiększeniu (`Zoom > 1.0x`) |
| **Kółko myszy (Scroll)** | Płynny Zoom w stronę kursora (do 12x) / Oddalanie |
| **Środkowy przycisk myszy / Shift + LPM** | Alternatywne przesuwanie powiększonego kadru (Pan) |
| **Strzałka w prawo (`›`)** | Następna klatka (+1) |
| **Strzałka w lewo (`‹`)** | Poprzednia klatka (-1) |
| **Shift + Strzałka w prawo** | Skok w przód o 10 klatek (+10) |
| **Shift + Strzałka w lewo** | Skok w tył o 10 klatek (-10) |
| **Strzałka w górę (`^`)** | Skok w przód o 30 klatek (+30) |
| **Strzałka w dół (`ˇ`)** | Skok w tył o 30 klatek (-30) |
| **Klawisz `Delete`** | Usunięcie ostatniego segmentu |

---

## Przegląd modeli AI (Detektory twarzy)

Możliwość wyboru silnika detekcji w zależności od specyfiki materiału i mocy procesora:

```
[ YuNet ] ¦¦¦¦¦¦¦¦¦¦ Lekki, zintegrowany z OpenCV (~335 KB), bardzo szybki na CPU
[ SCRFD 500M ] ¦¦¦¦¦ Bardzo szybki (~2.4 MB), idealny do małych twarzy w tłumie
[ SCRFD 2.5G ] ¦¦¦¦¦ Najlepszy stosunek precyzji do obciążenia procesora (~3.2 MB)
[ YOLO-Face Nano ] ¦ Niezrównany przy profilach 90°, nakryciach głowy i trudnych kątach (~6.0 MB)
```

| Model | Waga | Zastosowanie |
| :--- | :--- | :--- |
| **YuNet** | ~335 KB | Szybkie wykrywanie twarzy frontalnych i lekko obróconych. |
| **SCRFD 500M** | ~2.4 MB | Słabsze procesory, ujęcia szerokokątne z dużą liczbą osób. |
| **SCRFD 2.5G** | ~3.2 MB | **Rekomendowany:** optymalny balans skuteczności i prędkości. |
| **YOLO-Face (Nano)** | ~6.0 MB | Twarze w profilu (90°), mocne przechyły, okulary i maski. |
| **Haar Cascade** | ~900 KB | Wbudowany silnik awaryjny (fallback) na wypadek braku plików ONNX. |

---

## Tryby zamazywania i kształty

1. **Gaussian Blur:** Klasyczne, gładkie rozmycie Gaussa o sile regulowanej suwakiem *Rozmycie*.
2. **Feathering (Wtapianie):** Zmiękcza krawędzie rozmycia, eliminując odcinające się linie maski.
3. **PIXEL MODE:** Pikselowa mozaika. Wielkość bloków dopasowuje się do suwaka rozmycia.
4. **BLACKBOX:** Kryjąca czarna cenzura (prostokątna lub eliptyczna).
5. **Maska CIRCLE (Owal):** Naturalne, owalne dopasowanie maski do obrysu głowy.
6. **Maska LINE (Wielokąt):** Precyzyjne obrysowanie nieregularnych elementów (tatuaże, logotypy, tablice rejestracyjne).

---

## Rozwiązywanie problemów (FAQ)

#### 1. Dlaczego wyeksportowany film nie ma dźwięku?
Upewnij się, że plik `ffmpeg.exe` znajduje się w tym samym folderze co plik wykonywalny programu. Program poinformuje o braku pliku stosownym komunikatem przy starcie.

#### 2. Windows SmartScreen wyświetla ostrzeżenie przy uruchomieniu pliku .EXE
Jest to standardowe zachowanie filtru Windows Defender dla nowo skompilowanych aplikacji bez płatnego certyfikatu:
* Kliknij **„Więcej informacji”**, a następnie wybierz **„Uruchom mimo to”**.
* Aplikacja jest w 100% bezpieczna, nie łączy się z internetem i działa wyłącznie lokalnie na Twoim komputerze.

#### 3. Postać obraca się tyłem lub profilem i tracker gubi twarz
* Przełącz model na **YOLO-Face (Nano)** lub **SCRFD 2.5G**.
* Zwiększ wartość suwaka **Tolerancja skoku** (np. do 180–220 px).
* W razie zerwania śledzenia zatrzymaj film, narysuj ramkę ręcznie (LPM) i kontynuuj odtwarzanie.

#### 4. Gdzie zapisywane są moje ustawienia suwaków?
Wartości suwaków (*Rozmycie*, *Wtapianie*, *Tolerancja skoku*) są automatycznie zapisywane w pliku `settings.json` w folderze programu przy każdym zamknięciu okna.

#### 5. Jak w podglądzie nagle pojawiają się zaznaczenia w przypadkowych miejscach to naciskamy RESUME (przestaje nam śledzić) i rysujemy ręcznie obrys na twarzy. Od tego momentu śledzi to co narysowaliśmy.
