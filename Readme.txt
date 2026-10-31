

# ?? FaceBlur (Multi-Face Tracking & Anonymizer)

[![GitHub](https://img.shields.io/badge/GitHub-marek7400-blue?style=flat&logo=github)](https://github.com/marek7400?tab=repositories)
[![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat&logo=windows)](https://github.com/marek7400)
[![Python](https://img.shields.io/badge/Python-3.8%2B-brightgreen?style=flat&logo=python)](https://www.python.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-DNN%20%7C%20CSRT-orange?style=flat&logo=opencv)](https://opencv.org/)

Nowoczesne, lekkie i w 100% lokalne narzêdzie dla systemu Windows do automatycznego i rêcznego zamazywania/anonimizacji **wielu twarzy jednoczeœnie na wideo oraz statycznych zdjêciach**.

Program zosta³ zoptymalizowany pod k¹tem pracy na standardowych procesorach (CPU) bez potrzeby posiadania potê¿nej karty graficznej. Posiada wbudowany bufor trajektorii (brak zaciêæ przy cofaniu wideo), 4 zaawansowane silniki detekcji AI ONNX, p³ynny zoom z przesuwaniem kadru (Pan) oraz mo¿liwoœæ tworzenia masek prostok¹tnych, owalnych i wielok¹tnych (`LINE`).

---

## ?? Spis treœci
1. [G³ówne cechy](#-g³ówne-cechy)
2. [Pobieranie i uruchomienie (.EXE)](#-pobieranie-i-uruchomienie-exe)
3. [Uruchomienie ze Ÿróde³ (Python)](#-uruchomienie-ze-Ÿróde³-python)
4. [Szczegó³owy opis funkcji i interfejsu](#-szczegó³owy-opis-funkcji-i-interfejsu)
5. [Skróty klawiszowe i sterowanie mysz¹](#-skróty-klawiszowe-i-sterowanie-mysz¹)
6. [Przegl¹d modeli AI (Detektory twarzy)](#-przegl¹d-modeli-ai-detektory-twarzy)
7. [Tryby zamazywania i kszta³ty](#-tryby-zamazywania-i-kszta³ty)
8. [Rozwi¹zywanie problemów (FAQ)](#-rozwi¹zywanie-problemów-faq)
---

## ?? G³ówne cechy

* **Wsparcie dla wideo i zdjêæ:** Anonimizacja plików wideo (`.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`) oraz fotografii (`.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`).
* **Œledzenie wielu twarzy naraz (Multi-Face):** Niezale¿ne œledzenie wielu osób w kadrze z osobnymi identyfikatorami (Twarz 1, Twarz 2 itd.).
* **Brak sztucznych obwódek:** Zamazane obszary wtapiaj¹ siê czysto w otoczenie bez widocznych kolorowych obrysów.
* **Hybrydowe oznaczanie (AUTO + MANUAL na ka¿dej klatce):** 
  * Klikniêcie zielonej ramki AI $\rightarrow$ natychmiastowe œledzenie automatyczne.
  * Przeci¹gniêcie lewym przyciskiem myszy $\rightarrow$ rêczne dodanie w³asnej ramki.
  * Tryb **LINE** $\rightarrow$ swobodne rysowanie wielok¹tów klikniêcie po klikniêciu.
* **Elastyczna korekta (Handover per twarz):** Klikniêcie nowej pozycji œledzonej twarzy automatycznie koñczy jej stary segment i p³ynnie wznawia nowy.
* **Pamiêæ trajektorii (Trajectory Caching):** B³yskawiczne przewijanie osi czasu wstecz bez ponownego przeliczania trackerów i bez zamra¿ania programu.
* **Pauza œledzenia (PAUSE / RESUME):** Mo¿liwoœæ czasowego przerwania zamazywania (np. gdy postaæ chowa siê za œcian¹) i wznowienia go w odpowiedniej klatce.
* **Wbudowany podgl¹d z Zoomem (do 12x):** Przybli¿anie kó³kiem myszy i przesuwanie kadru (Pan) prawym przyciskiem myszy. Kontrolki nie powiêkszaj¹ siê do gigantycznych rozmiarów przy zoomie.

---

## ?? Pobieranie i uruchomienie (.EXE)

Aplikacja jest dystrybuowana jako **pojedynczy, w pe³ni samowystarczalny plik wykonywalny Windows**:

1. Pobierz plik **`FaceBlurTool.exe`** z zak³adki Releases lub skompiluj go samodzielnie skryptem `make_exe.bat`.
2. Umieœæ plik w dowolnym folderze na dysku.
3. Uruchom program dwuklikiem – wszystkie 4 silniki AI s¹ zaszyte wewn¹trz pliku, wiêc aplikacja nie wymaga instalowania Pythona ani ¿adnych zewnêtrznych bibliotek.

---

## ?? Uruchomienie ze Ÿróde³ (Python)

Jeœli wolisz uruchomiæ aplikacjê bezpoœrednio w œrodowisku Pythona:

```bash
# 1. Klonowanie repozytorium
git clone https://github.com/marek7400/face-blur-tool.git
cd face-blur-tool

# 2. Instalacja zale¿noœci
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

## ?? Szczegó³owy opis funkcji i interfejsu

### 1. Górny pasek narzêdzi
* **Wybierz wideo:** Otwiera plik filmowy, ³aduje klatki i konfiguruje oœ czasu.
* **Wybierz zdjêcie:** Wycisza oœ czasu i prze³¹cza program w tryb obróbki pojedynczego zdjêcia w pe³nej rozdzielczoœci.
* **Model (Lista rozwijana):** Prze³¹czanie w locie silnika AI (YuNet, SCRFD 500M, SCRFD 2.5G, YOLO-Face Nano).
* **Wska¿...:** Pozwala wskazaæ z dysku dowolny inny plik `.onnx` zgodny z architektur¹.
* **Zama¿ wszystkie zielone:** Zaczyna œledziæ wszystkie twarze wykryte przez AI na bie¿¹cej klatce jednym klikniêciem.
* **? PAUSE:** Zamyka aktywny segment twarzy na poprzedniej klatce. Na bie¿¹cej klatce i kolejnych rozmycie zostaje wy³¹czone.
* **? RESUME:** Wznawia œledzenie twarzy z listy od bie¿¹cej klatki (tworzy nowy segment).
* **CIRCLE (owal):** Zmienia kszta³t maski z prostok¹tnej na owaln¹ (elipsa).
* **BLACKBOX:** Zamiast rozmycia nak³ada pe³ne zaczernienie (cenzurê).
* **LINE (wielok¹t):** W³¹cza tryb obrysowywania kszta³tów wieloma liniami.
* **Stop gdy zgubi:** Zatrzymuje œledzenie danej twarzy, jeœli obiekt wykona zbyt gwa³towny skok lub zniknie z kadru.
* **?? ZOOM 1:1:** Natychmiast resetuje powiêkszenie i centruje ca³y kadr.
* **Cofnij:** Cofa ostatnio dodany segment lub przejêcie œledzenia.
* **Wyczyœæ:** Resetuje wszystkie ramki i ca³¹ oœ czasu do zera.
* **Eksportuj / Zapisz:** Zapisuje gotowy film `.mp4` lub przetworzone zdjêcie.

### 2. Oœ czasu (Dó³ ekranu)
* **`|<< Pocz¹tek`:** B³yskawiczny skok do klatki `0`.
* **Suwak osi czasu:** Przesuwanie klatek. Przy szybkim przeci¹ganiu program wyœwietla podgl¹d lekki, a pe³ne przeliczenie wykonuje po zwolnieniu przycisku myszy.
* **`Koniec >>|`:** B³yskawiczny skok do ostatniej klatki filmu.
* **Pole numeryczne klatki:** Pozwala wpisaæ numer klatki i wcisn¹æ `Enter`.
* **Si³a rozmycia:** P³ynna regulacja od 15 do 151 (steruje stopniem rozmycia Gaussa lub rozmiarem klocków w PIXEL MODE).
* **PIXEL MODE:** Prze³¹cza rozmycie na styl pikselowej mozaiki retro.
* **Tolerancja skoku (px):** Maksymalny dopuszczalny ruch twarzy miêdzy klatkami, po przekroczeniu którego tracker uznaje obiekt za zgubiony (ochrona przed dryfem).

### 3. Panel boczny (Prawa strona)
* Informacje o aktywnym modelu AI, rozdzielczoœci i liczbie klatek.
* Przewijalna lista wszystkich segmentów (`Twarz X [ID Y]`).
* Przyciski przy ka¿dym segmencie:
  * **`ID`** – przenosi suwak dok³adnie do klatki startowej danego segmentu.
  * **`X`** – trwale usuwa wskazany segment.

---

## ?? Metody zaznaczania i edycji

```
-¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¬
- AUTO: Kliknij lewym zielon¹ ramkê AI                                  -
- MANUAL: Naciœnij lewy i przeci¹gnij prostok¹t/owal                    -
- LINE: Zaznacz 'LINE', klikaj punkty, zakoñcz 2x klikniêciem           -
- PRZESUNIÊCIE: Chwyæ za ¿ó³t¹ ikonê 4 strza³ek na œrodku ramki          -
- ROZMIAR: Chwyæ za krawêdŸ lub naro¿nik ramki                           -
L¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦¦-
```

* **Korekta w trakcie filmu (Handover):** Jeœli na klatce 100 tracker lekko zjecha³ z twarzy, wystarczy klikn¹æ now¹ zielon¹ ramkê AI lub narysowaæ now¹ rêcznie w pobli¿u. Program automatycznie zakoñczy stary segment na klatce 99 i zacznie nowy od klatki 100.
* **Przesuwanie i skalowanie ramki:** 
  * NajedŸ na œrodek dowolnej aktywnej ramki – pojawi siê ikona z 4 strza³kami. Wciœnij lewy przycisk i przesuñ.
  * NajedŸ na krawêdŸ lub naro¿nik – kursor zmieni siê w strza³ki skalowania. Wciœnij lewy przycisk i rozci¹gnij/zmniejsz obszar.
  * Dzia³a to równie¿ przy w³¹czonym trybie `LINE` (kontrole edycji maj¹ pierwszeñstwo przed stawianiem nowych punktów).

---

## ? Skróty klawiszowe i sterowanie mysz¹

| Klawisz / Przycisk | Akcja |
| :--- | :--- |
| **Lewy przycisk myszy** | Klikniêcie w zielon¹ ramkê (AUTO) / Przeci¹gniêcie (MANUAL) |
| **Lewy przycisk (œrodek ramki)** | Chwycenie i przesuniêcie ca³ej ramki |
| **Lewy przycisk (krawêdŸ ramki)** | Zmiana rozmiaru ramki w wybranym kierunku |
| **Podwójny klik (Lewy)** | Zamkniêcie i utworzenie wielok¹ta w trybie `LINE` |
| **Prawy przycisk myszy (klik)** | Usuniêcie pojedynczej ramki pod kursorem (tak¿e anulowanie rysowania `LINE`) |
| **Prawy przycisk myszy (przeci¹gniêcie)** | **Chwycenie i przesuwanie obrazu (Pan)** przy powiêkszeniu (`Zoom > 1.0x`) |
| **Kó³ko myszy (Scroll)** | P³ynny Zoom w stronê kursora (do 12x) / Oddalanie |
| **Œrodkowy przycisk myszy / Shift + LPM** | Alternatywne przesuwanie powiêkszonego obrazu (Pan) |
| **Strza³ka w prawo (`›`)** | Nastêpna klatka (+1) |
| **Strza³ka w lewo (`‹`)** | Poprzednia klatka (-1) |
| **Shift + Strza³ka w prawo** | Skok w przód o 10 klatek (+10) |
| **Shift + Strza³ka w lewo** | Skok w ty³ o 10 klatek (-10) |
| **Strza³ka w górê (`^`)** | Skok w przód o 30 klatek (+30) |
| **Strza³ka w dó³ (`¡`)** | Skok w ty³ o 30 klatek (-30) |
| **Klawisz `Delete`** | Cofniêcie / usuniêcie ostatniego segmentu |

---

## ?? Przegl¹d modeli AI (Detektory twarzy)

Program pozwala na dynamiczny wybór spoœród 4 modeli ONNX:

```
[ YuNet ] ¦¦¦¦¦¦¦¦¦¦ Lekki, wbudowany w OpenCV (~300 KB), bardzo szybki na CPU
[ SCRFD 500M ] ¦¦¦¦¦ Zoptymalizowany pod CPU (~2.4 MB), demon prêdkoœci
[ SCRFD 2.5G ] ¦¦¦¦¦ Najlepszy balans dok³adnoœci do prêdkoœci (~3.2 MB)
[ YOLO-Face Nano ] ¦ Œwietny przy profilach 90° i trudnych k¹tach (~6.0 MB)
```

| Model | Waga | Rekomendowane zastosowanie |
| :--- | :--- | :--- |
| **YuNet** | ~335 KB | Szybkie wykrywanie twarzy frontalnych i lekko obróconych. |
| **SCRFD 500M** | ~2.4 MB | Bardzo s³abe procesory; sceny z wieloma ma³ymi twarzami. |
| **SCRFD 2.5G** | ~3.2 MB | **Zalecany domyœlny:** znakomity stosunek dok³adnoœci do szybkoœci na CPU. |
| **YOLO-Face (Nano)** | ~6.0 MB | Twarze w pe³nym profilu (90°), mocno odchylone g³owy, czapki i okulary. |
| **Haar Cascade** | ~900 KB | Tradycyjny algorytm OpenCV dzia³aj¹cy jako automatyczny fallback w razie braku plików `.onnx`. |

---

## ?? Tryby zamazywania i kszta³ty

1. **Gaussian Blur (Domyœlny):** P³ynne, estetyczne rozmycie plam¹ Gaussa. Si³ê ustala suwak `Si³a rozmycia`.
2. **PIXEL MODE:** Mozaika pikselowa. Rozmiar pikseli roœnie wraz z wartoœci¹ suwaka rozmycia.
3. **BLACKBOX:** Klasyczna, solidna cenzura czarnym prostok¹tem lub owalem.
4. **Maska CIRCLE (Owal):** Twarze maskowane s¹ elips¹ wyciêt¹ precyzyjnie w kadrze.
5. **Maska LINE (Wielok¹t):** Rêcznie zdefiniowany wielok¹t dopasowany do nieregularnych kszta³tów (np. tatua¿, tablica rejestracyjna, specyficzny kadr).

---

## ? Rozwi¹zywanie problemów (FAQ)

#### 1. Czy program wymaga zewnêtrznej karty graficznej?
Nie. Wszystkie algorytmy i sieci neuronowe ONNX zosta³y zoptymalizowane pod instrukcje wektorowe procesora (CPU). Program dzia³a p³ynnie nawet na zintegrowanych uk³adach graficznych i starszych komputerach.

#### 2. Windows Defender / SmartScreen wyœwietla ostrze¿enie przy uruchomieniu pliku .EXE
Jest to standardowe zachowanie filtru Windows SmartScreen dla nowo skompilowanych plików `.exe` bez p³atnego certyfikatu cyfrowego EV:
* Kliknij **„Wiêcej informacji”**, a nastêpnie **„Uruchom mimo to”**.
* Program nie zawiera ¿adnego z³oœliwego kodu (kod Ÿród³owy jest w 100% jawny i otwarty).

#### 3. Program nie wykrywa twarzy obróconej ca³kowicie bokiem (profil 90°)
* Zmieñ model z listy na **YOLO-Face (Nano)** lub **SCRFD 2.5G** – radz¹ sobie ze skrajnymi obrotami znacznie lepiej ni¿ YuNet.
* Alternatywnie: po prostu narysuj ramkê rêcznie (przeci¹gaj¹c lewym przyciskiem myszy) – od tego momentu tracker CSRT poprowadzi twarz samodzielnie.

#### 4. Jak cofn¹æ siê w filmie bez zacinania obrazu?
Dziêki wdro¿eniu pamiêci trajektorii (`traj cache`) cofanie w wideo jest natychmiastowe. Pamiêtaj jednak, ¿e podczas bardzo szybkiego przesuwania suwaka w przód program celowo nie przelicza AI dla ka¿dej klatki poœredniej – zwolnij suwak w docelowym miejscu, aby program dokona³ pe³nej analizy.
```