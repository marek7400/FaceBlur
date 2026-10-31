

#  FaceBlur (Multi-Face Tracking & Anonymizer)

[![GitHub](https://img.shields.io/badge/GitHub-marek7400-blue?style=flat&logo=github)](https://github.com/marek7400?tab=repositories)
[![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat&logo=windows)](https://github.com/marek7400)
[![Python](https://img.shields.io/badge/Python-3.8%2B-brightgreen?style=flat&logo=python)](https://www.python.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-DNN%20%7C%20CSRT-orange?style=flat&logo=opencv)](https://opencv.org/)

Nowoczesne, lekkie i w 100% lokalne narzędzie dla systemu Windows do automatycznego i ręcznego zamazywania/anonimizacji **wielu twarzy jednocześnie na wideo oraz statycznych zdjęciach**.

Program został zoptymalizowany pod kątem pracy na standardowych procesorach (CPU) bez potrzeby posiadania potężnej karty graficznej. Posiada wbudowany bufor trajektorii (brak zacięć przy cofaniu wideo), 4 zaawansowane silniki detekcji AI ONNX, płynny zoom z przesuwaniem kadru (Pan) oraz możliwość tworzenia masek prostokątnych, owalnych i wielokątnych (`LINE`).

<img width="1920" height="1159" alt="FaceBlur" src="https://github.com/user-attachments/assets/832b7a70-4fda-444f-ac4a-52badacc4095" />

## Obecnie (v1.35) OpenCV (cv2.VideoWriter), którego używasz do eksportu, zawsze usuwa ścieżkę dźwiękową z nagrania.
---

##  Spis treści
1. [Główne cechy](#-główne-cechy)
2. [Pobieranie i uruchomienie (.EXE)](#-pobieranie-i-uruchomienie-exe)
3. [Uruchomienie ze źródeł (Python)](#-uruchomienie-ze-źródeł-python)
4. [Szczegółowy opis funkcji i interfejsu](#-szczegółowy-opis-funkcji-i-interfejsu)
5. [Skróty klawiszowe i sterowanie myszą](#-skróty-klawiszowe-i-sterowanie-myszą)
6. [Przegląd modeli AI (Detektory twarzy)](#-przegląd-modeli-ai-detektory-twarzy)
7. [Tryby zamazywania i kształty](#-tryby-zamazywania-i-kształty)
8. [Rozwiązywanie problemów (FAQ)](#-rozwiązywanie-problemów-faq)
---

##  Główne cechy
* **Wsparcie dla wideo i zdjęć:** Anonimizacja plików wideo (`.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`) oraz fotografii (`.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`).
* **Śledzenie wielu twarzy naraz (Multi-Face):** Niezależne śledzenie wielu osób w kadrze z osobnymi identyfikatorami (Twarz 1, Twarz 2 itd.).
* **Brak sztucznych obwódek:** Zamazane obszary wtapiają się czysto w otoczenie bez widocznych kolorowych obrysów.
* **Hybrydowe oznaczanie (AUTO + MANUAL na każdej klatce):** 
  * Kliknięcie zielonej ramki AI $\rightarrow$ natychmiastowe śledzenie automatyczne.
  * Przeciągnięcie lewym przyciskiem myszy $\rightarrow$ ręczne dodanie własnej ramki.
  * Tryb **LINE** $\rightarrow$ swobodne rysowanie wielokątów kliknięcie po kliknięciu.
* **Elastyczna korekta (Handover per twarz):** Kliknięcie nowej pozycji śledzonej twarzy automatycznie kończy jej stary segment i płynnie wznawia nowy.
* **Pamięć trajektorii (Trajectory Caching):** Błyskawiczne przewijanie osi czasu wstecz bez ponownego przeliczania trackerów i bez zamrażania programu.
* **Pauza śledzenia (PAUSE / RESUME):** Możliwość czasowego przerwania zamazywania (np. gdy postać chowa się za ścianą) i wznowienia go w odpowiedniej klatce.
* **Wbudowany podgląd z Zoomem (do 12x):** Przybliżanie kółkiem myszy i przesuwanie kadru (Pan) prawym przyciskiem myszy. Kontrolki nie powiększają się do gigantycznych rozmiarów przy zoomie.

---

##  Pobieranie i uruchomienie (.EXE)

Aplikacja jest dystrybuowana jako **pojedynczy, w pełni samowystarczalny plik wykonywalny Windows**:

1. Pobierz plik **`FaceBlur.exe`** z zakładki Releases lub skompiluj go samodzielnie skryptem `make_exe.bat`.
2. Umieść plik w dowolnym folderze na dysku.
3. Uruchom program dwuklikiem – wszystkie 4 silniki AI są zaszyte wewnątrz pliku, więc aplikacja nie wymaga instalowania Pythona ani żadnych zewnętrznych bibliotek.

---

##  Uruchomienie ze źródeł (Python)

Jeśli wolisz uruchomić aplikację bezpośrednio w środowisku Pythona:

```bash
# 1. Klonowanie repozytorium
git clone https://github.com/marek7400/face-blur-tool.git
cd face-blur-tool

# 2. Instalacja zależności
pip install -r requirements.txt

# 3. Uruchomienie aplikacji
python face_blur_v35_multi_model.py

lub zainstaluj Python (3.13) i uruchom plik run_v35_multi-model.bat
```

Wymagania w `requirements.txt`:
```text
opencv-contrib-python>=4.8.0
Pillow>=9.0.0
numpy
```

---

##  Szczegółowy opis funkcji i interfejsu

### 1. Górny pasek narzędzi
* **Wybierz wideo:** Otwiera plik filmowy, ładuje klatki i konfiguruje oś czasu.
* **Wybierz zdjęcie:** Wycisza oś czasu i przełącza program w tryb obróbki pojedynczego zdjęcia w pełnej rozdzielczości.
* **Model (Lista rozwijana):** Przełączanie w locie silnika AI (YuNet, SCRFD 500M, SCRFD 2.5G, YOLO-Face Nano).
* **Wskaż...:** Pozwala wskazać z dysku dowolny inny plik `.onnx` zgodny z architekturą.
* **Zamaż wszystkie zielone:** Zaczyna śledzić wszystkie twarze wykryte przez AI na bieżącej klatce jednym kliknięciem.
* **PAUSE:** Zamyka aktywny segment twarzy na poprzedniej klatce. Na bieżącej klatce i kolejnych rozmycie zostaje wyłączone.
* **RESUME:** Wznawia śledzenie twarzy z listy od bieżącej klatki (tworzy nowy segment).
* **CIRCLE (owal):** Zmienia kształt maski z prostokątnej na owalną (elipsa).
* **BLACKBOX:** Zamiast rozmycia nakłada pełne zaczernienie (cenzurę).
* **LINE (wielokąt):** Włącza tryb obrysowywania kształtów wieloma liniami.
* **Stop gdy zgubi:** Zatrzymuje śledzenie danej twarzy, jeśli obiekt wykona zbyt gwałtowny skok lub zniknie z kadru.
* **ZOOM 1:1:** Natychmiast resetuje powiększenie i centruje cały kadr.
* **Cofnij:** Cofa ostatnio dodany segment lub przejęcie śledzenia.
* **Wyczyść:** Resetuje wszystkie ramki i całą oś czasu do zera.
* **Eksportuj / Zapisz:** Zapisuje gotowy film `.mp4` lub przetworzone zdjęcie.

### 2. Oś czasu (Dół ekranu)
* **`|<< Początek`:** Błyskawiczny skok do klatki `0`.
* **Suwak osi czasu:** Przesuwanie klatek. Przy szybkim przeciąganiu program wyświetla podgląd lekki, a pełne przeliczenie wykonuje po zwolnieniu przycisku myszy.
* **`Koniec >>|`:** Błyskawiczny skok do ostatniej klatki filmu.
* **Pole numeryczne klatki:** Pozwala wpisać numer klatki i wcisnąć `Enter`.
* **Siła rozmycia:** Płynna regulacja od 15 do 151 (steruje stopniem rozmycia Gaussa lub rozmiarem klocków w PIXEL MODE).
* **PIXEL MODE:** Przełącza rozmycie na styl pikselowej mozaiki retro.
* **Tolerancja skoku (px):** Maksymalny dopuszczalny ruch twarzy między klatkami, po przekroczeniu którego tracker uznaje obiekt za zgubiony (ochrona przed dryfem).

### 3. Panel boczny (Prawa strona)
* Informacje o aktywnym modelu AI, rozdzielczości i liczbie klatek.
* Przewijalna lista wszystkich segmentów (`Twarz X [ID Y]`).
* Przyciski przy każdym segmencie:
  * **`IDŹ`** – przenosi suwak dokładnie do klatki startowej danego segmentu.
  * **`X`** – trwale usuwa wskazany segment.

---

##  Metody zaznaczania i edycji

```
-¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¬
- AUTO: Kliknij lewym zieloną ramkę AI                                  -
- MANUAL: Naciśnij lewy i przeciągnij prostokąt/owal                    -
- LINE: Zaznacz 'LINE', klikaj punkty, zakończ 2x kliknięciem           -
- PRZESUNIĘCIE: Chwyć za żółtą ikonę 4 strzałek na środku ramki          -
- ROZMIAR: Chwyć za krawędź lub narożnik ramki                           -
L¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦-
```

* **Korekta w trakcie filmu (Handover):** Jeśli na klatce 100 tracker lekko zjechał z twarzy, wystarczy kliknąć nową zieloną ramkę AI lub narysować nową ręcznie w pobliżu. Program automatycznie zakończy stary segment na klatce 99 i zacznie nowy od klatki 100.
* **Przesuwanie i skalowanie ramki:** 
  * Najedź na środek dowolnej aktywnej ramki – pojawi się ikona z 4 strzałkami. Wciśnij lewy przycisk i przesuń.
  * Najedź na krawędź lub narożnik – kursor zmieni się w strzałki skalowania. Wciśnij lewy przycisk i rozciągnij/zmniejsz obszar.
  * Działa to również przy włączonym trybie `LINE` (kontrole edycji mają pierwszeństwo przed stawianiem nowych punktów).

---

##  Skróty klawiszowe i sterowanie myszą

| Klawisz / Przycisk | Akcja |
| :--- | :--- |
| **Lewy przycisk myszy** | Kliknięcie w zieloną ramkę (AUTO) / Przeciągnięcie (MANUAL) |
| **Lewy przycisk (środek ramki)** | Chwycenie i przesunięcie całej ramki |
| **Lewy przycisk (krawędź ramki)** | Zmiana rozmiaru ramki w wybranym kierunku |
| **Podwójny klik (Lewy)** | Zamknięcie i utworzenie wielokąta w trybie `LINE` |
| **Prawy przycisk myszy (klik)** | Usunięcie pojedynczej ramki pod kursorem (także anulowanie rysowania `LINE`) |
| **Prawy przycisk myszy (przeciągnięcie)** | **Chwycenie i przesuwanie obrazu (Pan)** przy powiększeniu (`Zoom > 1.0x`) |
| **Kółko myszy (Scroll)** | Płynny Zoom w stronę kursora (do 12x) / Oddalanie |
| **Środkowy przycisk myszy / Shift + LPM** | Alternatywne przesuwanie powiększonego obrazu (Pan) |
| **Strzałka w prawo (`›`)** | Następna klatka (+1) |
| **Strzałka w lewo (`‹`)** | Poprzednia klatka (-1) |
| **Shift + Strzałka w prawo** | Skok w przód o 10 klatek (+10) |
| **Shift + Strzałka w lewo** | Skok w tył o 10 klatek (-10) |
| **Strzałka w górę (`^`)** | Skok w przód o 30 klatek (+30) |
| **Strzałka w dół (`ˇ`)** | Skok w tył o 30 klatek (-30) |
| **Klawisz `Delete`** | Cofnięcie / usunięcie ostatniego segmentu |

---

## Przegląd modeli AI (Detektory twarzy)

Program pozwala na dynamiczny wybór spośród 4 modeli ONNX:

```
[ YuNet ] ¦¦¦¦¦¦¦¦¦¦ Lekki, wbudowany w OpenCV (~300 KB), bardzo szybki na CPU
[ SCRFD 500M ] ¦¦¦¦¦ Zoptymalizowany pod CPU (~2.4 MB), demon prędkości
[ SCRFD 2.5G ] ¦¦¦¦¦ Najlepszy balans dokładności do prędkości (~3.2 MB)
[ YOLO-Face Nano ] ¦ Świetny przy profilach 90° i trudnych kątach (~6.0 MB)
```

| Model | Waga | Rekomendowane zastosowanie |
| :--- | :--- | :--- |
| **YuNet** | ~335 KB | Szybkie wykrywanie twarzy frontalnych i lekko obróconych. |
| **SCRFD 500M** | ~2.4 MB | Bardzo słabe procesory; sceny z wieloma małymi twarzami. |
| **SCRFD 2.5G** | ~3.2 MB | **Zalecany domyślny:** znakomity stosunek dokładności do szybkości na CPU. |
| **YOLO-Face (Nano)** | ~6.0 MB | Twarze w pełnym profilu (90°), mocno odchylone głowy, czapki i okulary. |
| **Haar Cascade** | ~900 KB | Tradycyjny algorytm OpenCV działający jako automatyczny fallback w razie braku plików `.onnx`. |

---

## Tryby zamazywania i kształty

1. **Gaussian Blur (Domyślny):** Płynne, estetyczne rozmycie plamą Gaussa. Siłę ustala suwak `Siła rozmycia`.
2. **PIXEL MODE:** Mozaika pikselowa. Rozmiar pikseli rośnie wraz z wartością suwaka rozmycia.
3. **BLACKBOX:** Klasyczna, solidna cenzura czarnym prostokątem lub owalem.
4. **Maska CIRCLE (Owal):** Twarze maskowane są elipsą wyciętą precyzyjnie w kadrze.
5. **Maska LINE (Wielokąt):** Ręcznie zdefiniowany wielokąt dopasowany do nieregularnych kształtów (np. tatuaż, tablica rejestracyjna, specyficzny kadr).

---

## Rozwiązywanie problemów (FAQ)

#### 1. Czy program wymaga zewnętrznej karty graficznej?
Nie. Wszystkie algorytmy i sieci neuronowe ONNX zostały zoptymalizowane pod instrukcje wektorowe procesora (CPU). Program działa płynnie nawet na zintegrowanych układach graficznych i starszych komputerach.

#### 2. Windows Defender / SmartScreen wyświetla ostrzeżenie przy uruchomieniu pliku .EXE
Jest to standardowe zachowanie filtru Windows SmartScreen dla nowo skompilowanych plików `.exe` bez płatnego certyfikatu cyfrowego EV:
* Kliknij **„Więcej informacji”**, a następnie **„Uruchom mimo to”**.
* Program nie zawiera żadnego złośliwego kodu (kod źródłowy jest w 100% jawny i otwarty).

#### 3. Program nie wykrywa twarzy obróconej całkowicie bokiem (profil 90°)
* Zmień model z listy na **YOLO-Face (Nano)** lub **SCRFD 2.5G** – radzą sobie ze skrajnymi obrotami znacznie lepiej niż YuNet.
* Alternatywnie: po prostu narysuj ramkę ręcznie (przeciągając lewym przyciskiem myszy) – od tego momentu tracker CSRT poprowadzi twarz samodzielnie.

#### 4. Jak cofnąć się w filmie bez zacinania obrazu?
Dzięki wdrożeniu pamięci trajektorii (`traj cache`) cofanie w wideo jest natychmiastowe. Pamiętaj jednak, że podczas bardzo szybkiego przesuwania suwaka w przód program celowo nie przelicza AI dla każdej klatki pośredniej – zwolnij suwak w docelowym miejscu, aby program dokonał pełnej analizy.
