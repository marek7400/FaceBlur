<img width="1920" height="1160" alt="pl" src="https://github.com/user-attachments/assets/97bd8f5f-1e31-4a34-9e91-d0736b61e117" />

<BR> <BR>

# Face & Person Blur Tool


> Repo z dodatkowymi modelami: [github.com/marek7400/FaceBlur/tree/main/models](https://github.com/marek7400/FaceBlur/tree/main/models).

## Spis treści

- [Co to jest](#co-to-jest)
- [Szybki start](#szybki-start)
- [Wymagania i instalacja](#wymagania-i-instalacja)
- [Układ programu](#układ-programu)
- [Modele AI](#modele-ai)
- [Dodatkowe modele: pobieranie i instalacja](#dodatkowe-modele-pobieranie-i-instalacja)
- [Tryb wideo i tryb zdjęcia](#tryb-wideo-i-tryb-zdjęcia)
- [Dodawanie zamazania](#dodawanie-zamazania)
- [Śledzenie: jak program podąża za osobą](#śledzenie-jak-program-podąża-za-osobą)
- [Edycja na podglądzie: mysz, klawiatura, zoom i pan](#edycja-na-podglądzie-mysz-klawiatura-zoom-i-pan)
- [Lista Segmenty i Oś Czasu](#lista-segmenty-i-oś-czasu)
- [Odtwarzanie i nawigacja](#odtwarzanie-i-nawigacja)
- [Znaczniki eksportu](#znaczniki-eksportu)
- [Wygląd zamazania: kształt, typ i suwaki](#wygląd-zamazania-kształt-typ-i-suwaki)
- [Maski segmentacji: pędzel, gumka i margines](#maski-segmentacji-pędzel-gumka-i-margines)
- [Zrzut ekranu](#zrzut-ekranu)
- [Eksport wideo i zdjęcia](#eksport-wideo-i-zdjęcia)
- [Ustawienia: plik settings.json](#ustawienia-plik-settingsjson)
- [Język i motyw](#język-i-motyw)
- [Ostrzeżenia i zasada fail-closed](#ostrzeżenia-i-zasada-fail-closed)
- [Znane problemy i ograniczenia](#znane-problemy-i-ograniczenia)
- [Rozwiązywanie problemów](#rozwiązywanie-problemów)
- [Prywatność](#prywatność)

## Co to jest

Face & Person Blur Tool to lokalny program desktopowy (jeden plik Python, interfejs Tkinter) do zamazywania twarzy i całych sylwetek na wideo i na zdjęciach.

Program nie jest edytorem wideo ogólnego przeznaczenia. Robi jedną rzecz, ale dokładnie: wykrywa obiekty modelem AI, zamienia wykrycie w tracker (segment), podąża za nim klatka po klatce i przy eksporcie nakłada zamazanie tylko tam, gdzie tracker ma pokrycie. Jeśli pokrycia nie ma, program ostrzega, zamiast cicho wyeksportować czysty fragment.

Najważniejsze cechy:

- wykrywanie twarzy i osób modelami ONNX: YuNet, SCRFD, YOLO Face, YOLO Person Box, YOLO Person Seg, YOLO26,
- śledzenie: ponowna detekcja AI, tracker CSRT (awaryjnie MIL), interpolacja i tryb HOLD,
- zamazanie całej sylwetki po obrysie segmentacji, a nie po prostokącie,
- ręczne rysowanie: prostokąt, wielokąt LINE, pędzel maski, gumka maski,
- pauza globalna i pauza pojedynczego segmentu,
- eksport fragmentów (znaczniki OD i DO) albo całego filmu, z dźwiękiem przez FFmpeg,
- zrzuty ekranu z zamazaniem albo bez,
- język polski i angielski, motyw jasny i ciemny,
- wszystko działa lokalnie, bez wysyłania plików do chmury.

## Szybki start

1. Jeśli chcesz extra modele połóż obok programu folder `models` z plikami `.onnx`. Możesz też dać nowsze niż zaszyte w programie - pliki `ffmpeg.exe` i `ffprobe.exe`. 
2. Uruchom program, kliknij **Wybierz wideo** albo **Wybierz zdjęcie**.
3. Wybierz model na liście, np. **YOLO-Osoba (Seg)**, i kliknij **Zamaż wszystkie zielone**, albo kliknij pojedynczą zieloną ramkę, albo kliknij **Zamaż cały film (AI)**.
4. Przejrzyj film klatka po klatce. Popraw to, co AI zgubiła: przeciągnij ramkę, domaluj maskę pędzlem, wytnij gumką, wznów segment.
5. Ustaw znaczniki eksportu albo eksportuj cały film przyciskiem **Eksportuj / Zapisz**.
6. Przed publikacją obejrzyj wynik. Zwłaszcza klatki, dla których program pokazał ostrzeżenia HOLD albo BEZ BLUR.

## Wymagania i instalacja

### Wersja EXE (zalecana dla użytkownika końcowego)

Wystarczy katalog z programem. Obok pliku EXE powinny leżeć:

| Plik / katalog | Do czego służy | Obowiązkowy |
| --- | --- | --- |
| Plik EXE programu | Uruchomienie | Tak |
| `models/` z plikami `.onnx` | Modele AI | Tak, przynajmniej jeden |
| `ffmpeg.exe` (oraz `ffprobe.exe`) | Dźwięk w eksporcie wideo | Nie, ale bez niego eksport jest bez dźwięku |
| `settings.json` | Zapisane ustawienia i własne modele | Nie, tworzy się przy zamknięciu |
| `haarcascade_frontalface_default.xml` | Awaryjny model Haar Cascade | Nie |

Program szuka `ffmpeg.exe` w tej kolejności: katalog programu, katalog `_MEIPASS` (wersja onefile), podkatalog `bin`, katalog z PATH systemowego. Jeśli go nie znajdzie, przy starcie pokaże okno z linkami do pobrania FFmpeg (BtbN oraz gyan.dev).

### Wersja z kodu źródłowego (.py)

Potrzebny jest Python 3 oraz biblioteki:

- `opencv-python` (cv2, w tym trackery CSRT / MIL),
- `numpy`,
- `Pillow` (PIL),
- `onnxruntime` (do modeli `.onnx`; bez niego detekcja YOLO / SCRFD nie zadziała),
- `tkinter` (zazwyczaj jest w standardowej instalacji Pythona na Windows).

Uruchomienie:

```text
python Face-Blur-V66.py
```

Nazwa pliku może się różnić w zależności od wydania. Program wykrywa katalog startowy na podstawie położenia pliku EXE albo pliku `.py`, a nie na podstawie bieżącego katalogu roboczego (CWD). Dzięki temu skan modeli nie ładuje przypadkowych plików `.onnx` z Pulpitu ani z innego katalogu.

### GPU

Program sam wybiera najlepszy dostępny backend i pokazuje go w panelu po prawej, np. `CPU (ORT)`, `CUDA GPU (ORT)`, `DirectML GPU (ORT)` albo `CPU (CV2)` / `CUDA GPU (CV2)`.

Kolejność prób dla modeli ONNX to: CUDA (ONNX Runtime), DirectML, CPU. Jeśli DirectML się nie powiedzie dla danego pliku, program zapamiętuje ten plik i przy kolejnych próbach od razu używa CPU. Dla YuNet program próbuje też backend CUDA w OpenCV DNN, a w razie błędu wraca do CPU.

Brak GPU nie blokuje pracy. Program działa na CPU, tylko wolniej, zwłaszcza przy modelach Seg i przy opcji Dalekie (2x2).

## Układ programu

Okno jest zmaksymalizowane przy starcie. Od góry:

1. **Pasek górny**: wybór pliku, wybór modelu, przyciski AI, zrzut ekranu, eksport.
2. **Pasek śledzenia**: PAUSE / RESUME, wskaźnik śledzenia, Stop gdy zgubi, ZOOM 1:1, Cofnij, Wyczyść, Próg AI, Dalekie (2x2).
3. **Pasek masek**: Gumka maski, Pędzel maski, Rozmiar, Reset maski, Tylko główny obrys, Margines maski.
4. **Środek**: po lewej podgląd wideo / zdjęcia, po prawej panel detektora i lista segmentów.
5. **Sterowanie odtwarzaniem**: PLAY, skoki klatek, oś czasu, znaczniki eksportu.
6. **Pasek parametrów**: suwaki Rozmycie, Wtapianie, Tolerancja skoku, Prędkość odtwarzania, wybór kształtu i typu zamazania.
7. **Pasek statusu**: numer klatki, zoom, liczba aktywnych sylwetek / twarzy, kształt, typ zamazania, ostrzeżenia.

Panel po prawej pokazuje:

- nagłówek `DETEKTOR AI [urządzenie]:`, nazwę modelu, nazwę pliku `.onnx`,
- informacje o pliku: nazwę, rozmiar w pikselach, liczbę klatek i FPS,
- nagłówek `Segmenty i Oś Czasu:` oraz listę trackerów.

## Modele AI

### Lista wbudowana

Lista w programie pokazuje nazwę oraz rozmiar pliku w MB, np. `YOLO26-S Osoba Seg (22.4 MB)`. W języku polskim słowo Person jest tłumaczone na Osoba, reszta nazwy zostaje bez zmian.

| Model na liście | Co wykrywa | Uwagi |
| --- | --- | --- |
| YuNet-Face | Twarze | Szybki, dobry do twarzy z przodu |
| SCRFD 500M-Face | Twarze | Lekki, dobry kompromis szybkość i jakość |
| SCRFD 2.5G-Face | Twarze | Dokładniejszy od 500M, wolniejszy |
| SCRFD 10G-Face | Twarze | Najdokładniejszy SCRFD, najwolniejszy z tej rodziny |
| YOLO-Face (Nano) | Twarze | Mały model YOLO do twarzy |
| YOLO11-Face | Twarze | Generyczny wpis YOLO11 do twarzy |
| YOLO-Person (Box) | Osoby, boxy | Generyczny wpis YOLO do osób, tylko prostokąty |
| YOLO-Person (Seg) | Osoby, obrys | Generyczny wpis YOLO do osób, maski segmentacji |
| YOLO26-N Person Seg | Osoby, obrys | Model YOLO26 Nano, obrys z maski |
| YOLO26-N Person Box | Osoby, boxy | Ten sam plik co Seg, ale tylko boxy, bez masek |

Dodatkowo program ma awaryjny model **Haar Cascade** (`haarcascade_frontalface_default.xml`), używany gdy nie ma pliku ONNX dla danego modelu. Haar wykrywa tylko twarze z przodu, ma minimalny rozmiar wykrywania 20 na 20 px i jest wyraźnie słabszy od modeli ONNX.

### Co oznaczają Box i Seg

- **Box**: model zwraca prostokąt. Zamazanie jest nakładane na prostokąt, elipsę albo na prostokąt ograniczający wielokąt LINE.
- **Seg**: model zwraca maskę segmentacji. Zamazanie jest nakładane **tylko na obrys** z maski. Jeśli maska się nie policzyła, program nie wypełnia boxa. To celowa zasada. Wcześniejsze wersje wypełniały box i dawały czarny kwadrat wokół osoby, obecna wersja tego nie robi: brak obrysu oznacza brak zamazania i ostrzeżenie.

Wybór modelu Osoba (Seg) nie wymusza kształtu BOX. Kształt wybierasz osobno suwakami na dole, ale dla Seg i tak liczy się obrys.

### Sortowanie i ukrywanie duplikatów

Lista jest sortowana po rodzinie, a nie po rozmiarze. Rodziny idą w kolejności: YuNet, SCRFD (500M, 2.5G, 10G), YOLO8, YOLO11, YOLO26 (N, S, M, L, X). W obrębie rodziny kolejność ról to: Face, Box, Seg. Dzięki temu np. YOLO11-N Face, YOLO11-N Person Box i YOLO11-N Person Seg stoją obok siebie.

Program ukrywa generyczny wpis (YOLO-Face (Nano), YOLO11-Face, YOLO-Person (Box), YOLO-Person (Seg)), gdy istnieje precyzyjny wpis na ten sam plik i tę samą rolę, np. YOLO11-N Face zamiast YOLO11-Face. Seg i Box to różne role, więc ten sam plik `-seg` daje dwa wpisy: Seg oraz Box. Jeśli precyzyjnego wpisu nie ma, generyczny wraca na listę.

### Deduplikacja po treści

Dwa pliki o tej samej treści (ten sam SHA256) to dla programu jeden model, nawet jeśli leżą w różnych katalogach albo mają wariant nazwy, np. `yolo11n-face.onnx` i `yolov11n-face.onnx`. Kopia tego samego pliku w `models/` i obok EXE nie tworzy duplikatu, wygrywa katalog `models/`. Inny plik o tej samej nazwie (inna treść, np. własny trening) to inny model i dostaje sufiks z nazwą pliku, a w razie potrzeby numer, np. `(2)`.

### Inspekcja pliku ONNX

Przy skanowaniu program naprawdę otwiera każdy znaleziony plik `.onnx` (ONNX Runtime, tylko CPU, bez inferencji) i czyta jego wyjścia. Sprawdza, czy model ma protos (czy Seg jest prawdziwy), ile kolumn ma wyjście detekcji oraz czy jest to detekcja end-to-end (np. 300 wykryć) czy klasyczna (np. 8400 wykryć).

Dzięki temu:

- plik przemianowany na `*-seg.onnx`, ale bez protos, dostaje tylko wpis Box, a nie Seg,
- plik bez `seg` w nazwie, który naprawdę ma protos, dostaje wpisy Seg oraz Box,
- uszkodzony plik nie wywala skanowania, program wraca do logiki z nazwy pliku.

Wynik inspekcji jest zapisywany w logu konsoli jako `[INSPECT]`, razem z ostrzeżeniem, gdy nazwa `seg` kłamie.

### Przyciski związane z modelem

- **Model:** lista rozwijana. Pokazuje nazwę oraz rozmiar w MB, gdy plik istnieje. Brak pliku oznacza brak rozmiaru.
- **Wskaż...**: ręczne wskazanie pliku `.onnx` dla aktualnie wybranego modelu. Program dopisuje go do listy oraz do `settings.json`. Jeśli wskazany plik to ten sam plik co model wbudowany, program nie tworzy duplikatu, tylko przełącza na wpis wbudowany.
- **Próg AI (%):** suwak 15 do 80, domyślnie 35. Im niższy próg, tym więcej wykryć, ale też więcej fałszywych wykryć. Dla YuNet minimalny próg efektywny to 15.
- **Dalekie (2x2):** dzieli klatkę na 4 kafelki i wykrywa w każdym osobno. Pomaga na małe i dalekie osoby. Wolniejsze, może dawać duplikaty, które program scala po IoU.

## Dodatkowe modele: pobieranie i instalacja

Dodatkowe modele można pobrać tutaj:

**https://github.com/marek7400/FaceBlur/tree/main/models**

Po pobraniu wrzuć plik `.onnx` do katalogu programu. Może to być katalog główny programu albo podkatalog `models`. Zalecany układ:

```text
FaceBlur/
  FaceBlur2.0.exe
  ffmpeg.exe
  ffprobe.exe
  settings.json
  models/
    yolov11n-seg.onnx
    yolo26s-seg.onnx
    scrfd_500m_bnkps.onnx
```

Po wrzuceniu pliku program go załaduje i pokaże na liście modeli. Nie trzeba klikać Wskaż....

Żeby nowy plik pojawił się na liście:

- zrestartuj program, albo
- wymuś odświeżenie listy, np. przełączając język PL/EN albo klikając Wskaż... (odświeżenie listy ponawia skan katalogu).

Zasady nazewnictwa nowych wpisów:

- program buduje krótką nazwę z nazwy pliku: numer YOLO, rozmiar (N, S, M, L, X), cel (Face albo Person) oraz rodzaj (Seg albo Box),
- przykład: `yolo26s-seg.onnx` daje wpisy `YOLO26-S Person Seg` oraz `YOLO26-S Person Box`,
- przykład: `yolo11n-face.onnx` daje wpis `YOLO11-N Face`,
- plik COCO (np. `yolov8n.onnx`, `yolov11n-seg.onnx`, bez słowa `face` w nazwie) nigdy nie dostaje wpisu Face, bo klasa 0 w COCO to osoba, a nie twarz,
- plik YOLO26 nigdy nie dostaje wpisu Face, bo YOLO26 w tym programie to tylko osoba (klasa 0 COCO),
- plik jawnie z twarzą i osobą w nazwie (np. `yolo11n-face-person.onnx`) daje parę Face oraz Person,
- plik face-only (np. `yolov8n-face.onnx`) daje tylko Face, nawet gdy ma `seg` w nazwie, bo maski dotyczą twarzy, a nie osoby,
- plik bez rozpoznawalnej nazwy dostaje długą nazwę w starym formacie: `Baza, nazwa pliku.onnx`, żeby dwa różne pliki `model.onnx` dało się odróżnić.

Wpis własny jest zapisywany w `settings.json` w polu `custom_models`. Jeśli plik zostanie przeniesiony albo usunięty, wpis jest pomijany przy kolejnym starcie. Stare wpisy z błędnym Face dla pliku COCO są czyszczone przy wczytaniu ustawień i budowane od nowa bez Face.

Program szuka modeli w tych katalogach: `models/` obok EXE, katalog EXE, `models/` w `_MEIPASS`, `_MEIPASS`, `models/` obok pliku `.py`, katalog pliku `.py`. Nie skanuje dowolnego CWD ani katalogu domowego. To celowe zabezpieczenie: złośliwy plik `.onnx` podrzucony do przypadkowego katalogu nie załaduje się sam.

## Tryb wideo i tryb zdjęcia

| Funkcja | Wideo | Zdjęcie |
| --- | --- | --- |
| Formaty wejściowe | mp4, mov, avi, mkv, wmv | jpg, jpeg, png, bmp, webp |
| Odtwarzanie, skoki klatek | Tak | Nie |
| Znaczniki eksportu | Tak | Nie |
| Skanowanie całego filmu | Tak | Nie, działa jak Zamaż wszystkie zielone |
| Pauza globalna i pauza segmentu | Tak | Nie |
| Eksport | Wideo mp4 | Zdjęcie w formacie zbliżonym do źródła |
| Zrzut ekranu | Tak, z numerem klatki | Tak, bez numeru klatki |

Po wczytaniu wideo program ustawia prędkość odtwarzania na 10 kl./s, niezależnie od FPS źródła. FPS źródła jest używany do liczenia czasu i do eksportu. Jeśli plik nie zgłasza liczby klatek, program liczy klatki, czytając plik od początku. Może to chwilę potrwać przy długim filmie.

Po wczytaniu zdjęcia suwak czasu, przyciski skoków, PLAY, znaczniki i pauza są wyłączone. Zdjęcie ma zawsze jedną klatkę o numerze 0.

Otwarcie nowego pliku, gdy są trackery albo znaczniki eksportu, pyta o potwierdzenie. Otwarcie nowego pliku bezpowrotnie usuwa niezapisane trackery i znaczniki, bo program nie ma pliku projektu.

## Dodawanie zamazania

### 1. Kliknięcie zielonej ramki

Na zatrzymanej klatce program rysuje aktualne wykrycia na zielono: prostokąty dla modeli Box oraz obrysy dla modeli Seg. Kliknięcie lewym przyciskiem myszy w pobliżu zielonej ramki dodaje tracker dla tego wykrycia. Jeśli ramka jest już trackerem, program jej nie duplikuje (próg IoU 0.72).

Wybierany jest najbliższy kandydat, a przy remisie mniejszy box. Margines kliknięcia to 15 px wokół boxa.

### 2. Przeciągnięcie prostokąta (tryb ręczny)

Przeciągnij lewym przyciskiem myszy pusty obszar. Powstaje tracker ręczny (manual). Minimalny rozmiar to 8 na 8 px. To najlepszy sposób, gdy AI nie wykryła osoby albo gdy chcesz zamazać obiekt, który nie jest twarzą ani osobą.

### 3. Wielokąt LINE

Włącz **LINE** na dole. Klikaj punkty wielokąta, podwójne kliknięcie zamyka wielokąt. Minimalnie 3 punkty, minimalny box 5 na 5 px. Prawy przycisk myszy w trakcie rysowania czyści punkty. Zamazanie jest nakładane na wielokąt, a nie na cały prostokąt.

### 4. Pędzel maski

Włącz **Pędzel maski** i maluj po istniejącej masce albo po pustym miejscu. Malowanie po pustym miejscu tworzy nowy tracker typu Brush. Pędzel działa tylko dla masek segmentacji i dla trackerów Brush.

### 5. Zamaż wszystkie zielone

Przycisk **Zamaż wszystkie zielone** (pomarańczowy) dodaje trackery dla wszystkich zielonych wykryć na bieżącej klatce. Pomija wykrycia już pokryte trackerem.

### 6. Zamaż cały film (AI)

Przycisk **Zamaż cały film (AI)** (różowy) skanuje cały film wybranym modelem i tworzy trackery dla wszystkich wykrytych trajektorii. Przed startem pyta o potwierdzenie i pokazuje liczbę klatek. W trakcie skanowania pokazuje okno postępu z procentem i licznikiem klatek oraz przycisk Anuluj. Anulowanie przerywa skanowanie, ale nie cofa trackerów już utworzonych.

W trybie zdjęcia ten przycisk działa jak Zamaż wszystkie zielone.

Skanowanie łączy wykrycia w trajektorie. Tracker twarzy może przetrwać do 6 klatek bez wykrycia, tracker osoby Seg do 20 klatek. Po przekroczeniu limitu trajektoria się kończy.

## Śledzenie: jak program podąża za osobą

Tracker to nie jest samo wykrycie na jednej klatce. To segment: od klatki startowej do klatki końcowej (albo bez końca), z historią pozycji (`traj`), historią masek (`traj_poly`) oraz informacją o pochodzeniu każdej pozycji.

Pochodzenie pozycji (`traj_prov`):

- `detection`: świeża detekcja AI,
- `csrt`: tracker CSRT / MIL,
- `interp`: interpolacja między dwoma wykryciami twarzy,
- `manual`: ręczna edycja, przeciągnięcie, pędzel, gumka,
- `hold`: stara pozycja, brak świeżej detekcji.

### Kolejność decyzji na kolejnej klatce

Dla osób (Person, Seg i Box) program najpierw próbuje dopasować świeżą detekcję AI do poprzedniej pozycji, z uwzględnieniem prędkości i odległości. Jeśli detekcja pasuje, tracker dostaje nową pozycję z detekcji. Jeśli nie, dla modelu Box próbuje CSRT, a dla osoby Seg nie sięga po CSRT, gdy jest podejrzenie, że osoba wyszła z kadru. Jeśli nic nie pasuje, tracker przechodzi w HOLD, czyli trzyma starą pozycję, ale liczy klatki zgubienia.

Dla twarzy (Face) program łączy CSRT z ponowną detekcją: CSRT podąża co klatkę, a co 8 klatek program próbuje poprawić pozycję świeżą detekcją i odtwarza tracker CSRT od nowej pozycji. Przy dużym skoku (powyżej 3 klatek) program dopasowuje detekcję i interpoluje brakujące klatki jako `interp`.

### Stop gdy zgubi i HOLD

Przełącznik **Stop gdy zgubi** decyduje, co się stanie po zgubieniu:

| Tracker | Stop gdy zgubi: włączone | Stop gdy zgubi: wyłączone |
| --- | --- | --- |
| Twarz | koniec po więcej niż 3 klatkach zgubienia | HOLD do 150 klatek |
| Osoba Seg / Box / Brush | koniec po więcej niż 20 klatkach zgubienia | HOLD do 150 klatek |

HOLD oznacza: zamazanie jest dalej rysowane, ale w starym miejscu. Twarz albo sylwetka mogła się w tym czasie przesunąć poza zamazany obszar. Dlatego HOLD jest liczone osobno i pokazywane jako ostrzeżenie w statusie oraz przy eksporcie.

Gdy wyłączysz Stop gdy zgubi, tracker oznaczony jako `failed_auto` ożywa: program przywraca mu pierwotną klatkę końcową, zeruje licznik zgubienia i próbuje dalej śledzić. To działa też przy przełączaniu w trakcie podglądu i eksportu.

### Krawędzie kadru i przeskoki na inną osobę

Program ma osobne reguły na krawędzie kadru, bo tam najczęściej dochodzi do przeskoku na inną osobę:

- słabe dopasowanie przy krawędzi (IoU poniżej 0.30, środek daleko) jest odrzucane,
- jeśli przy krawędzi jest słaby kandydat (inna osoba wchodzi z tej samej strony), tracker kończy się od razu, zamiast trzymać HOLD i przeskoczyć,
- drugie kolejne nietrafienie przy krawędzi kończy tracker,
- dla skoku powyżej 10 klatek w podglądzie program kroczy klatka po klatce (do 30 klatek), zamiast dopasowywać jednorazowo ze starej pozycji do nowej klatki,
- dla jednorazowego dopasowania przy skoku powyżej 10 klatek wymagane jest IoU co najmniej 0.25, w przeciwnym razie jest HOLD albo STOP, a nie przeskok.

### PAUSE i RESUME

**PAUSE** (globalne) ucina wszystkie aktywne trackery na bieżącej klatce minus 1 i oznacza je jako zapauzowane. Podgląd pokazuje napis `PAUZA ŚLEDZENIA - BRAK ROZMYCIA`, gdy na klatce nie ma aktywnego zamazania. **RESUME** tworzy nowe segmenty dla tych samych `face_id` od bieżącej klatki, próbując najpierw dopasować świeżą detekcję do ostatniego dobrego boxa. Stare segmenty zostają w historii.

**PAUSE** dla jednego segmentu działa tak samo, ale tylko dla tego wiersza na liście. Przycisk na liście pokazuje **PAUSE**, gdy segment jest aktywny, **Wznów**, gdy jest zapauzowany, oraz myślnik (wyłączony), gdy segment zakończył się naturalnie.

Pauza na pierwszej klatce daje segment o końcu wcześniejszym niż start. Taki segment jest dalej pokazywany na liście, żeby dało się go wznowić.

## Edycja na podglądzie: mysz, klawiatura, zoom i pan

### Hover

Najedź kursorem na tracker, a program pokaże uchwyty oraz podświetli wiersz na liście na żółto. Hover działa też dla obrysu segmentacji, nie tylko dla boxa: najpierw sprawdzany jest obrys, potem box. Jeśli najedziesz na obrys, ale nie na uchwyt, kursor jest domyślnie do przesuwania, a wiersz i tak się podświetli.

Kursory:

- środek boxa: przesuwanie,
- krawędź: zmiana rozmiaru w poziomie albo w pionie,
- narożnik: zmiana rozmiaru po przekątnej,
- tryb LINE: celownik,
- tryb Gumka / Pędzel: kółko o rozmiarze pędzla.

### Przeciąganie i zmiana rozmiaru

- **Środek**: przeciągnij, aby przesunąć tracker.
- **Krawędź / narożnik**: przeciągnij, aby zmienić rozmiar. Minimalny rozmiar to 10 px.
- Przeciągnięcie na klatce startowej zmienia `init_bbox` trackera.
- Przeciągnięcie na późniejszej klatce dzieli tracker na dwa segmenty o tym samym `face_id`: stary kończy się na bieżącej klatce minus 1, nowy zaczyna się na bieżącej klatce z nową pozycją. Przyszłe ręczne edycje są zachowane w nowym segmencie.
- W trakcie przeciągania przyszły cache nie jest czyszczony co klatkę myszy, tylko przy puszczeniu przycisku.

### Usuwanie

- **Prawy przycisk myszy** na trackerze usuwa ten tracker. Jeśli zoom jest większy niż 1.0, prawy przycisk najpierw działa jako pan, a kliknięcie bez przeciągnięcia usuwa tracker.
- **X** na liście usuwa jeden segment.
- **Cofnij** usuwa ostatni dodany tracker. Jeśli usunięty tracker był kontynuacją wcześniejszego segmentu, program łączy poprzedni segment z powrotem (przywraca mu klatkę końcową).
- **Wyczyść** usuwa wszystkie trackery po potwierdzeniu. Pyta o potwierdzenie i pokazuje liczbę trackerów.

### Zoom i pan

- **Scroll (kółko myszy)**: zoom 1.0 do 12.0, krok razy 1.25, zoom do kursora. Powrót blisko 1.0 przyciąga do dokładnie 1.0.
- **ZOOM 1:1**: reset zoomu do 1.0 i wyśrodkowanie kadru.
- **Środkowy przycisk myszy** albo **Shift + lewy przycisk**: pan (przeciąganie widoku).
- **Prawy przycisk przy zoomie**: pan, jak wyżej.

### Klawiatura

| Klawisz | Działanie |
| --- | --- |
| Strzałka w lewo / w prawo | Klatka minus 1 / plus 1 |
| Shift + strzałka w lewo / w prawo | Klatka minus 10 / plus 10 |
| Strzałka w górę | Klatka plus 30 |
| Strzałka w dół | Klatka minus 30 |
| Delete | Cofnij (usuń ostatni tracker) |

Skróty działają w oknie głównym. Wpisanie numeru klatki w polu obok suwaka i naciśnięcie Enter też przenosi do tej klatki.

## Lista Segmenty i Oś Czasu

Każdy wiersz na liście to jeden segment. Wiersz pokazuje:

- typ: **Osoba** albo **Twarz**,
- numer `face_id`,
- rodzaj: `Seg`, `Brush`, pierwsza część nazwy modelu dla AUTO, albo pierwsza litera typu dla ręcznych,
- zakres klatek: `[start-koniec]`, gdzie brak końca oznacza segment otwarty,
- znacznik `PAUZA`, gdy segment jest zapauzowany na bieżącej klatce.

Przyciski w wierszu (od prawej):

- **X**: usuń segment,
- **IDŹ**: skocz do klatki startowej segmentu (tylko wideo),
- **PAUSE / Wznów / myślnik**: pauza albo wznowienie tylko tego segmentu.

Kolor wiersza: zielonkawy oznacza segment aktywny na bieżącej klatce, szary oznacza segment nieaktywny. Tryb ciemny używa ciemniejszych odpowiedników. Hover na podglądzie podświetla wiersz na żółto / pomarańczowo i delikatnie przewija listę, żeby wiersz był widoczny.

## Odtwarzanie i nawigacja

- **PLAY / STOP**: odtwarza od bieżącej klatki z prędkością z suwaka. Jeśli jesteś na ostatniej klatce, odtwarzanie zaczyna od początku. Odtwarzanie używa zegara ściennego, a nie FPS źródła. Przeciągnięcie suwaka czasu albo zmiana klatki zatrzymuje odtwarzanie.
- **W tył / W przód**: przyciski skoków: minus 30, 20, 10, 5, 2, 1 oraz plus 1, 2, 5, 10, 20, 30.
- **Suwak czasu**: przeciągnięcie pokazuje klatkę bez detekcji (lekki podgląd), puszczenie przeładowuje klatkę z detekcją.
- **Pole numeru klatki**: wpisz numer i naciśnij Enter.
- **Pasek znaczników**: cienki pasek nad suwakiem. Zielone pola to zakresy eksportu, żółta linia to tymczasowy znacznik OD, czerwona linia to bieżąca klatka. Kliknięcie na pasek przenosi do tej klatki.
- **Przyciski |<< i >>|**: skok na początek oraz na koniec filmu.
- **Licznik czasu**: dokładny czas bieżącej klatki w formacie `GG:MM:SS.mmm`.

Pasek statusu pokazuje m.in. liczbę aktywnych zamazań na klatce oraz ostrzeżenia `BEZ BLUR` i `HOLD (stary box)`.

## Znaczniki eksportu

Znaczniki służą do eksportu tylko wybranych fragmentów filmu.

1. Przejdź do klatki początkowej i kliknij **Eksport OD**.
2. Przejdź do klatki końcowej i kliknij **Eksport DO**.
3. Powtórz dla kolejnych fragmentów.

Zasady:

- jeśli klikniesz Eksport DO bez wcześniejszego OD, program przyjmuje OD jako klatkę 0,
- zakresy są sortowane i łączone, gdy stykają się albo nachodzą na siebie (łączenie z tolerancją 1 klatki),
- **Kasuj znaczniki** usuwa zakres, w którym znajduje się bieżąca klatka. Przycisk jest aktywny tylko wtedy, gdy bieżąca klatka jest w jakimś zakresie,
- brak zakresów oznacza eksport całego filmu,
- przy eksporcie zakresów dźwięk jest cięty i łączony dla każdego zakresu osobno.

## Wygląd zamazania: kształt, typ i suwaki

### Kształt

Wybór jest wzajemnie wykluczający. Wybrany kształt jest podświetlony.

- **CIRCLE (OWAL)**: elipsa wpisana w box. Domyślny kształt.
- **BOX (PROSTOKĄT)**: cały prostokąt.
- **LINE (WIELOKĄT)**: wielokąt rysowany kliknięciami. Dla trackerów Seg i tak liczy się obrys maski, a LINE jest osobnym sposobem rysowania wielokąta.

Dla trackerów Seg kształt CIRCLE / BOX nie zastępuje obrysu. Obrys maski ma pierwszeństwo.

### Typ zamazania

Wybór jest wzajemnie wykluczający.

- **PIXEL**: pikselizacja. Blok pikseli rośnie wraz z suwakiem Rozmycie. Domyślny typ.
- **GAUSS**: rozmycie gaussowskie. Jądro rośnie wraz z suwakiem Rozmycie. Dla dużych obszarów program najpierw zmniejsza ROI, rozmywa i powiększa z powrotem, żeby zachować wydajność.
- **BLACK**: czarny prostokąt / czarna maska, bez rozmycia. Wtapianie nie działa dla BLACK.

### Suwaki na dole

| Suwak | Zakres | Domyślnie | Co robi |
| --- | --- | --- | --- |
| Rozmycie | 0 do 100 | 50 | Siła zamazania. Dla PIXEL: rozmiar bloku (od 2 do 40 px). Dla GAUSS: rozmiar jądra (od 3 do 151 px, zawsze nieparzyste) |
| Wtapianie | 0 do 100 | 15 | Miękka krawędź maski. 0 oznacza ostrą krawędź. Dla BLACK jest ignorowane |
| Tolerancja skoku | 0 do 300 | 130 | Jak daleko twarz może skoczyć między klatkami i dalej być tą samą twarzą. Używana w dopasowaniu twarzy oraz w eksporcie |
| Prędkość odtwarzania (kl./s) | 0.5 do 120, krok 0.5 | 10.0 | Prędkość odtwarzania podglądu. Nie zmienia FPS eksportu |

Wszystkie suwaki obsługują też kółko myszy po kliknięciu w suwak (focus), krok 1, a dla prędkości krok 0.5. Wartość prędkości można też wpisać w polu obok suwaka (Enter albo utrata focusu).

Zmiana suwaków Rozmycie i Wtapianie od razu odświeża bieżącą klatkę.

## Maski segmentacji: pędzel, gumka i margines

Ta sekcja dotyczy tylko modeli Seg oraz trackerów Brush. Dla modeli Box pędzel i gumka nie mają maski do edycji.

- **Pędzel maski**: domalowuje obszar do maski. Przeciągnięcie maluje linię o grubości zależnej od suwaka Rozmiar. Malowanie na nowej pozycji rozszerza box trackera do nowej maski. Malowanie po pustym miejscu tworzy nowy tracker Brush.
- **Gumka maski**: wycina obszar z maski. Przeciągnięcie wycina pas o grubości zależnej od suwaka Rozmiar. Pojedyncze kliknięcie (bez przeciągnięcia) usuwa tylko wyspę, w którą klikniesz, o ile maska ma więcej niż jeden kontur. Dzięki temu jednym kliknięciem usuniesz np. drugą osobę wciągniętą do maski, a nie wytniesz dziury w głównej sylwetce.
- **Rozmiar**: suwak 8 do 80, domyślnie 24. To promień pędzla / gumki w pikselach oryginalnej klatki, a nie w pikselach ekranu. Na podglądzie widać czerwone (gumka) albo zielone (pędzel) kółko o tym rozmiarze.
- **Tylko główny obrys**: gdy włączone, program zostawia tylko największy kontur maski. Gdy wyłączone, maska może mieć wiele konturów (wysp). Domyślnie włączone.
- **Margines maski**: suwak minus 4 do plus 4, domyślnie 0. Dodatni margines rozszerza maskę, ujemny ją zwęża. Przydaje się, gdy obrys jest za ciasny i widać krawędź twarzy albo za luźny i zamazuje za dużo tła.
- **Reset maski**: przywraca oryginalną maskę AI na bieżącej klatce, czyli cofa ręczne poprawki pędzlem i gumką na tej klatce. Jeśli maska jest już w stanie oryginalnym, program to zgłasza i nic nie zmienia.

Ręczna edycja maski jest zapamiętywana jako `manual_poly_frames` oraz jako oryginalna maska AI (`ai_orig_poly` / `ai_orig_bbox`). Dzięki temu program potrafi przenieść poprawkę (różnicę między maską ręczną a oryginalną) na kolejne klatki: to, co domalowałeś, jest dodawane do nowej maski AI, a to, co wyciąłeś gumką, jest z niej usuwane. Jeśli nowy box różni się od oryginalnego ponad 1.8 raza w szerokości albo wysokości, program nie przenosi poprawki i używa czystej maski AI.

Edycja maski unieważnia przyszły cache trackera od tej klatki, żeby kolejne klatki nie używały starej maski.

## Zrzut ekranu

Przycisk **Zrzut ekranu** zapisuje bieżącą klatkę do pliku JPG albo PNG.

- Checkbox **z blur** (obok przycisku zrzutu) decyduje, czy zrzut ma zamazanie. Domyślnie włączony.
- Jeśli zoom jest większy niż 1.0, zrzut jest przycięty do widocznego fragmentu (widoczny crop), a nazwa pliku zaczyna się od `zoom-`.
- Nazwa pliku: `nazwa-filmu-zamazane-000123.jpg` dla wideo (numer klatki z zerami wiodącymi) albo `nazwa-zdjecia-zamazane.jpg` dla zdjęcia.
- Sufiks `-zamazane` jest dodawany tylko wtedy, gdy klatka naprawdę ma pokrycie. Brak pokrycia oznacza brak sufiksu, żeby nazwa nie kłamała.
- Nie można nadpisać pliku źródłowego. Program odrzuca taką próbę i pokazuje błąd.
- Jeśli klatka ma trackery HOLD albo BEZ BLUR, program pokazuje ostrzeżenie po zapisie.

## Eksport wideo i zdjęcia

### Przycisk Eksportuj / Zapisz

- Checkbox **z blur** (obok przycisku eksportu) decyduje, czy eksport ma zamazanie. Domyślnie włączony. Jeśli jest wyłączony albo nie ma trackerów, eksport zapisuje czysty film albo zdjęcie.
- Dla wideo program pyta o nazwę pliku MP4, dla zdjęcia pyta o nazwę pliku graficznego.
- Nie można nadpisać pliku źródłowego. Program odrzuca taką próbę.
- Nazwa domyślna dla wideo: `nazwa-zamazane-MMSS-MMSS.mp4` dla zakresów albo `nazwa-zamazane.mp4` dla całego filmu. Jeśli eksport jest bez zamazania i bez zakresów, nazwa to `nazwa-kopia.mp4`.
- Eksport wideo działa w wątku w tle, z oknem postępu i przyciskiem Anuluj. Zamknięcie okna w trakcie eksportu anuluje wątek i czeka do 10 sekund na jego zakończenie. Jeśli wątek dalej żyje, okno się nie zamknie, trzeba spróbować ponownie po chwili.

### Jak działa eksport wideo

1. Program czyta film od początku klatka po klatce.
2. Dla każdej klatki w zakresie eksportu (albo dla całego filmu, gdy nie ma zakresów) nakłada zamazanie dla aktywnych trackerów.
3. Wideo jest zapisywane kodekiem `mp4v` do pliku tymczasowego o unikalnej nazwie (`._fb_tmp_*.mp4`).
4. Jeśli jest `ffmpeg.exe` i  `ffprobe.exe`, program scala wideo z dźwiękiem ze źródła do pliku `._fb_ff_*.mp4`, a potem zamienia plik tymczasowy na docelowy. Pliki tymczasowe są usuwane.
5. Jeśli nie ma `ffmpeg.exe` i `ffprobe.exe`, program zapisuje samo wideo bez dźwięku i dodaje ostrzeżenie.

Dźwięk:

- cały film: kopiowanie dźwięku albo ponowne kodowanie do AAC 192k, gdy kopiowanie się nie powiedzie,
- jeden zakres: wycinanie dźwięku `atrim` od startu do końca zakresu, kodowanie AAC 192k,
- wiele zakresów: wycinanie i łączenie dźwięku z każdego zakresu filtrem `concat`,
- brak dźwięku w źródle: program zapisuje wideo bez dźwięku, bez błędu.

Program sprawdza liczbę klatek w pliku wynikowym. Jeśli jest inna niż oczekiwana, pokazuje ostrzeżenie o przerwanym zapisie (pełny dysk, błąd kodeka) i prosi o weryfikację przed publikacją.

### Eksport zdjęcia

Eksport zdjęcia zapisuje bieżącą klatkę z zamazaniem dla trackerów na klatce 0. Format wynikowy jest dobierany na podstawie źródła (JPG, PNG, BMP, WEBP), a przy nieznanym rozszerzeniu program używa JPG. Ostrzeżenia HOLD, BEZ BLUR i UNCOVERED działają tak samo jak przy eksporcie wideo.

## Ustawienia: plik settings.json

Ustawienia są zapisywane przy zamknięciu programu do pliku `settings.json` w katalogu programu. Zapisywane są:

- Rozmycie,
- Wtapianie,
- Tolerancja skoku,
- Próg AI,
- Rozmiar gumki / pędzla,
- Tylko główny obrys,
- Margines maski,
- Własne modele (`custom_models`).

Nie są zapisywane:

- trackery (segmenty),
- znaczniki eksportu,
- wybrany model (program startuje z modelem domyślnym YuNet-Face, a lista jest odświeżana),
- kształt i typ zamazania (program startuje z CIRCLE i PIXEL),
- prędkość odtwarzania (program startuje z 10 kl./s),
- bieżący plik.

Jeśli plik `settings.json` jest uszkodzony albo ma zły typ pola, program używa wartości domyślnych i wypisuje błąd w konsoli, zamiast się wywalić.

## Język i motyw

- **PL/EN**: przełącza język całego interfejsu między polskim a angielskim. Język startowy jest wykrywany z języka systemu (Windows: polski, gdy język UI to 0x15; w przeciwnym razie angielski, z fallbackiem na zmienną LANG / locale). Przełączenie języka odświeża też listę modeli, więc nowy plik w `models/` pojawi się po przełączeniu języka.
- **Ciemny / jasny motyw** (przycisk ze słońcem i księżycem): przełącza kolory interfejsu, listy, panelu i okien dialogowych. Nie zmienia samego wideo.
- Link **GitHub: marek7400** otwiera profil autora w przeglądarce.

## Ostrzeżenia i zasada fail-closed

Program działa w trybie fail-closed: plik wyjściowy nigdy nie zawiera klatki, która powinna być zamazana, bez ostrzeżenia. Stany klatki:

- **BLURRED**: zamazanie nałożone ze świeżej detekcji, CSRT albo ręcznej edycji.
- **HOLD**: zamazanie nałożone w starym miejscu, brak świeżej detekcji, tracker zgubiony. Twarz albo sylwetka mogła się przesunąć poza zamazany obszar.
- **UNBLURRED**: aktywny tracker nie ma boxa na tej klatce, zamazanie nie zostało nałożone.
- **UNCOVERED**: klatka nie ma żadnego trackera pokrywającego. Plik może być bez zamazania.
- **ERASED_INTENTIONAL**: maska celowo wymazana gumką do pustej listy. To nie jest błąd, tylko świadome usunięcie zamazania.

Gdzie widać ostrzeżenia:

- pasek statusu w podglądzie: `BEZ BLUR: N` oraz `HOLD (stary box): N`,
- okno po eksporcie wideo: liczba klatek BEZ BLUR, liczba klatek HOLD, przykładowe numery klatek, błędy detekcji AI, ostrzeżenia o liczbie klatek i o dźwięku,
- okno po eksporcie zdjęcia i po zrzucie ekranu: te same ostrzeżenia dla jednej klatki,
- przed eksportem zakresów: jeśli żaden zakres nie ma trackera pokrywającego, program pyta, czy na pewno eksportować czysty film.

Jeśli program pokazuje HOLD albo BEZ BLUR, nie publikuj bez obejrzenia tych klatek. W praktyce oznacza to: przewiń do wskazanych klatek, popraw ręcznie albo wznów tracker i wyeksportuj ponownie.

## Znane problemy i ograniczenia

Przeczytaj tę sekcję przed publikacją filmu z osobami, zwłaszcza z dziećmi. Żadne z tych ograniczeń nie jest naprawiane przez samo ponowne wyeksportowanie tego samego pliku.

1. **AI nie wykrywa wszystkich osób.** Zasłonięte twarze, plecy, małe osoby w tle, osoby na krawędzi kadru, szybki ruch i słabe światło to najczęstsze braki. Model Seg widzi sylwetki lepiej niż model Face, ale też gubi.
2. **Tracker może przeskoczyć na inną osobę.** Zwłaszcza gdy dwie osoby się mijają, jedna wychodzi z kadru, a druga wchodzi z tej samej strony, albo gdy skoczysz o 20 do 30 klatek. Program ma reguły anty-przeskokowe, ale nie są doskonałe. Sprawdź klatki po mijaniu się osób.
3. **HOLD to nie pewny blur.** Zamazanie w starym miejscu wygląda na podglądzie jak zamazanie, ale osoba mogła się przesunąć. Traktuj HOLD jako do sprawdzenia.
4. **Seg bez maski oznacza brak blur.** Program celowo nie wypełnia boxa, gdy nie ma obrysu. To lepsze niż czarny kwadrat, ale oznacza, że klatka może być bez zamazania. Sprawdź ostrzeżenia.
5. **Brak pliku projektu.** Trackery i znaczniki nie są zapisywane. Zamknięcie programu albo otwarcie nowego pliku usuwa pracę. Eksportuj od razu po skończeniu.
6. **Cofnij usuwa tylko ostatni tracker.** Nie ma pełnego stosu cofania dla edycji maski, przeciągnięć ani znaczników. Cofnięcie edycji maski to Reset maski na bieżącej klatce.
7. **Brak dźwięku bez ffmpeg.exe i `ffprobe.exe`.** Program zapisze wideo bez dźwięku i pokaże ostrzeżenie. Dźwięk wymaga `ffmpeg.exe` i `ffprobe.exe` obok programu.
8. **Wydajność.** Modele Seg, Dalekie (2x2) i wideo 4K są wyraźnie wolniejsze na CPU. Skanowanie całego filmu może trwać długo. Zmniejsz próg AI tylko wtedy, gdy naprawdę trzeba, bo niższy próg to więcej wykryć do przetworzenia.
9. **Ścieżki z polskimi znakami.** Program próbuje zamienić ścieżkę na krótką ścieżkę 8.3 dla OpenCV na Windows. W większości przypadków działa, ale ścieżki z nietypowymi znakami, długie ścieżki i dyski sieciowe to nadal najczęstsze źródło błędów otwierania i zapisu.
10. **Odczyt wideo może się skończyć przedwcześnie.** Uszkodzony plik, zmienna liczba klatek albo błąd kodeka mogą sprawić, że eksport zapisze mniej klatek niż deklaruje nagłówek. Program ostrzega o tym po eksporcie i pokazuje liczbę zapisanych klatek.
11. **Podgląd to nie eksport.** Podgląd używa cache detekcji i lekkiego śledzenia. Eksport przelicza klatki od początku z detekcją. Jeśli podgląd i eksport się różnią, wierz eksportowi i jego ostrzeżeniom.
12. **Zamykanie w trakcie eksportu / skanowania.** Program anuluje wątki w tle i czeka do 10 sekund. Jeśli wątek dalej żyje, okno się nie zamknie. Nie wymuszaj zamknięcia, poczekaj na koniec wątku i spróbuj ponownie.
13. **Model Haar jest słaby.** To tylko awaryjny fallback. Nie używaj go do publikacji, jeśli zależy ci na pełnym zamazaniu.
14. **Eksport nie nadpisuje źródła.** To celowe zabezpieczenie. Wybierz inną nazwę pliku.
15. **Brak obsługi napisów, rozdziałów i metadanych.** Eksport zachowuje obraz i dźwięk, ale nie kopiuje napisów ani metadanych ze źródła.

## Rozwiązywanie problemów

**Nie widzę modelu na liście.**

- Sprawdź, czy plik `.onnx` leży w katalogu programu albo w `models/`, a nie na Pulpicie.
- Zrestartuj program albo przełącz język PL/EN, żeby odświeżyć listę.
- Sprawdź, czy plik nie jest duplikatem innego pliku (ten sam SHA256). Duplikat nie dostaje osobnego wpisu.
- Sprawdź, czy plik nie jest uszkodzony. Program loguje `[INSPECT]` w konsoli przy skanowaniu.
- Sprawdź, czy wpis nie jest ukryty jako generyczny. Jeśli istnieje precyzyjny wpis na ten sam plik, generyczny jest chowany celowo.

**Model nie wykrywa osób.**

- Obniż Próg AI, np. do 20 do 25.
- Włącz Dalekie (2x2) dla małych osób w tle.
- Przełącz na model Seg zamiast Face, gdy chcesz zamazać całe sylwetki.
- Sprawdź, czy nie wybrałeś modelu Face dla pliku COCO. Plik COCO nie ma wykrywania twarzy.

**Zamazanie to czarny kwadrat wokół osoby.**

- To zachowanie sprzed starej wersji. W 2.0 (V66) model Seg nigdy nie wypełnia boxa, gdy nie ma obrysu. Jeśli widzisz czarny kwadrat, sprawdź, czy masz włączony typ BLACK. Przełącz na PIXEL albo GAUSS.
- Jeśli masz stary plik z maską bez obrysu, usuń tracker i dodaj go ponownie modelem Seg.

**Tracker przeskoczył na inną osobę.**

- Cofnij tracker (Cofnij albo X na liście), wróć do klatki przed przeskokiem i dodaj tracker ponownie.
- Nie skacz o 20 do 30 klatek w trakcie sprawdzania, tylko odtwarzaj albo skacz o 1 do 10 klatek.
- Włącz Stop gdy zgubi, żeby tracker kończył się zamiast trzymać HOLD i przeskakiwać.

**Eksport jest bez dźwięku.**

- Połóż `ffmpeg.exe` i i `ffprobe.exe` obok programu i zrestartuj program. Przy starcie nie powinien pokazać się dialog o braku FFmpeg.
- Sprawdź, czy źródło w ogóle ma dźwięk. Jeśli źródło nie ma ścieżki audio, eksport bez dźwięku jest poprawny.
- Sprawdź ostrzeżenie po eksporcie. Jeśli scalanie audio się nie powiodło, program zapisuje wideo bez dźwięku i to zgłasza.

**Program nie zamyka się.**

- Poczekaj na koniec eksportu albo skanowania. Program czeka do 10 sekund na wątki w tle.
- Jeśli okno dalej się nie zamyka, spróbuj ponownie po chwili. Nie zamykaj okna wymuszeniem w trakcie zapisu, bo możesz dostać niekompletny plik.

**Zrzut ekranu ma złą nazwę albo brak sufiksu -zamazane.**

- To celowe. Sufiks jest dodawany tylko wtedy, gdy klatka ma pokrycie. Brak sufiksu oznacza, że zrzut może być bez zamazania. Sprawdź ostrzeżenie po zapisie.

**Podgląd jest wolny.**

- Wyłącz Dalekie (2x2), przełącz na lżejszy model (YuNet albo SCRFD 500M), zmniejsz zoom, zamknij inne programy.
- Skanowanie całego filmu w tle i tak będzie wolniejsze na CPU niż na GPU.

## Prywatność

Program przetwarza pliki lokalnie na komputerze. Nie wysyła wideo, zdjęć ani klatek do serwera. Modele AI są plikami lokalnymi `.onnx`. Jedynym ruchem sieciowym jest otwarcie linku GitHub w przeglądarce po kliknięciu oraz linków do pobrania FFmpeg w oknie dialogowym przy starcie (gdy brakuje `ffmpeg.exe`).

## Pliki w repozytorium

Typowy układ, który warto trzymać w repozytorium:

```text
FaceBlur/

  Face-Blur-V66-...py
  models/
    yolov11n-seg.onnx
    yolo26n-seg.onnx
  ffmpeg.exe
  ffprobe.exe
  settings.json
```

Plik `settings.json` jest tworzony przy zamknięciu programu. Nie musisz go dodawać ręcznie.

---

<img width="1920" height="1162" alt="en" src="https://github.com/user-attachments/assets/d5e52729-ec36-49b3-9786-8b0b1891f16a" />

# Face & Person Blur Tool

> Version described in this file: **2.0 (V66)**. 
> Extra models repo: [github.com/marek7400/FaceBlur/tree/main/models](https://github.com/marek7400/FaceBlur/tree/main/models).

## Table of Contents

- [What it is](#what-it-is)
- [Quick start](#quick-start)
- [Requirements and installation](#requirements-and-installation)
- [Layout](#layout)
- [AI models](#ai-models)
- [Extra models: download and install](#extra-models-download-and-install)
- [Video mode and image mode](#video-mode-and-image-mode)
- [Adding blur](#adding-blur)
- [Tracking: how the program follows a person](#tracking-how-the-program-follows-a-person)
- [Editing on the preview: mouse, keyboard, zoom and pan](#editing-on-the-preview-mouse-keyboard-zoom-and-pan)
- [Segments and Timeline list](#segments-and-timeline-list)
- [Playback and navigation](#playback-and-navigation)
- [Export markers](#export-markers)
- [Blur look: shape, type and sliders](#blur-look-shape-type-and-sliders)
- [Segmentation masks: brush, eraser and margin](#segmentation-masks-brush-eraser-and-margin)
- [Screenshot](#screenshot)
- [Video and image export](#video-and-image-export)
- [Settings: settings.json](#settings-settingsjson)
- [Language and theme](#language-and-theme)
- [Warnings and the fail-closed rule](#warnings-and-the-fail-closed-rule)
- [Known issues and limitations](#known-issues-and-limitations)
- [Troubleshooting](#troubleshooting)
- [Privacy](#privacy)

## What it is

Face & Person Blur Tool is a local desktop program (a single Python file, Tkinter UI) for blurring faces and whole bodies in videos and photos.

It is not a general video editor. It does one job, carefully: it detects objects with an AI model, turns each detection into a tracker (a segment), follows it frame by frame, and at export time applies blur only where the tracker has coverage. If there is no coverage, it warns you instead of silently exporting a clean fragment.

Main features:

- face and person detection with ONNX models: YuNet, SCRFD, YOLO Face, YOLO Person Box, YOLO Person Seg, YOLO26,
- tracking: repeated AI detection, CSRT tracker (MIL as fallback), interpolation and HOLD mode,
- whole-body blur along the segmentation outline, not along a rectangle,
- manual drawing: rectangle, LINE polygon, mask brush, mask eraser,
- global pause and per-segment pause,
- export of marked fragments (FROM and TO markers) or of the whole video, with audio via FFmpeg,
- screenshots with or without blur,
- Polish and English UI, light and dark theme,
- everything runs locally, no files are sent to the cloud.

## Quick start

1. If you want additional models, place a `models` folder containing `.onnx` files next to the program. You can also include newer versions of the `ffmpeg.exe` and `ffprobe.exe` files than those included with the program.
2. Run the program and click **Choose video** or **Choose image**.
3. Pick a model from the list, for example **YOLO-Person (Seg)**, and click **Blur all green**, or click a single green frame, or click **Blur whole video (AI)**.
4. Go through the video frame by frame. Fix what the AI lost: drag the frame, paint the mask with the brush, cut with the eraser, resume the segment.
5. Set export markers or export the whole video with **Export / Save**.
6. Watch the result before publishing. Pay special attention to frames where the program showed HOLD or NO BLUR warnings.

## Requirements and installation

### EXE version (recommended for end users)

You only need the program folder. Next to the EXE file there should be:

| File / folder | What it is for | Required |
| --- | --- | --- |
| Program EXE | Launching | Yes |
| `models/` with `.onnx` files | AI models | Yes, at least one |
| `ffmpeg.exe` (and `ffprobe.exe`) | Audio in video exports | No, but without it exports have no audio |
| `settings.json` | Saved settings and custom models | No, created on close |
| `haarcascade_frontalface_default.xml` | Fallback Haar Cascade model | No |

The program looks for `ffmpeg.exe` i `ffprobe.exe` in this order: program folder, `_MEIPASS` folder (onefile build), `bin` subfolder, system PATH. If it does not find it, it shows a startup dialog with FFmpeg download links (BtbN and gyan.dev).

### Running from source (.py)

You need Python 3 and these libraries:

- `opencv-python` (cv2, including CSRT / MIL trackers),
- `numpy`,
- `Pillow` (PIL),
- `onnxruntime` (for `.onnx` models; without it YOLO / SCRFD detection does not work),
- `tkinter` (usually included in a standard Python install on Windows).

Run:

```text
python Face-Blur-V66.py
```

The file name may differ by release. The program detects its start folder from the location of the EXE or the `.py` file, not from the current working directory (CWD). This way model scanning does not load random `.onnx` files from the Desktop or from another folder.

### GPU

The program picks the best available backend itself and shows it in the right panel, for example `CPU (ORT)`, `CUDA GPU (ORT)`, `DirectML GPU (ORT)`, or `CPU (CV2)` / `CUDA GPU (CV2)`.

For ONNX models the try order is: CUDA (ONNX Runtime), DirectML, CPU. If DirectML fails for a given file, the program remembers that file and goes straight to CPU next time. For YuNet it also tries the CUDA backend in OpenCV DNN, and falls back to CPU on error.

No GPU does not block work. The program runs on CPU, only slower, especially with Seg models and with the Far (2x2) option.

## Layout

The window is maximized at startup. From top to bottom:

1. **Top bar**: file choice, model choice, AI buttons, screenshot, export.
2. **Tracking bar**: PAUSE / RESUME, tracking indicator, Stop on loss, ZOOM 1:1, Undo, Clear, AI threshold, Far (2x2).
3. **Mask bar**: Mask eraser, Mask brush, Size, Reset mask, Only main outline, Mask margin.
4. **Middle**: video / photo preview on the left, detector panel and segment list on the right.
5. **Playback controls**: PLAY, frame jumps, timeline, export markers.
6. **Parameters bar**: sliders for Blur, Feathering, Jump tolerance, Playback speed, shape and blur type choice.
7. **Status bar**: frame number, zoom, number of active silhouettes / faces, shape, blur type, warnings.

The right panel shows:

- header `AI DETECTOR [device]:`, model name, `.onnx` file name,
- file info: name, size in pixels, frame count and FPS,
- header `Segments and Timeline:` and the tracker list.

## AI models

### Built-in list

The list shows the name plus file size in MB, for example `YOLO26-S Person Seg (22.4 MB)`. In Polish, the word Person is translated to Osoba, the rest of the name stays the same.

| Model in the list | What it detects | Notes |
| --- | --- | --- |
| YuNet-Face | Faces | Fast, good for frontal faces |
| SCRFD 500M-Face | Faces | Light, good speed and quality balance |
| SCRFD 2.5G-Face | Faces | More accurate than 500M, slower |
| SCRFD 10G-Face | Faces | Most accurate SCRFD, slowest in this family |
| YOLO-Face (Nano) | Faces | Small YOLO face model |
| YOLO11-Face | Faces | Generic YOLO11 face entry |
| YOLO-Person (Box) | People, boxes | Generic YOLO person entry, rectangles only |
| YOLO-Person (Seg) | People, outlines | Generic YOLO person entry, segmentation masks |
| YOLO26-N Person Seg | People, outlines | YOLO26 Nano model, outline from mask |
| YOLO26-N Person Box | People, boxes | Same file as Seg, but boxes only, no masks |

The program also has a fallback model, **Haar Cascade** (`haarcascade_frontalface_default.xml`), used when there is no ONNX file for a given model. Haar detects only frontal faces, has a minimum detection size of 20 by 20 px, and is clearly weaker than the ONNX models.

### What Box and Seg mean

- **Box**: the model returns a rectangle. Blur is applied to the rectangle, the ellipse, or the bounding box of a LINE polygon.
- **Seg**: the model returns a segmentation mask. Blur is applied **only to the outline** from the mask. If the mask was not computed, the program does not fill the box. This is a deliberate rule. Older versions filled the box and produced a black square around the person; the current version does not do that: no outline means no blur, plus a warning.

Choosing a Person (Seg) model does not force the BOX shape. You pick the shape separately with the controls at the bottom, but for Seg the mask outline still wins.

### Sorting and hiding duplicates

The list is sorted by family, not by size. Family order is: YuNet, SCRFD (500M, 2.5G, 10G), YOLO8, YOLO11, YOLO26 (N, S, M, L, X). Inside a family, role order is: Face, Box, Seg. So for example YOLO11-N Face, YOLO11-N Person Box and YOLO11-N Person Seg stand next to each other.

The program hides a generic entry (YOLO-Face (Nano), YOLO11-Face, YOLO-Person (Box), YOLO-Person (Seg)) when a precise entry exists for the same file and the same role, for example YOLO11-N Face instead of YOLO11-Face. Seg and Box are different roles, so the same `-seg` file gives two entries: Seg and Box. If there is no precise entry, the generic entry returns to the list.

### Deduplication by content

Two files with the same content (same SHA256) are one model for the program, even if they sit in different folders or have a name variant, for example `yolo11n-face.onnx` and `yolov11n-face.onnx`. A copy of the same file in `models/` and next to the EXE does not create a duplicate; the `models/` folder wins. A different file with the same name (different content, for example your own training) is a different model and gets a suffix with the file name, and a number if needed, for example `(2)`.

### ONNX file inspection

When scanning, the program really opens every `.onnx` file it finds (ONNX Runtime, CPU only, no inference) and reads its outputs. It checks whether the model has protos (whether Seg is real), how many columns the detection output has, and whether it is end-to-end detection (for example 300 detections) or classic detection (for example 8400 detections).

Because of this:

- a file renamed to `*-seg.onnx`, but without protos, gets only a Box entry, not Seg,
- a file without `seg` in the name, which really has protos, gets Seg and Box entries,
- a damaged file does not crash scanning; the program falls back to name-based logic.

The inspection result is written to the console log as `[INSPECT]`, with a warning when the `seg` name lies.

### Model buttons

- **Model:** dropdown list. Shows the name plus size in MB when the file exists. No file means no size.
- **Browse...**: manually point to an `.onnx` file for the currently selected model. The program adds it to the list and to `settings.json`. If the pointed file is the same file as a built-in model, the program does not create a duplicate, it switches to the built-in entry.
- **AI threshold (%):** slider from 15 to 80, default 35. A lower threshold means more detections, but also more false detections. For YuNet the effective minimum threshold is 15.
- **Far (2x2):** splits the frame into 4 tiles and detects in each one separately. Helps with small and far people. Slower, may produce duplicates which the program merges by IoU.

## Extra models: download and install

You can download extra models here:

**https://github.com/marek7400/FaceBlur/tree/main/models**

After downloading, put the `.onnx` file in the program folder. It can be the main program folder or the `models` subfolder. Recommended layout:

```text
FaceBlur/
  FaceBlur2.0.exe
  ffmpeg.exe
  ffprobe.exe
  settings.json
  models/
    yolov11n-seg.onnx
    yolo26s-seg.onnx
    scrfd_500m_bnkps.onnx
```

After you drop the file in, the program loads it and shows it in the model list. You do not need to click Browse....

To make a new file appear in the list:

- restart the program, or
- force a list refresh, for example by switching the language PL/EN or by clicking Browse... (refreshing the list rescans the folder).

Naming rules for new entries:

- the program builds a short name from the file name: YOLO number, size (N, S, M, L, X), target (Face or Person) and kind (Seg or Box),
- example: `yolo26s-seg.onnx` gives entries `YOLO26-S Person Seg` and `YOLO26-S Person Box`,
- example: `yolo11n-face.onnx` gives entry `YOLO11-N Face`,
- a COCO file (for example `yolov8n.onnx`, `yolov11n-seg.onnx`, without the word `face` in the name) never gets a Face entry, because class 0 in COCO is a person, not a face,
- a YOLO26 file never gets a Face entry, because YOLO26 in this program is person only (COCO class 0),
- a file explicitly with face and person in the name (for example `yolo11n-face-person.onnx`) gives a Face and Person pair,
- a face-only file (for example `yolov8n-face.onnx`) gives only Face, even when it has `seg` in the name, because its masks are about faces, not people,
- a file without a recognizable name gets a long name in the old format: `Base, filename.onnx`, so two different `model.onnx` files can be told apart.

A custom entry is saved in `settings.json` in the `custom_models` field. If the file is moved or deleted, the entry is skipped at the next start. Old entries with a wrong Face for a COCO file are cleaned on settings load and rebuilt without Face.

The program looks for models in these folders: `models/` next to the EXE, EXE folder, `models/` in `_MEIPASS`, `_MEIPASS`, `models/` next to the `.py` file, `.py` file folder. It does not scan an arbitrary CWD or the home folder. This is a deliberate security rule: a malicious `.onnx` file dropped into a random folder does not load by itself.

## Video mode and image mode

| Feature | Video | Image |
| --- | --- | --- |
| Input formats | mp4, mov, avi, mkv, wmv | jpg, jpeg, png, bmp, webp |
| Playback, frame jumps | Yes | No |
| Export markers | Yes | No |
| Whole video scan | Yes | No, works like Blur all green |
| Global pause and per-segment pause | Yes | No |
| Export | Video mp4 | Image in a format close to the source |
| Screenshot | Yes, with frame number | Yes, without frame number |

After loading a video the program sets playback speed to 10 fps, regardless of the source FPS. The source FPS is used for time calculation and for export. If the file does not report a frame count, the program counts frames by reading the file from the start. This can take a while for a long video.

After loading an image, the time slider, jump buttons, PLAY, markers and pause are disabled. An image always has one frame, number 0.

Opening a new file while there are trackers or export markers asks for confirmation. Opening a new file deletes unsaved trackers and markers, because the program has no project file.

## Adding blur

### 1. Clicking a green frame

On a paused frame the program draws current detections in green: rectangles for Box models and outlines for Seg models. A left mouse click near a green frame adds a tracker for that detection. If the frame is already a tracker, the program does not duplicate it (IoU threshold 0.72).

The nearest candidate is picked, and on a tie the smaller box wins. The click margin is 15 px around the box.

### 2. Dragging a rectangle (manual mode)

Drag an empty area with the left mouse button. This creates a manual tracker. Minimum size is 8 by 8 px. This is the best way when the AI did not detect a person, or when you want to blur an object that is not a face or a person.

### 3. LINE polygon

Turn on **LINE** at the bottom. Click polygon points, double-click closes the polygon. Minimum 3 points, minimum box 5 by 5 px. The right mouse button while drawing clears the points. Blur is applied to the polygon, not to the whole rectangle.

### 4. Mask brush

Turn on **Mask brush** and paint on an existing mask or on an empty area. Painting on an empty area creates a new Brush tracker. The brush works only for segmentation masks and Brush trackers.

### 5. Blur all green

The **Blur all green** button (orange) adds trackers for all green detections on the current frame. It skips detections already covered by a tracker.

### 6. Blur whole video (AI)

The **Blur whole video (AI)** button (pink) scans the whole video with the selected model and creates trackers for all detected trajectories. Before starting it asks for confirmation and shows the frame count. During scanning it shows a progress window with percent and frame counter, plus a Cancel button. Cancelling stops scanning, but does not undo trackers already created.

In image mode this button works like Blur all green.

Scanning joins detections into trajectories. A face tracker can survive up to 6 frames without a detection, a Seg person tracker up to 20 frames. After the limit, the trajectory ends.

## Tracking: how the program follows a person

A tracker is not just a detection on one frame. It is a segment: from a start frame to an end frame (or open-ended), with a position history (`traj`), a mask history (`traj_poly`), and provenance info for every position.

Position provenance (`traj_prov`):

- `detection`: fresh AI detection,
- `csrt`: CSRT / MIL tracker,
- `interp`: interpolation between two face detections,
- `manual`: manual edit, drag, brush, eraser,
- `hold`: old position, no fresh detection.

### Decision order on the next frame

For people (Person, Seg and Box) the program first tries to match a fresh AI detection to the previous position, taking speed and distance into account. If a detection matches, the tracker gets a new position from detection. If not, for a Box model it tries CSRT, and for a Seg person it does not reach for CSRT when there is a suspicion that the person left the frame. If nothing matches, the tracker goes into HOLD: it keeps the old position, but counts lost frames.

For faces (Face) the program combines CSRT with repeated detection: CSRT follows every frame, and every 8 frames the program tries to fix the position with a fresh detection and recreates the CSRT tracker from the new position. On a big jump (more than 3 frames) the program matches a detection and interpolates the missing frames as `interp`.

### Stop on loss and HOLD

The **Stop on loss** switch decides what happens after a loss:

| Tracker | Stop on loss: on | Stop on loss: off |
| --- | --- | --- |
| Face | ends after more than 3 lost frames | HOLD up to 150 frames |
| Person Seg / Box / Brush | ends after more than 20 lost frames | HOLD up to 150 frames |

HOLD means: blur is still drawn, but in the old place. The face or silhouette may have moved out of the blurred area in the meantime. That is why HOLD is counted separately and shown as a warning in the status bar and at export.

When you turn off Stop on loss, a tracker marked as `failed_auto` comes back to life: the program restores its original end frame, zeroes the lost counter and tries to keep tracking. This also works when switching during preview and export.

### Frame edges and jumping to another person

The program has separate rules for frame edges, because that is where jumping to another person happens most often:

- a weak match at the edge (IoU below 0.30, center far away) is rejected,
- if at the edge there is a weak candidate (another person entering from the same side), the tracker ends at once, instead of holding HOLD and jumping,
- a second consecutive miss at the edge ends the tracker,
- for a jump of more than 10 frames in preview the program steps frame by frame (up to 30 frames), instead of matching in one jump from the old position to the new frame,
- for a one-shot match on a jump of more than 10 frames, IoU of at least 0.25 is required, otherwise it is HOLD or STOP, not a jump.

### PAUSE and RESUME

**PAUSE** (global) cuts all active trackers at the current frame minus 1 and marks them as paused. The preview shows `TRACKING PAUSED - NO BLUR` when there is no active blur on the frame. **RESUME** creates new segments for the same `face_id` values from the current frame, first trying to match a fresh detection to the last good box. Old segments stay in history.

**PAUSE** for one segment works the same way, but only for that row in the list. The button in the list shows **PAUSE** when the segment is active, **Resume** when it is paused, and a dash (disabled) when the segment ended naturally.

Pausing on the first frame gives a segment whose end is earlier than its start. Such a segment is still shown in the list, so it can be resumed.

## Editing on the preview: mouse, keyboard, zoom and pan

### Hover

Hover over a tracker and the program shows handles and highlights the row in the list in yellow. Hover also works for the segmentation outline, not only for the box: the outline is checked first, then the box. If you hover the outline, but not a handle, the cursor is the move cursor by default, and the row is still highlighted.

Cursors:

- box center: move,
- edge: resize horizontally or vertically,
- corner: resize diagonally,
- LINE mode: crosshair,
- Eraser / Brush mode: circle sized like the brush.

### Dragging and resizing

- **Center**: drag to move the tracker.
- **Edge / corner**: drag to resize. Minimum size is 10 px.
- Dragging on the start frame changes the tracker `init_bbox`.
- Dragging on a later frame splits the tracker into two segments with the same `face_id`: the old one ends at the current frame minus 1, the new one starts at the current frame with the new position. Future manual edits are kept in the new segment.
- During dragging the future cache is not cleared on every mouse frame, only on mouse release.

### Deleting

- **Right mouse button** on a tracker deletes that tracker. If zoom is above 1.0, the right button first works as pan, and a click without dragging deletes the tracker.
- **X** in the list deletes one segment.
- **Undo** deletes the last added tracker. If the deleted tracker was a continuation of an earlier segment, the program merges the previous segment back (restores its end frame).
- **Clear** deletes all trackers after confirmation. It asks for confirmation and shows the tracker count.

### Zoom and pan

- **Mouse wheel**: zoom 1.0 to 12.0, step times 1.25, zoom to cursor. Returning close to 1.0 snaps to exactly 1.0.
- **ZOOM 1:1**: resets zoom to 1.0 and centers the frame.
- **Middle mouse button** or **Shift + left button**: pan (drag the view).
- **Right button while zoomed**: pan, as above.

### Keyboard

| Key | Action |
| --- | --- |
| Left / Right arrow | Frame minus 1 / plus 1 |
| Shift + Left / Right arrow | Frame minus 10 / plus 10 |
| Up arrow | Frame plus 30 |
| Down arrow | Frame minus 30 |
| Delete | Undo (delete last tracker) |

Shortcuts work in the main window. Typing a frame number in the field next to the slider and pressing Enter also jumps to that frame.

## Segments and Timeline list

Each row in the list is one segment. A row shows:

- type: **Person** or **Face**,
- `face_id` number,
- kind: `Seg`, `Brush`, first part of the model name for AUTO, or first letter of the type for manual ones,
- frame range: `[start-end]`, where a missing end means an open segment,
- `PAUSED` tag when the segment is paused on the current frame.

Buttons in a row (from the right):

- **X**: delete segment,
- **GO**: jump to the segment start frame (video only),
- **PAUSE / Resume / dash**: pause or resume only this segment.

Row color: greenish means the segment is active on the current frame, gray means inactive. Dark mode uses darker equivalents. Hover on the preview highlights the row in yellow / orange and gently scrolls the list so the row is visible.

## Playback and navigation

- **PLAY / STOP**: plays from the current frame at the slider speed. If you are on the last frame, playback starts from the beginning. Playback uses the wall clock, not the source FPS. Dragging the time slider or changing the frame stops playback.
- **Back / Forward**: jump buttons: minus 30, 20, 10, 5, 2, 1 and plus 1, 2, 5, 10, 20, 30.
- **Time slider**: dragging shows the frame without detection (light preview), releasing reloads the frame with detection.
- **Frame number field**: type a number and press Enter.
- **Marker bar**: thin bar above the slider. Green fields are export ranges, the yellow line is the temporary FROM marker, the red line is the current frame. Clicking the bar jumps to that frame.
- **|<< and >>| buttons**: jump to the start and to the end of the video.
- **Time counter**: exact time of the current frame in `HH:MM:SS.mmm` format.

The status bar shows, among other things, the number of active blurs on the frame and the warnings `NO BLUR: N` and `HOLD (old box): N`.

## Export markers

Markers are for exporting only selected fragments of the video.

1. Go to the start frame and click **Export FROM**.
2. Go to the end frame and click **Export TO**.
3. Repeat for more fragments.

Rules:

- if you click Export TO without a prior FROM, the program takes FROM as frame 0,
- ranges are sorted and merged when they touch or overlap (merge tolerance 1 frame),
- **Delete markers** deletes the range that contains the current frame. The button is active only when the current frame is in some range,
- no ranges means export of the whole video,
- on range export, audio is cut and joined for each range separately.

## Blur look: shape, type and sliders

### Shape

The choice is mutually exclusive. The selected shape is highlighted.

- **CIRCLE (OVAL)**: ellipse inscribed in the box. Default shape.
- **BOX (RECTANGLE)**: whole rectangle.
- **LINE (POLYGON)**: polygon drawn with clicks. For Seg trackers the mask outline still counts, and LINE is a separate way to draw a polygon.

For Seg trackers the CIRCLE / BOX shape does not replace the outline. The mask outline has priority.

### Blur type

The choice is mutually exclusive.

- **PIXEL**: pixelation. The pixel block grows with the Blur slider. Default type.
- **GAUSS**: Gaussian blur. The kernel grows with the Blur slider. For large areas the program first downscales the ROI, blurs, and upscales back, to keep performance.
- **BLACK**: black rectangle / black mask, no blur. Feathering does not work for BLACK.

### Sliders at the bottom

| Slider | Range | Default | What it does |
| --- | --- | --- | --- |
| Blur | 0 to 100 | 50 | Blur strength. For PIXEL: block size (from 2 to 40 px). For GAUSS: kernel size (from 3 to 151 px, always odd) |
| Feathering | 0 to 100 | 15 | Soft mask edge. 0 means a sharp edge. Ignored for BLACK |
| Jump tolerance | 0 to 300 | 130 | How far a face can jump between frames and still be the same face. Used in face matching and in export |
| Playback speed (fps) | 0.5 to 120, step 0.5 | 10.0 | Preview playback speed. Does not change export FPS |

All sliders also support the mouse wheel after clicking the slider (focus), step 1, and for speed step 0.5. The speed value can also be typed in the field next to the slider (Enter or focus loss).

Changing the Blur and Feathering sliders refreshes the current frame at once.

## Segmentation masks: brush, eraser and margin

This section is only for Seg models and Brush trackers. For Box models the brush and eraser have no mask to edit.

- **Mask brush**: paints area into the mask. Dragging paints a line with a thickness set by the Size slider. Painting on a new position expands the tracker box to the new mask. Painting on an empty area creates a new Brush tracker.
- **Mask eraser**: cuts area out of the mask. Dragging cuts a band with a thickness set by the Size slider. A single click (without dragging) deletes only the island you clicked, if the mask has more than one contour. This way one click can remove, for example, a second person pulled into the mask, instead of cutting a hole in the main silhouette.
- **Size**: slider from 8 to 80, default 24. This is the brush / eraser radius in original frame pixels, not in screen pixels. The preview shows a red (eraser) or green (brush) circle of this size.
- **Only main outline**: when on, the program keeps only the largest mask contour. When off, the mask can have many contours (islands). Default on.
- **Mask margin**: slider from minus 4 to plus 4, default 0. A positive margin expands the mask, a negative margin shrinks it. Useful when the outline is too tight and a face edge shows, or too loose and it blurs too much background.
- **Reset mask**: restores the original AI mask on the current frame, that is, undoes manual brush and eraser fixes on this frame. If the mask is already in its original state, the program reports it and changes nothing.

A manual mask edit is remembered as `manual_poly_frames` plus the original AI mask (`ai_orig_poly` / `ai_orig_bbox`). This lets the program carry the fix (the difference between the manual mask and the original) to later frames: what you painted is added to the new AI mask, and what you cut with the eraser is removed from it. If the new box differs from the original by more than 1.8 times in width or height, the program does not carry the fix and uses the clean AI mask.

A mask edit invalidates the tracker future cache from that frame, so later frames do not use the old mask.

## Screenshot

The **Screenshot** button saves the current frame to a JPG or PNG file.

- The **with blur** checkbox (next to the screenshot button) decides whether the screenshot has blur. Default on.
- If zoom is above 1.0, the screenshot is cropped to the visible fragment (visible crop), and the file name starts with `zoom-`.
- File name: `video-name-blurred-000123.jpg` for video (frame number with leading zeroes) or `photo-name-blurred.jpg` for a photo.
- The `-blurred` suffix is added only when the frame really has coverage. No coverage means no suffix, so the name does not lie.
- The source file cannot be overwritten. The program rejects such an attempt and shows an error.
- If the frame has HOLD or NO BLUR trackers, the program shows a warning after saving.

## Video and image export

### Export / Save button

- The **with blur** checkbox (next to the export button) decides whether the export has blur. Default on. If it is off or there are no trackers, the export saves a clean video or photo.
- For video the program asks for an MP4 file name, for a photo it asks for an image file name.
- The source file cannot be overwritten. The program rejects such an attempt.
- Default name for video: `name-blurred-MMSS-MMSS.mp4` for ranges or `name-blurred.mp4` for the whole video. If the export is without blur and without ranges, the name is `name-copy.mp4`.
- Video export runs in a background thread, with a progress window and a Cancel button. Closing the window during export cancels the thread and waits up to 10 seconds for it to finish. If the thread is still alive, the window does not close; try again in a moment.

### How video export works

1. The program reads the video from the start, frame by frame.
2. For every frame in the export range (or for the whole video when there are no ranges) it applies blur for active trackers.
3. The video is written with the `mp4v` codec to a temp file with a unique name (`._fb_tmp_*.mp4`).
4. If `ffmpeg.exe` and `ffprobe` is present, the program muxes the video with audio from the source into a `._fb_ff_*.mp4` file, then replaces the temp file with the target file. Temp files are deleted.
5. If there is no `ffmpeg.exe` and `ffprobe`, the program saves video only, without audio, and adds a warning.

Audio:

- whole video: audio copy, or re-encode to AAC 192k when copying fails,
- one range: audio trim `atrim` from range start to range end, AAC 192k encode,
- many ranges: trim and join audio from each range with the `concat` filter,
- no audio in the source: the program saves video without audio, without an error.

The program checks the frame count in the output file. If it differs from the expected count, it shows a warning about interrupted writing (full disk, codec error) and asks you to verify before publishing.

### Image export

Image export saves the current frame with blur for trackers on frame 0. The output format is picked from the source (JPG, PNG, BMP, WEBP), and for an unknown extension the program uses JPG. HOLD, NO BLUR and UNCOVERED warnings work the same as in video export.

## Settings: settings.json

Settings are saved on program close to `settings.json` in the program folder. Saved values are:

- Blur,
- Feathering,
- Jump tolerance,
- AI threshold,
- Eraser / brush size,
- Only main outline,
- Mask margin,
- Custom models (`custom_models`).

Not saved:

- trackers (segments),
- export markers,
- selected model (the program starts with the default YuNet-Face model, and the list is refreshed),
- shape and blur type (the program starts with CIRCLE and PIXEL),
- playback speed (the program starts with 10 fps),
- current file.

If `settings.json` is damaged or has a wrong field type, the program uses default values and prints an error to the console, instead of crashing.

## Language and theme

- **PL/EN**: switches the whole UI between Polish and English. The start language is detected from the system language (Windows: Polish when the UI language is 0x15; otherwise English, with a fallback to the LANG variable / locale). Switching the language also refreshes the model list, so a new file in `models/` appears after switching language.
- **Dark / light theme** (sun and moon button): switches UI colors for the interface, list, panel and dialogs. It does not change the video itself.
- The **GitHub: marek7400** link opens the author profile in the browser.

## Warnings and the fail-closed rule

The program works in fail-closed mode: an output file never contains a frame that should be blurred, without a warning. Frame states:

- **BLURRED**: blur applied from a fresh detection, CSRT, or manual edit.
- **HOLD**: blur applied in the old place, no fresh detection, tracker lost. The face or silhouette may have moved out of the blurred area.
- **UNBLURRED**: an active tracker has no box on this frame, blur was not applied.
- **UNCOVERED**: the frame has no covering tracker. The file may be without blur.
- **ERASED_INTENTIONAL**: mask deliberately erased with the eraser to an empty list. This is not an error, it is a deliberate removal of blur.

Where warnings appear:

- status bar in preview: `NO BLUR: N` and `HOLD (old box): N`,
- window after video export: number of NO BLUR frames, number of HOLD frames, example frame numbers, AI detection errors, frame count and audio warnings,
- window after image export and after screenshot: the same warnings for one frame,
- before range export: if no range has a covering tracker, the program asks whether to export a clean video at all.

If the program shows HOLD or NO BLUR, do not publish without watching those frames. In practice this means: go to the listed frames, fix them manually or resume the tracker, and export again.

## Known issues and limitations

Read this section before publishing a video with people, especially children. None of these limits is fixed by simply re-exporting the same file.

1. **The AI does not detect all people.** Covered faces, backs, small people in the background, people at the frame edge, fast motion and poor light are the most common misses. A Seg model sees silhouettes better than a Face model, but it also loses them.
2. **A tracker can jump to another person.** Especially when two people pass each other, one leaves the frame and another enters from the same side, or when you jump 20 to 30 frames. The program has anti-jump rules, but they are not perfect. Check frames after people pass each other.
3. **HOLD is not sure blur.** Blur in the old place looks like blur in the preview, but the person may have moved. Treat HOLD as to-check.
4. **Seg without a mask means no blur.** The program deliberately does not fill the box when there is no outline. This is better than a black square, but it means a frame can be without blur. Check the warnings.
5. **No project file.** Trackers and markers are not saved. Closing the program or opening a new file deletes the work. Export right after finishing.
6. **Undo deletes only the last tracker.** There is no full undo stack for mask edits, drags or markers. Undo for a mask edit is Reset mask on the current frame.
7. **No audio without ffmpeg.exe and ffprobe.exe.** The program saves video without audio and shows a warning. Audio needs `ffmpeg.exe` and `ffprobe.exe` next to the program.
8. **Performance.** Seg models, Far (2x2) and 4K video are clearly slower on CPU. Scanning a whole video can take a long time. Lower the AI threshold only when you really need it, because a lower threshold means more detections to process.
9. **Paths with Polish characters.** The program tries to convert the path to a short 8.3 path for OpenCV on Windows. In most cases it works, but paths with unusual characters, long paths and network drives are still the most common source of open and save errors.
10. **Video reading can end early.** A damaged file, a variable frame count or a codec error can make the export save fewer frames than the header declares. The program warns about this after export and shows the number of saved frames.
11. **Preview is not export.** The preview uses a detection cache and light tracking. Export recalculates frames from the start with detection. If preview and export differ, trust the export and its warnings.
12. **Closing during export / scanning.** The program cancels background threads and waits up to 10 seconds. If a thread is still alive, the window does not close. Do not force close during saving, wait for the thread to finish and try again.
13. **The Haar model is weak.** It is only an emergency fallback. Do not use it for publishing when full blur matters.
14. **Export does not overwrite the source.** This is a deliberate safeguard. Pick another file name.
15. **No subtitles, chapters or metadata support.** Export keeps image and audio, but does not copy subtitles or metadata from the source.

## Troubleshooting

**I do not see a model in the list.**

- Check that the `.onnx` file is in the program folder or in `models/`, not on the Desktop.
- Restart the program or switch language PL/EN to refresh the list.
- Check that the file is not a duplicate of another file (same SHA256). A duplicate does not get its own entry.
- Check that the file is not damaged. The program logs `[INSPECT]` to the console when scanning.
- Check that the entry is not hidden as generic. If a precise entry exists for the same file, the generic one is hidden on purpose.

**The model does not detect people.**

- Lower the AI threshold, for example to 20 to 25.
- Turn on Far (2x2) for small people in the background.
- Switch to a Seg model instead of Face when you want to blur whole silhouettes.
- Check that you did not pick a Face model for a COCO file. A COCO file has no face detection.

**Blur is a black square around the person.**

- This is old behavior. In 2.0 (V66) a Seg model never fills the box when there is no outline. If you see a black square, check whether the BLACK type is on. Switch to PIXEL or GAUSS.
- If you have an old file with a mask without an outline, delete the tracker and add it again with a Seg model.

**The tracker jumped to another person.**

- Undo the tracker (Undo or X in the list), go back to the frame before the jump and add the tracker again.
- Do not jump 20 to 30 frames while checking, play or jump by 1 to 10 frames.
- Turn on Stop on loss, so the tracker ends instead of holding HOLD and jumping.

**Export has no audio.**

- Put `ffmpeg.exe` and `ffprobe.exe` next to the program and restart it. At startup it should not show the missing FFmpeg dialog.
- Check whether the source has audio at all. If the source has no audio track, export without audio is correct.
- Check the warning after export. If audio muxing failed, the program saves video without audio and reports it.

**The program does not close.**

- Wait for export or scanning to finish. The program waits up to 10 seconds for background threads.
- If the window still does not close, try again in a moment. Do not force close during saving, or you may get an incomplete file.

**The screenshot has a wrong name or no -blurred suffix.**

- This is deliberate. The suffix is added only when the frame has coverage. No suffix means the screenshot may be without blur. Check the warning after saving.

**Preview is slow.**

- Turn off Far (2x2), switch to a lighter model (YuNet or SCRFD 500M), lower the zoom, close other programs.
- Whole video scanning in the background is still slower on CPU than on GPU.

## Privacy

The program processes files locally on your computer. It does not send videos, photos or frames to a server. AI models are local `.onnx` files. The only network traffic is opening the GitHub link in the browser after a click, and the FFmpeg download links in the startup dialog (when `ffmpeg.exe` is missing).

## Files in the repo

Typical layout worth keeping in the repo:

```text
FaceBlur/
  Face-Blur-V66-...py
  
  models/
    yolov11n-seg.onnx
    yolo26n-seg.onnx
  ffmpeg.exe
  ffprobe.exe
  settings.json
```

The `settings.json` file is created on program close. You do not need to add it manually.

