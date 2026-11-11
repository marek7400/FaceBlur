<img width="1918" height="1154" alt="FacelBlur_v1 37" src="https://github.com/user-attachments/assets/6c2844e6-459d-4ebf-a3c7-731095e93b4c" />


# Face Blur Tool

Narzędzie desktopowe (Python + Tkinter + OpenCV) do ręcznego i półautomatycznego zamazywania twarzy na wideo i zdjęciach.

Nie zamazuje „wszystkiego, co wygląda jak twarz, i już”. Ty decydujesz, która twarz ma być zamazana, od której klatki, do której klatki, jakim kształtem i jak mocno — a program pomaga Ci to śledzić z klatki na klatkę.

> **Zasada działania w jednym zdaniu:** detektor AI znajduje kandydatów (zielone ramki), Ty zatwierdzasz twarz do zamazania (klik / przeciągnięcie / przycisk), a tracker CSRT prowadzi zamazany obszar przez kolejne klatki, dopóki go nie przerwiesz, nie usuniesz albo tracker go nie zgubi.

---

## Spis treści

- [Co potrafi program](#co-potrafi-program)
- [Wymagania i instalacja](#wymagania-i-instalacja)
- [Pliki modeli AI](#pliki-modeli-ai)
- [Szybki start](#szybki-start)
- [Jak wygląda interfejs](#jak-wygląda-interfejs)
- [Legenda kolorów ramek](#legenda-kolorów-ramek)
- [Opis wszystkich przycisków i pól — pasek górny, wiersz 1](#opis-wszystkich-przycisków-i-pól--pasek-górny-wiersz-1)
- [Opis wszystkich przycisków — pasek górny, wiersz 2](#opis-wszystkich-przycisków--pasek-górny-wiersz-2)
- [Panel boczny po prawej — lista twarzy](#panel-boczny-po-prawej--lista-twarzy)
- [Sterowanie odtwarzaniem i skakaniem po klatkach](#sterowanie-odtwarzaniem-i-skaniem-po-klatkach)
- [Oś czasu i znaczniki fragmentów do eksportu](#oś-czasu-i-znaczniki-fragmentów-do-eksportu)
- [Suwaki i przełączniki parametrów zamazania](#suwaki-i-przełączniki-parametrów-zamazania)
- [Mysz, zoom i rysowanie](#mysz-zoom-i-rysowanie)
- [Ręczne kasowanie zamazania](#ręczne-kasowanie-zamazania)
- [Ręczna edycja ramki — zmiana położenia i rozmiaru chwytając myszką](#ręczna-edycja-ramki--zmiana-położenia-i-rozmiaru-chwytając-myszką)
- [Skróty klawiaturowe](#skróty-klawiaturowe)
- [Jak działa śledzenie](#jak-działa-śledzenie)
- [Eksport wideo i zdjęć](#eksport-wideo-i-zdjęć)
- [Zrzut ekranu](#zrzut-ekranu)
- [⚠️ PROBLEM 1: Niepotrzebne zamazane obszary w losowych miejscach](#-problem-1-niepotrzebne-zamazane-obszary-w-losowych-miejscach)
- [⚠️ PROBLEM 2: Twarz nadal śledzona, ale już odwrócona — czy dalej zamazywać?](#-problem-2-twarz-nadal-śledzona-ale-już-odwrócona--czy-dalej-zamazywać)
- [Dobre praktyki — kolejność pracy, która oszczędza poprawki](#dobre-praktyki--kolejność-pracy-która-oszczędza-poprawki)
- [Rozwiązywanie problemów](#rozwiązywanie-problemów)
- [Plik `settings.json`](#plik-settingsjson)
- [Ograniczenia](#ograniczenia)

---

## Co potrafi program

- Wczytywanie wideo: `.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`.
- Wczytywanie zdjęć: `.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`.
- Wykrywanie twarzy 4 modelami AI (do wyboru) + awaryjnie Haar Cascade.
- Trzy sposoby dodania zamazania: **AUTO** (klik na wykrytą twarz), **MANUAL** (ręczne narysowanie prostokąta), **LINE** (ręczne narysowanie wielokąta).
- Śledzenie zamazanej twarzy przez film trackerem CSRT z ponownym zakotwiczeniem przez AI.
- Przerwanie / wznowienie śledzenia w dokładnie wybranej klatce.
- Ręczne przesuwanie i zmiana rozmiaru zamazanego obszaru na dowolnej klatce (z podziałem segmentu).
- Usuwanie pojedynczego zamazania: z listy, prawym przyciskiem myszy albo przyciskiem **Cofnij**.
- Kształty zamazania: owal (CIRCLE), prostokąt, wielokąt (LINE), pikselizacja (PIXEL), czarny prostokąt/owal (BLACKBOX) albo klasyczny blur Gaussa.
- Regulacja siły rozmycia, wtapiania krawędzi i tolerancji skoku trackera.
- Eksport całego filmu albo tylko wybranych fragmentów (znaczniki **Eksport OD / DO**), z zachowaniem dźwięku przez FFmpeg.
- Eksport zdjęcia z zamazanymi twarzami.
- Zrzut bieżącej klatki — z zamazaniem albo bez (czysta klatka źródłowa).
- Podgląd na żywo z zoomem do 12× i przesuwaniem widoku (Pan).
- Motyw jasny / ciemny.
- Zapamiętywanie ostatnich ustawień suwaków w pliku `settings.json`.

---

## Wymagania i instalacja

### Jeśli masz gotowy `.exe`

1. Włóż do jednego folderu: plik programu (`.exe`), plik `ffmpeg.exe` oraz pliki modeli `.onnx` (patrz niżej).
2. Uruchom `.exe`.
3. Jeśli brakuje `ffmpeg.exe`, program pokaże okno z linkami do pobrania. Bez FFmpeg eksport wideo nadal działa, ale **bez dźwięku**.

### Jeśli uruchamiasz z kodu źródłowego

```bash
pip install opencv-python numpy Pillow
python face_blur_v37_multi_model.py
```

Wymagane: Python 3.x, Windows (program wymusza obsługę wysokiego DPI w Windows, ale logika działa też na innych systemach, gdzie obsługa DPI jest po prostu pomijana).

### FFmpeg — po co i skąd

FFmpeg jest potrzebny **tylko** do doklejenia oryginalnej ścieżki dźwiękowej do wyeksportowanego wideo.

- Program najpierw szuka `ffmpeg.exe` obok siebie, potem w `PATH` systemowym.
- Jeśli go nie ma przy starcie, pokazuje ostrzeżenie z linkami:
  - https://github.com/BtbN/FFmpeg-Builds/releases
  - https://www.gyan.dev/ffmpeg/builds/
- Wypakuj z pobranej paczki sam plik `ffmpeg.exe` do folderu programu.

---

## Pliki modeli AI

Program szuka plików modelu w folderze programu, folderze roboczym i katalogu `~/.face_detector`. Możesz też wskazać plik ręcznie przyciskiem **Wskaż...**.

| Model w liście | Szukane pliki (pierwszy znaleziony wygrywa) | Charakter |
| --- | --- | --- |
| **YuNet** (domyślny) | `face_detection_yunet_2026may.onnx`, `face_detection_yunet_2023mar.onnx`, `yunet.onnx` | Szybki, dobry punkt startowy do większości nagrań. |
| **SCRFD 500M** | `scrfd_500m_bnkps.onnx`, `scrfd_500m.onnx`, `500m.onnx`, `det_500m.onnx` | Lekki, często lepszy na małych / dalekich twarzach. |
| **SCRFD 2.5G** | `scrfd_2.5g_bnkps.onnx`, `scrfd_2.5g.onnx`, `2.5g_bnkps.onnx`, `det_2.5g.onnx` | Dokładniejszy, wolniejszy. |
| **YOLO-Face (Nano)** | `yolov8n-face.onnx`, `yolov8n_face.onnx`, `yolo-face.onnx`, `model.onnx` | Alternatywa, gdy inne modele coś pomijają albo coś fałszywie wykrywają. |
| **Haar Cascade** (awaryjny) | `haarcascade_frontalface_default.xml` | Użyty automatycznie, gdy nie ma żadnego pliku `.onnx` albo model się nie wczyta. Najsłabszy — głównie twarze na wprost. |

> Zmiana modelu w trakcie pracy odświeża detekcję na bieżącej klatce. Już dodane zamazania (trackery) nie znikają — zmienia się tylko to, jakie zielone ramki-kandydaci zobaczysz.

---

## Szybki start

### Wideo — najczęstszy scenariusz

1. **Wybierz wideo**.
2. Zatrzymaj się na klatce, gdzie widać twarz do zamazania.
3. Kliknij w zieloną ramkę tej twarzy (AUTO) albo przeciągnij prostokąt myszą (MANUAL).
4. Odtwórz film i obserwuj, czy zamazanie trzyma się twarzy.
5. Gdy twarz znika / odwraca się na stałe / wychodzi z kadru — przerwij śledzenie (patrz: Problem 2).
6. Powtórz dla kolejnych osób.
7. Ustaw **Rozmycie** i **Wtapianie** tak, żeby nie było widać rysów, ale krawędź nie wyglądała jak wycięta nożyczkami.
8. **Eksportuj / Zapisz**.

### Zdjęcie

1. **Wybierz zdjęcie**.
2. Kliknij zieloną ramkę albo narysuj prostokąt / wielokąt.
3. Popraw pozycję i rozmiar, przeciągając środek / krawędzie ramki.
4. **Eksportuj / Zapisz** — zapisze zdjęcie z zamazaniem.

### Szybka ścieżka automatyczna (do wstępnej kontroli, nie do ślepego eksportu)

1. **Zamaż cały film (AI)** — program przeskanuje cały film i doda zamazania do wszystkich wykrytych trajektorii.
2. **Obowiązkowo** przejrzyj listę po prawej i cały film klatka po klatce w podejrzanych miejscach.
3. Usuń fałszywe zamazania (patrz: Problem 1), skróć segmenty odwróconych twarzy (patrz: Problem 2).
4. Dopiero wtedy eksportuj.

---

## Jak wygląda interfejs

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ WIERSZ 1: Wybierz wideo | Wybierz zdjęcie | GitHub | Model: [YuNet]       │
│ [Wskaż...] [Zamaż wszystkie zielone...] [Zamaż cały film (AI)] [🌙/☀️]    │
│ [📸 Zrób zrzut ekranu] [z blur] [💾 Eksportuj / Zapisz]                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ WIERSZ 2: [PAUSE] [RESUME] ● AKTYWNE [Stop gdy zgubi] [ZOOM 1:1]          │
│ [Cofnij] [Wyczyść]                                                         │
├──────────────────────────────────────────────────────────┬───────────────┤
│                                                          │ DETEKTOR AI:  │
│                                                          │ YuNet         │
│                  PODGLĄD WIDEO                             │ Informacje    │
│                  (klik / przeciągnięcie /                │ Segmenty i   │
│                   scroll = zoom)                         │ Oś Czasu:   │
│                                                          │ [lista twarzy]│
│                                                          │ [IDŹ] [X]     │
├──────────────────────────────────────────────────────────┴───────────────┤
│ Nazwa wczytanego pliku                                                   │
├──────────────────────────────────────────────────────────────────────────────┤
│ 00:00:00.000 [▶ PLAY] W tył: [-30][-20][-10][-5][-2][-1]                  │
│ W przód: [+1][+2][+5][+10][+20][+30]                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ [|<<] ─────── OŚ CZASU (suwak + pasek znaczników) ─────── [>>|] [klatka] │
│ / total (znaczniki:) [Eksport OD] [Eksport DO] [Kasuj znaczniki]          │
├──────────────────────────────────────────────────────────────────────────────┤
│ Rozmycie | Wtapianie | Tolerancja skoku | Prędkość odtwarzania (kl./s)    │
│ [CIRCLE] [BLACKBOX] [LINE] [PIXEL]                                         │
├──────────────────────────────────────────────────────────────────────────────┤
│ Pasek statusu: Klatka: ... | Zoom: ... | Aktywne twarze: ...              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Legenda kolorów ramek

To jest najważniejsza rzecz do zrozumienia, zanim zaczniesz klikać:

| Co widzisz | Co to znaczy | Czy będzie zamazane w eksporcie? |
| --- | --- | --- |
| **Zielona ramka** | Kandydat wykryty przez AI na bieżącej klatce. To tylko propozycja. | **Nie** — dopóki jej nie zatwierdzisz. |
| **Turkusowa ramka / owal + uchwyty w narożnikach i środek z krzyżykiem** | Aktywny tracker — zamazanie, które już dodałeś i które program śledzi. Pojawia się po najechaniu myszą na zamazany obszar. | **Tak** — o ile klatka mieści się w przedziale `[start_frame – end_frame]` tego trackera. |
| **Turkusowa linia wielokąta** | Aktywny tracker w trybie LINE (wielokąt). | Tak, tylko wewnątrz wielokąta. |
| **Brak ramki, ale obszar jest zamazany** | Tracker działa, ale mysz nie jest nad nim, więc program nie rysuje obramowania. Zamazanie nadal działa. | Tak. |
| **Napis na podglądzie: `PAUZA ŚLEDZENIA – BRAK ROZMYCIA`** | Śledzenie jest wstrzymane i na tej klatce nie ma żadnego aktywnego zamazania. | Nie. |

> **Wniosek:** zielone ramki to nie zamazanie. Zamazanie to dopiero to, co widzisz jako rozmyty / czarny / pikselowany obszar oraz to, co jest na liście po prawej.

---

## Opis wszystkich przycisków i pól — pasek górny, wiersz 1

### `Wybierz wideo`

- **Co robi:** otwiera okno wyboru pliku wideo (`.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`).
- **Po wczytaniu:** resetuje wszystkie trackery, znaczniki eksportu, zoom i liczniki; ustawia prędkość odtwarzania na podstawie FPS filmu; odblokowuje przyciski odtwarzania, osi czasu i eksportu.
- **Uwaga:** wczytanie nowego pliku kasuje dotychczasową pracę nad poprzednim filmem. Najpierw wyeksportuj poprzedni.

### `Wybierz zdjęcie`

- **Co robi:** otwiera okno wyboru zdjęcia (`.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`).
- **Po wczytaniu:** przełącza program w tryb zdjęcia: oś czasu, odtwarzanie, przyciski skakania, prędkość i znaczniki eksportu są wyłączone, bo jest tylko 1 klatka.
- **Eksport zdjęcia:** przyciskiem **Eksportuj / Zapisz**.

### `GitHub: marek7400`

- **Co robi:** kliknięcie otwiera w przeglądarce profil GitHub autora: https://github.com/marek7400
- Nie zmienia niczego w programie.

### `Model:` — lista rozwijana

- **Co robi:** wybór detektora AI: `YuNet`, `SCRFD 500M`, `SCRFD 2.5G`, `YOLO-Face (Nano)`.
- **Kiedy zmieniać:** gdy aktualny model gubi twarz, wykrywa coś, co twarzą nie jest, albo źle radzi sobie z małymi / obróconymi / częściowo zasłoniętymi twarzami.
- **Efekt:** program przeładowuje model i ponownie wykrywa twarze na bieżącej klatce. Istniejące zamazania zostają.
- Panel po prawej (`DETEKTOR AI:`) zawsze pokazuje, który model i który plik `.onnx` jest faktycznie aktywny.

### `Wskaż...`

- **Co robi:** pozwala ręcznie wskazać plik `.onnx` dla modelu wybranego na liście `Model:`.
- **Kiedy używać:** gdy plik modelu ma inną nazwę niż te, których program szuka automatycznie, albo leży w innym folderze.
- Jeśli pliku nie ma, program zapyta, czy chcesz go wskazać ręcznie. Odmowa = fallback do Haar Cascade.

### `Zamaż wszystkie zielone na bieżącej klatce`

- **Co robi:** zamienia **wszystkie** zielone ramki widoczne na bieżącej klatce w aktywne trackery (zamazania).
- **Kiedy używać:** gdy na klatce jest kilka osób i chcesz zamazać je wszystkie naraz, zamiast klikać każdą osobno.
- **Uwaga:** jeśli detektor pomylił się i zaznaczył na zielono coś, co twarzą nie jest (np. plakat, wzór na koszulce, dłoń), ten przycisk zamazuje też ten błąd. Po kliknięciu sprawdź listę po prawej i usuń fałszywe wpisy (patrz: Problem 1).
- Jeśli na klatce nie ma zielonych ramek, program pokaże ostrzeżenie.

### `⚡ Zamaż cały film (AI)`

- **Co robi:** skanuje **cały film klatka po klatce**, wykrywa twarze modelem AI i automatycznie tworzy zamazania dla każdej wykrytej trajektorii twarzy.
- **Przebieg:** program pyta o potwierdzenie → pokazuje okno postępu z procentami i licznikiem klatek → możesz przerwać przyciskiem **Anuluj**.
- **Zasady tworzenia trajektorii w skanowaniu:**
  - Nowa twarz na klatce, która pasuje do już śledzonej (blisko + nakładanie się), dopisuje się do tej samej trajektorii.
  - Twarz, której nie widać przez **6 kolejnych klatek**, kończy trajektorię.
  - Do listy trafiają tylko trajektorie o długości **co najmniej 3 klatek**. Jednoklatkowe „mignięcia” są odrzucane.
- **Czego NIE robi:** nie ocenia, czy twarz jest odwrócona tyłem, czy to na pewno twarz, ani czy ta osoba w ogóle powinna być zamazana. To Twoja decyzja po skanowaniu.
- **Po skanowaniu obowiązkowo:** przejrzyj listę po prawej i film — usuń fałszywe zamazania i skróć segmenty, gdzie twarz jest już odwrócona (patrz: Problem 1 i Problem 2).
- W trybie zdjęcia przycisk działa jak „zamaż wszystkie zielone na bieżącej klatce”.

### `🌙 / ☀️` — motyw

- **Co robi:** przełącza motyw jasny / ciemny całego interfejsu.
- Nie wpływa na zamazanie, eksport ani wideo. Tylko wygląd.

### `📸 Zrób zrzut ekranu`

- **Co robi:** zapisuje bieżącą klatkę do pliku `.jpg` / `.png` (wybierasz lokalizację w oknie zapisu).
- Nazwa domyślna: `zrzut_klatka_000123_blur.jpg` albo `..._czysta.jpg`, zależnie od checkboxa **z blur**.
- Szczegóły: patrz sekcja [Zrzut ekranu](#zrzut-ekranu).

### `z blur` — checkbox obok zrzutu ekranu

- **Zaznaczony (domyślnie):** zrzut zawiera zamazania z bieżącej klatki (tak jak na podglądzie).
- **Odznaczony:** zrzut to czysta klatka źródłowa, bez zamazań — mimo że na podglądzie zamazania widać.
- Przydatne, gdy chcesz porównać „przed / po” albo zachować dowód, jak wyglądała klatka przed edycją.

### `💾 Eksportuj / Zapisz`

- **Co robi:** główny przycisk zapisu.
  - W trybie wideo: otwiera okno wyboru nazwy pliku `.mp4` i eksportuje wideo.
  - W trybie zdjęcia: zapisuje zdjęcie z zamazaniem.
- **Blokada:** jeśli nie ma wczytanego pliku albo nie ma żadnego trackera (żadnego zamazania), program pokaże ostrzeżenie i nic nie wyeksportuje.
- **Ważne:** eksportuje **tylko** to, co jest na liście trackerów i w ich przedziałach klatek. Zielone ramki same w sobie nie są eksportowane.
- Szczegóły eksportu: patrz sekcja [Eksport wideo i zdjęć](#eksport-wideo-i-zdjęć).

---

## Opis wszystkich przycisków — pasek górny, wiersz 2

### `⏸ PAUSE` — wstrzymaj śledzenie

- **Co robi:** kończy wszystkie aktywne trackery na klatce **poprzedzającej bieżącą**: ustawia im `end_frame = bieżąca_klatka − 1` i zatrzymuje ich śledzenie.
- **Kiedy używać:** to jest Twój główny przycisk do **kończenia zamazania w wybranym momencie** — np. gdy twarz się odwraca, wychodzi z kadru, znika za kimś albo po prostu od tej klatki nie chcesz jej już zamazywać.
- **Efekt wizualny:** wskaźnik zmienia się na `⏸ PAUZA ŚLEDZENIA`, a jeśli na tej klatce nie ma już żadnego aktywnego zamazania, na podglądzie pojawia się napis `PAUZA ŚLEDZENIA – BRAK ROZMYCIA`.
- **Nieaktywny, gdy:** tryb zdjęcia albo brak trackerów.
- **Uwaga:** PAUSE nie usuwa zamazań z wcześniejszych klatek. One zostają w eksporcie. PAUSE tnie tylko „odtąd dalej”.

### `▶ RESUME` — wznów śledzenie

- **Co robi:** wznawia śledzenie dla twarzy, których trackery zostały zakończone (np. przyciskiem PAUSE). Dla każdej takiej twarzy tworzy nowy segment śledzenia od bieżącej klatki, zaczynając od ostatniego znanego położenia.
- **Kiedy używać:** gdy wstrzymałeś śledzenie na chwilę (np. twarz odwróciła się na kilka klatek i wróciła przodem) i chcesz, żeby ta sama twarz była znowu śledzona.
- **Pułapka — przeczytaj uważnie:**
  - Jeśli wstrzymałeś śledzenie, bo twarz **na stałe** się odwróciła i nie chcesz jej już zamazywać, **NIE klikaj RESUME**. RESUME przywróci zamazywanie tej twarzy.
  - RESUME wznawia **wszystkie** zakończone trackery, nie tylko jedną wybraną twarz.
- **Nieaktywny, gdy:** tryb zdjęcia albo brak trackerów.

### Wskaźnik `● AKTYWNE` / `⏸ PAUZA ŚLEDZENIA`

- Tylko informacja: zielony = śledzenie aktywne, czerwony = śledzenie wstrzymane przyciskiem PAUSE.
- Sam w sobie nie jest klikalny.

### `Stop gdy zgubi` — checkbox (domyślnie zaznaczony)

- **Zaznaczony (zalecany):** gdy tracker straci twarz (np. gwałtowny skok, wyjście z kadru, zasłonięcie), program po 2–3 nieudanych klatkach oznacza tracker jako `failed`, kończy jego segment i **przestaje go zamazywać** w kolejnych klatkach.
- **Odznaczony:** tracker „trzyma się” ostatniego znanego położenia — zamazanie zostaje w miejscu, gdzie twarz była ostatnio widziana, nawet jeśli twarzy już tam nie ma.
- **Kiedy odznaczyć:** tylko świadomie, na krótki fragment, gdy twarz na chwilę znika za przeszkodą i zaraz wraca w to samo miejsce, a nie chcesz, żeby tracker się poddał. W większości przypadków zostaw zaznaczone — to główna ochrona przed zamazaniami „wiszącymi w powietrzu” (patrz: Problem 1).
- Wpływa zarówno na podgląd, jak i na eksport.

### `🔍 ZOOM 1:1`

- **Co robi:** resetuje zoom do 100% i centruje widok na środku klatki.
- Nie zmienia zamazań ani klatki. Tylko widok.

### `Cofnij`

- **Co robi:** usuwa **ostatnio dodany** tracker (ostatni wpis na liście).
- Jeśli usunięty tracker był kontynuacją tej samej twarzy (ten sam `face_id`), poprzedni segment tej twarzy zostaje „odblokowany” (jego `end_frame` wraca na `None`), czyli twarz znowu jest śledzona bez przerwy.
- Skrót klawiatury: `Delete`.
- Różnica względem **Wyczyść**: Cofnij usuwa tylko jeden, ostatni wpis.

### `Wyczyść`

- **Co robi:** usuwa **wszystkie** trackery i wszystkie narysowane punkty wielokąta. Resetuje liczniki twarzy.
- **Nieodwracalne** (nie ma „przywróć”). Używaj, gdy chcesz zacząć zamazywanie od zera albo gdy po skanowaniu AI lista jest tak zaśmiecona, że szybciej jest dodać wszystko ręcznie od nowa.
- Nie usuwa wczytanego pliku ani znaczników eksportu.

---

## Panel boczny po prawej — lista twarzy

### `DETEKTOR AI:` — informacja o modelu

Pokazuje aktywny model i nazwę pliku modelu, np.:

```
DETEKTOR AI:
YuNet
(face_detection_yunet_2023mar.onnx)
```

Jeśli widzisz `brak pliku` albo `Haar Cascade`, znaczy to, że plik `.onnx` nie został znaleziony i program używa awaryjnego detektora.

### Pole informacyjne

Pokazuje podstawowe dane o wczytanym pliku: nazwę, rozmiar klatki, liczbę klatek, FPS (wideo) albo rozmiar w pikselach (zdjęcie).

### `Segmenty i Oś Czasu:` — lista trackerów

Każdy wiersz to **jeden segment zamazania jednej twarzy**:

```
Twarz 2 [ID5] (A) [120-340]
```

- `Twarz 2` — numer twarzy (`face_id`). Ta sama osoba w różnych momentach filmu może mieć różne segmenty, ale ten sam `face_id`, jeśli program uznał, że to kontynuacja.
- `[ID5]` — unikalny numer segmentu (`id`). Dwa segmenty tej samej twarzy mają różne `ID`, ale ten sam numer `Twarz`.
- `(A)` / `(M)` / `(L)` — sposób dodania:
  - `A` = AUTO (klik na zieloną ramkę / przycisk „Zamaż wszystkie zielone...” / skanowanie AI),
  - `M` = MANUAL (ręczne przeciągnięcie prostokąta),
  - `L` = LINE (ręczne narysowanie wielokąta).
- `[120-340]` — przedział klatek, w którym ten segment zamazuje. `...` zamiast liczby końcowej oznacza, że segment jest otwarty (śledzenie trwa, nie ma ustawionego końca).

Kolor wiersza: zielonkawy = segment aktywny na bieżącej klatce, szary = segment nieaktywny na bieżącej klatce (np. zaczyna się później albo już się skończył).

### Przyciski w wierszu listy

| Przycisk | Co robi |
| --- | --- |
| `IDŹ` | Skacze do klatki początkowej (`start_frame`) tego segmentu. Najszybszy sposób, żeby sprawdzić, od kiedy dany tracker zamazuje i czy to na pewno ta twarz. |
| `X` | Usuwa **tylko ten segment**. Inne segmenty tej samej twarzy zostają. Jeśli to był ostatni segment twarzy i miał ustawiony koniec, poprzedni segment zostaje odblokowany. |

> **Jak używać listy do kontroli:** po skanowaniu AI albo po ręcznej pracy klikaj po kolei `IDŹ` przy każdym wpisie i patrz, czy zamazanie faktycznie leży na twarzy. To jest znacznie szybsze niż przewijanie całego filmu na ślepo.

---

## Sterowanie odtwarzaniem i skakaniem po klatkach

### Licznik czasu `00:00:00.000`

Dokładny czas bieżącej klatki w formacie `godziny:minuty:sekundy.milisekundy`. Obliczany jako `numer_klatki / FPS`.

### `▶ PLAY` / `⏹ STOP`

- **PLAY:** odtwarza film od bieżącej klatki z prędkością ustawioną suwakiem **Prędkość odtwarzania**.
- **STOP:** zatrzymuje odtwarzanie i odświeża klatkę z detekcją.
- Podczas odtwarzania program **nie** uruchamia detekcji AI na każdej klatce — pokazuje tylko już zdefiniowane zamazania. Dzięki temu odtwarzanie jest płynne. Detekcja wraca po zatrzymaniu.
- Jeśli jesteś na ostatniej klatce i naciśniesz PLAY, odtwarzanie zaczyna się od początku.
- W trybie zdjęcia przycisk jest nieaktywny.

### `W tył:` — przyciski `-30`, `-20`, `-10`, `-5`, `-2`, `-1`

Skok wstecz o podaną liczbę klatek. Np. `-30` przy 25 FPS to ok. 1,2 s wstecz.

### `W przód:` — przyciski `+1`, `+2`, `+5`, `+10`, `+20`, `+30`

Skok w przód o podaną liczbę klatek. `+1` / `-1` to przeglądanie klatka po klatce — najlepszy sposób na dokładne znalezienie momentu odwrócenia twarzy albo pojawienia się fałszywego zamazania.

### `|<<` i `>>|`

Skok na pierwszą / ostatnią klatkę filmu.

---

## Oś czasu i znaczniki fragmentów do eksportu

### Pasek znaczników (cienki pasek nad suwakiem)

- Zielone prostokąty = zaznaczone fragmenty do eksportu.
- Żółta linia = tymczasowy znacznik **OD** (czeka na kliknięcie **DO**).
- Czerwona linia = bieżąca klatka.
- Kliknięcie w pasek znaczników przenosi Cię do klikniętej klatki (i zatrzymuje odtwarzanie).

### Suwak osi czasu

- Przeciąganie suwaka = przewijanie filmu. Po puszczeniu suwaka program odświeża klatkę z detekcją.
- Suwak jest zsynchronizowany z bieżącą klatką i polem numeru klatki.
- W trybie zdjęcia suwak jest wyłączony.

### Pole numeru klatki + `/ total`

- Wpisz numer klatki i naciśnij `Enter`, żeby do niej skoczyć.
- `/ total` pokazuje numer ostatniej klatki (np. `/ 1250`). W trybie zdjęcia: `/ 0 (Zdjęcie)`.

### `(znaczniki:)` — `Eksport OD` / `Eksport DO` / `Kasuj znaczniki`

To **nie** są znaczniki zamazań. To znaczniki **fragmentów filmu do wyeksportowania**.

- `Eksport OD`: zapamiętuje bieżącą klatkę jako początek fragmentu. Przycisk zmienia kolor na pomarańczowy — to znak, że czeka na koniec.
- `Eksport DO`: zamyka fragment: `początek = mniejsza z klatek OD/DO`, `koniec = większa`. Fragment dopisuje się do listy. Możesz zaznaczyć wiele fragmentów — nakładające się albo stykające się fragmenty program automatycznie scala w jeden.
- `Kasuj znaczniki`: usuwa fragment, w którym aktualnie się znajdujesz. Przycisk jest aktywny (czerwony) tylko wtedy, gdy bieżąca klatka leży wewnątrz jakiegoś zaznaczonego fragmentu.
- **Jeśli nie zaznaczysz żadnego fragmentu**, eksport obejmie cały film.
- **Jeśli zaznaczysz fragmenty**, eksport obejmie **tylko** te fragmenty, połączone w kolejności, jeden po drugim (wideo i dźwięk są cięte i łączone w tej samej kolejności).

> Uwaga: znaczniki eksportu nie zastępują usuwania fałszywych trackerów. Jeśli w zaznaczonym fragmencie jest fałszywe zamazanie, to ono też się wyeksportuje. Najpierw wyczyść trackery, potem tnij fragmenty.

---

## Suwaki i przełączniki parametrów zamazania

Wszystkie zmiany widać od razu na podglądzie (po odświeżeniu klatki). Przy eksporcie program używa wartości suwaków z momentu kliknięcia **Eksportuj / Zapisz**.

### `Rozmycie` — suwak 0–100 (domyślnie 50)

- Siła rozmycia / pikselizacji.
- **Tryb Gauss:** im wyżej, tym większe jądro rozmycia Gaussa (od ok. 3 px do ok. 151 px). Przy 0 zamazanie jest prawie niewidoczne — nie używaj niskich wartości do anonimizacji.
- **Tryb PIXEL:** im wyżej, tym większe bloki pikseli (od ok. 2 px do ok. 40 px).
- **Jak ustawić:** podnieś tak wysoko, żeby nie dało się odczytać rysów twarzy, oczu, ust ani charakterystycznych pieprzyków / tatuaży. Sprawdź na zbliżeniu (zoom) i na zatrzymanej klatce, nie tylko w ruchu.

### `Wtapianie` — suwak 0–100 (domyślnie 15)

- Miękkość krawędzi zamazania (feathering).
- `0` = ostra, widoczna krawędź „wycięcia”.
- `15–30` = zwykle naturalne przejście, które nie rzuca się w oczy.
- `100` = bardzo rozmyta, szeroka krawędź — zamazanie „rozlewa się” poza twarz.
- **Nie działa** w trybie BLACKBOX (czarne wypełnienie jest wstawiane bez wtapiania).

### `Tolerancja skoku` — suwak 0–300 (domyślnie 130)

- Najważniejszy suwak do walki z „uciekającym” trackerem.
- Określa, o ile pikseli środek zamazanego obszaru może się przesunąć między klatkami, zanim program uzna to za błąd / zgubienie twarzy.
- **Za wysoka (np. 250–300):** tracker łatwiej „przeskoczy” na inną twarz, dłoń, plakat albo wzór w tle i zacznie zamazywać coś innego. To jedna z głównych przyczyn losowych zamazań.
- **Za niska (np. 0–30):** tracker szybciej się podda przy szybkim ruchu głowy, gwałtownym ruchu kamery albo niskim FPS. Wtedy zamazanie zniknie w połowie ruchu.
- **Jak ustawić:** zacznij od 130. Jeśli tracker przeskakuje na inne obiekty — zmniejsz (np. 70–100). Jeśli tracker gubi szybko poruszającą się twarz — zwiększ (np. 160–200). Zawsze po zmianie przewiń podejrzany fragment i sprawdź.
- Przy dużych skokach na osi czasu (skok o więcej niż 3 klatki) program dodatkowo używa AI do ponownego zakotwiczenia: szuka najbliższej wykrytej twarzy w pobliżu ostatniego położenia i tylko wtedy, gdy jest w zasięgu tolerancji i ma podobny rozmiar (stosunek boków poniżej 2,5×), uznaje ją za tę samą twarz.

### `Prędkość odtwarzania (kl./s)` — suwak 0,5–120 + pole tekstowe

- Prędkość podglądu, **nie** prędkość eksportowanego filmu. Eksport zawsze ma oryginalny FPS.
- Domyślnie ustawiana na FPS wczytanego filmu.
- Możesz wpisać wartość ręcznie w polu obok suwaka (np. `12.5`) i zatwierdzić `Enter` albo kliknięciem poza pole. Wartość jest zaokrąglana do 0,5 i ograniczana do zakresu 0,5–120.
- Niskie wartości (np. 5–10 kl./s) są idealne do dokładnego sprawdzenia, czy zamazanie trzyma się twarzy. Wysokie (np. 50–120) do szybkiego przeglądu całego filmu.

### `CIRCLE` — checkbox (domyślnie zaznaczony)

- **Zaznaczony:** zamazanie ma kształt owalu / elipsy wpisanej w ramkę trackera. To domyślny, najbardziej naturalny kształt dla twarzy.
- **Odznaczony:** zamazanie ma kształt prostokąta (cała ramka).
- Działa też dla trybu BLACKBOX (czarny owal vs czarny prostokąt) i dla wielokąta? Nie — jeśli tracker jest wielokątem LINE, kształt wielokąta ma pierwszeństwo nad CIRCLE.

### `BLACKBOX` — checkbox (domyślnie odznaczony)

- **Zaznaczony:** zamiast rozmycia wstawia jednolite czarne wypełnienie w kształcie owalu / prostokąta / wielokąta.
- **Odznaczony:** normalne rozmycie albo pikselizacja.
- Najmocniejsza anonimizacja (nic nie prześwituje), ale najbardziej widoczna w kadrze.
- Wtapianie nie działa w tym trybie — krawędź jest ostra.

### `LINE` — checkbox (domyślnie odznaczony)

- **Zaznaczony:** włącza tryb rysowania wielokąta.
  - Klikaj kolejne punkty wielokąta lewym przyciskiem myszy.
  - Podwójne kliknięcie zamyka kształt i tworzy tracker-wielokąt.
  - Prawy przycisk myszy kasuje rozpoczęty wielokąt (punkty znikają).
  - Włączenie / wyłączenie LINE kasuje rozpoczęty, niedokończony wielokąt.
- **Odznaczony:** normalne tryby AUTO / MANUAL (prostokąt).
- Po co wielokąt: gdy chcesz zamazać tylko część twarzy, nieregularny kształt (np. twarz częściowo za czymś), albo gdy prostokąt / owal zahacza o coś, czego nie chcesz zamazywać.
- Wielokąt skaluje się razem z trackerem: punkty są zapamiętywane względem początkowej ramki i przeliczane przy zmianie rozmiaru.

### `PIXEL` — checkbox (domyślnie odznaczony)

- **Zaznaczony:** zamazanie to pikselizacja (mozaika) zamiast rozmycia Gaussa.
- **Odznaczony:** rozmycie Gaussa (albo BLACKBOX, jeśli ten jest zaznaczony).
- Siła pikselizacji zależy od suwaka **Rozmycie**.
- Pikselizacja przy zbyt niskim **Rozmyciu** może nadal pozwalać odgadnąć twarz — sprawdź na pauzie i na zoomie.

### Pasek statusu (na samym dole)

Pokazuje na bieżąco:

```
Klatka: 123/1250 | Zoom: 1.0x | Aktywne twarze: 2 | Kształt: OWAL | Rozmycie: GAUSS
```

albo w trybie zdjęcia:

```
ZDJĘCIE | Zoom: 2.5x | Twarzy: 1 | Kształt: WIELOKĄT (LINE) | Rozmycie: PIXEL
```

`Aktywne twarze` to liczba trackerów, które mają zamazanie na bieżącej klatce. Jeśli widzisz tu `0`, a na podglądzie jest zamazany obszar — to znaczy, że to nie tracker, tylko coś innego (np. artefakt wideo). Jeśli widzisz tu liczbę większą niż liczba twarzy, które chcesz zamazać — masz fałszywe trackery (patrz: Problem 1).

---

## Mysz, zoom i rysowanie

### Na podglądzie wideo

| Gest | Co robi |
| --- | --- |
| **Klik lewym na zieloną ramkę** | **AUTO** — dodaje tracker dla tej wykrytej twarzy. |
| **Klik lewym poza zieloną ramką** | Nic nie dodaje. W pasku informacyjnym pojawi się `Klik (x,y) poza ramką.` |
| **Przeciągnięcie lewym (rysowanie prostokąta)** | **MANUAL** — tworzy tracker-prostokąt o narysowanym rozmiarze. Minimum 8×8 px. |
| **Najedź na środek ramki trackera** | Kursor zmienia się w „przesuwanie” (strzałki w 4 strony). Przeciągnięcie przesuwa całe zamazanie. |
| **Najedź na krawędź / narożnik ramki trackera** | Kursor zmienia się w strzałki zmiany rozmiaru. Przeciągnięcie zmienia rozmiar zamazania. |
| **Scroll (kółko myszy)** | Zoom 1,0×–12×, krok ×1,25. Zoom centruje się na pozycji kursora. |
| **Prawy przycisk — klik (bez przeciągania)** | **Usuwa tracker** pod kursorem (ten, w którego owal / prostokąt kliknąłeś). To najszybszy sposób usuwania fałszywych zamazań. |
| **Prawy przycisk — przeciągnięcie, gdy zoom > 1×** | Przesuwanie widoku (Pan). |
| **Shift + lewy przycisk + przeciągnięcie** | Przesuwanie widoku (Pan) — niezależnie od zoomu. |
| **LINE: klik lewym** | Dodaje kolejny punkt wielokąta. |
| **LINE: podwójne kliknięcie** | Zamyka wielokąt i tworzy tracker-wielokąt (minimum 3 punkty, minimum 5×5 px). |
| **LINE: prawy przycisk** | Kasuje rozpoczęty wielokąt (punkty). |

### Co się dzieje, gdy przesuniesz / zmienisz rozmiar trackera nie na jego klatce startowej

To jest subtelne i ważne:

- Jeśli poprawiasz tracker **na jego klatce startowej** — poprawiasz ten sam segment od początku.
- Jeśli poprawiasz tracker **na późniejszej klatce** — program **dzieli segment na dwa**:
  - stary segment kończy się na klatce poprzedzającej bieżącą,
  - nowy segment (z tym samym numerem `Twarz`, ale nowym `ID`) zaczyna się na bieżącej klatce z poprawioną ramką i nowym trackerem CSRT.
- Dzięki temu poprawka w środku filmu nie psuje zamazania we wcześniejszych klatkach, gdzie stara ramka była dobra.

### Zoom i Pan — wskazówki

- Do precyzyjnej poprawki ramki: zatrzymaj odtwarzanie, przybliż scrollem, przesuń / zmień rozmiar, oddal przyciskiem **ZOOM 1:1**.
- Zoom nie zmienia zamazania ani eksportu — tylko to, co widzisz.
- Jeśli po zoomie klikasz „w pustkę”, sprawdź, czy kursor na pewno jest nad obrazem, a nie nad czarnym paskiem wokół niego.

---

## Ręczne kasowanie zamazania

To jest kasowanie już dodanego zamazania (trackera). Nie myl go z:
- **PAUSE** — to nie kasowanie, tylko zakończenie zamazania od bieżącej klatki w przód. To, co było wcześniej, zostaje.
- **Kasuj znaczniki** — to kasowanie fragmentu do eksportu (OD/DO), a nie zamazania.
- **Zielona ramka** — to tylko propozycja detektora. Jej się nie kasuje. Znika sama po zmianie klatki. Zamazaniem staje się dopiero po kliknięciu / narysowaniu.

Skasowane zamazanie znika z podglądu i nie trafi do eksportu. Nie ma przycisku „przywróć” — po skasowaniu trzeba dodać zamazanie od nowa, jeśli było potrzebne.

### Sposób 1 — prawy przycisk myszy na zamazaniu (na podglądzie, najszybszy)

1. Zatrzymaj odtwarzanie (**⏹ STOP**).
2. Przejdź na klatkę, na której widać zamazanie do skasowania.
3. Jeśli jesteś w trybie **LINE** i masz rozpoczęty, niedokończony wielokąt (punkty na ekranie), najpierw go skasuj prawym przyciskiem albo wyłącz i włącz **LINE**. W przeciwnym razie prawy przycisk skasuje punkty wielokąta, a nie zamazanie.
4. Najedź kursorem **na środek** zamazanego obszaru. Środek jest najpewniejszy.
5. Kliknij **prawym przyciskiem myszy raz, bez przeciągania**.

Co się dzieje w zależności od zoomu:

| Sytuacja | Zachowanie prawego przycisku |
| --- | --- |
| Zoom 1:1 | Klik prawym = kasuje tracker pod kursorem. |
| Zoom powyżej 1:1, klik bez przeciągnięcia | Klik prawym = kasuje tracker pod kursorem. |
| Zoom powyżej 1:1, przeciągnięcie prawym | To nie jest kasowanie, tylko przesuwanie widoku (Pan). |

Dlatego przy kasowaniu: **kliknij i od razu puść, nie przeciągaj**.

Jak program sprawdza, czy trafiłeś:

- Gdy **CIRCLE** jest zaznaczone (domyślnie), program sprawdza, czy kliknąłeś wewnątrz owalu. Ma ok. 25% zapasu poza owalem, ale klik przy samej krawędzi może nie trafić. Klik w środek zawsze trafia.
- Gdy **CIRCLE** jest odznaczone, program sprawdza prostokąt trackera.
- Gdy tracker to wielokąt **LINE**, program sprawdza jego ramkę ograniczającą (prostokąt wokół wielokąta), a nie sam kształt wielokąta.

Jeśli po prawym kliku zamazanie nie zniknęło: kliknąłeś poza owalem / prostokątem, byłeś w trybie rysowania LINE z punktami, albo przeciągnąłeś mysz (Pan zamiast kasowania). Spróbuj kliknąć w środek albo użyj Sposobu 2.

### Sposób 2 — przycisk X na liście po prawej (najpewniejszy)

1. Znajdź na liście wpis tego zamazania: `Twarz N [ID...] [start-koniec]`.
2. Jeśli nie wiesz, który to wpis, klikaj po kolei **IDŹ** przy wpisach — program skoczy do klatki startowej każdego segmentu i zobaczysz, który leży w złym miejscu.
3. Kliknij **X** przy tym wpisie.

**X kasuje tylko ten jeden segment.** Inne segmenty tej samej twarzy zostają.

Używaj Sposobu 2 zawsze wtedy, gdy:
- zamazania na siebie nachodzą (prawy przycisk może trafić w złe, bo program sprawdza trackery po kolei, a nie „ten na wierzchu”),
- chcesz skasować segment, który na bieżącej klatce nie jest aktywny (np. zaczyna się później),
- prawy przycisk nie trafia, bo zamazanie jest małe albo na krawędzi kadru.

### Sposób 3 — Cofnij / Delete (tylko ostatnie zamazanie)

- Przycisk **Cofnij** albo klawisz `Delete` usuwa **ostatnio dodany** tracker — czyli ostatni wpis na liście.
- To **nie** jest „skasuj zamazanie pod kursorem”. Jeśli chcesz skasować inne zamazanie niż ostatnie, nie używaj Cofnij, użyj Sposobu 1 albo 2.

### Sposób 4 — Wyczyść (wszystkie zamazania)

- Przycisk **Wyczyść** usuwa wszystkie trackery i wszystkie punkty wielokąta.
- Nie kasuje wczytanego pliku ani znaczników eksportu.
- Używaj tylko wtedy, gdy naprawdę chcesz zacząć zamazywanie od zera.

### Ważne: kasowanie usuwa cały segment, nie tylko „odtąd”

Przycisk **X**, prawy przycisk i **Cofnij** kasują **cały segment** `[start-koniec]`, a nie tylko fragment od bieżącej klatki.

Przykład: masz segment `Twarz 1 [ID3] [100-300]`. Jesteś na klatce 200 i klikasz **X**. Znika zamazanie z klatek 100-300, nie tylko 200-300.

Jeśli chcesz skasować tylko „ogon” segmentu, czyli od jakiejś klatki w przód, a początek zostawić:

1. Przejdź na klatkę, od której zamazanie ma zniknąć.
2. Przeciągnij lekko ten tracker (środek albo krawędź). Program podzieli segment na dwa: stary kończy się na klatce poprzedzającej, nowy zaczyna się na bieżącej klatce.
3. Na liście pojawi się nowy wpis dla tego samego numeru `Twarz`, ale z nowym `ID`.
4. Kliknij **X** przy tym nowym wpisie. Stary segment zostaje, ogon znika.

To jest ten sam mechanizm, który jest opisany w Problemie 2 jako podział segmentu przy odwróceniu twarzy.

### Co się dzieje z poprzednim segmentem tej samej twarzy po skasowaniu

Gdy kasujesz segment, a ta sama twarz ma wcześniejszy segment, który był zakończony (miał ustawiony koniec), program „odblokowuje” ten wcześniejszy segment — jego koniec wraca na otwarty (`...`).

Przykład:
- Było: `Twarz 1 [ID2] [0-100]` i `Twarz 1 [ID3] [101-...]`.
- Kasujesz `ID3` (X / prawy przycisk / Cofnij).
- Zostaje: `Twarz 1 [ID2] [0-...]` — czyli zamazanie znowu trwa bez przerwy.

Dlatego po każdym skasowaniu sprawdź listę — czasem program automatycznie wydłuży poprzedni segment i trzeba go potem skrócić przyciskiem **PAUSE** na właściwej klatce.

### Jak sprawdzić, czy kasowanie się udało

1. Przewiń o kilka klatek wstecz i w przód (`+1` / `-1`). Zamazanie ma zniknąć na całym przedziale tego segmentu.
2. Sprawdź listę po prawej — wpisu ma już nie być.
3. Sprawdź pasek statusu: `Aktywne twarze: N` ma się zmniejszyć o 1 na klatkach, gdzie to zamazanie było jedyne.
4. Dopiero wtedy eksportuj.

### Najczęstsze błędy przy ręcznym kasowaniu

| Błąd | Dlaczego nie działa |
| --- | --- |
| Prawy przycisk z przeciągnięciem przy zoomie | To jest Pan (przesuwanie widoku), nie kasowanie. Kliknij bez przeciągania. |
| Prawy przycisk przy rozpoczętym wielokącie LINE | Kasuje punkty wielokąta, nie tracker. Najpierw skończ albo skasuj wielokąt. |
| Klik na krawędzi owalu przy włączonym CIRCLE | Klik poza owalem nie trafia. Kliknij w środek zamazania. |
| Cofnij, żeby skasować zamazanie pod kursorem | Cofnij kasuje ostatni dodany tracker, nie ten pod kursorem. Użyj X albo prawego przycisku. |
| PAUSE zamiast kasowania | PAUSE tylko kończy zamazanie od bieżącej klatki. Wcześniejsze klatki zostają zamazane. |
| Kasuj znaczniki zamiast kasowania zamazania | Kasuj znaczniki kasuje fragment do eksportu, nie tracker. Zamazanie zostanie. |

## Ręczna edycja ramki — zmiana położenia i rozmiaru chwytając myszką

W README wcześniej było o tym tylko zdanie w tabeli myszy: „środek = przesuwanie, krawędź = zmiana rozmiaru”. Tu jest pełny opis, bo to jest główny sposób poprawiania zamazania, które leży za wysoko, za nisko, za ciasno albo „odjechało” od twarzy.

Edycja dotyczy **geometrii** ramki: gdzie leży i jak duża jest. Nie zmienia siły rozmycia, kształtu (CIRCLE / BLACKBOX / PIXEL) ani przedziału klatek — chyba że edytujesz nie na klatce startowej, wtedy program dzieli segment (patrz dalej: „Co się dzieje po puszczeniu przycisku”).

### Zanim zaczniesz — 3 warunki

1. **Zatrzymaj odtwarzanie** (⏹ STOP). Edycji nie robi się w ruchu.
2. **Przejdź na klatkę, na której to zamazanie jest aktywne.** Czyli klatka musi leżeć w przedziale tego segmentu: od `start_frame` do `end_frame` albo do końca, jeśli segment jest otwarty (`...`). Na klatce spoza przedziału program nie pokaże chwytów do tej ramki i jej nie złapiesz.
3. **Nie trzymaj Shift i nie rysuj wielokąta LINE.** Shift + lewy przycisk to przesuwanie widoku (Pan), a nie edycja. A jeśli w trybie LINE masz już rozpoczęte punkty wielokąta, lewy przycisk doda kolejny punkt, a nie złapie ramki. Najpierw skasuj rozpoczęty wielokąt prawym przyciskiem.

### Krok 1. Najedź myszą — nie klikaj od razu

Chwyty działają tylko po najechaniu. Nie klikaj na ślepo w zamazany obszar.

Najedź kursorem na ramkę i poczekaj na 2 sygnały:

- pojawi się **turkusowa ramka** z kwadracikami w narożnikach i krzyżykiem na środku,
- zmieni się **kształt kursora**.

Jeśli ramka się nie pojawiła, nie jesteś nad chwytem tego trackera. Przesuń kursor bliżej środka / krawędzi / narożnika.

### Krok 2. Który chwyt gdzie jest — 9 stref

Program sprawdza strefy w tej kolejności: najpierw środek, potem narożniki, potem krawędzie. Przy nachodzących na siebie zamazaniach wygrywa ostatnio dodany tracker.

| Strefa, na którą najeżdżasz | Co złapiesz | Kursor |
| --- | --- | --- |
| **Środek ramki** — mały okrąg wokół środka | **Przesuwanie całej ramki** | Strzałki w 4 strony (`fleur`) |
| **Lewy górny narożnik** | Zmiana rozmiaru: lewa + górna krawędź naraz | Przekątna NW-SE |
| **Prawy górny narożnik** | Zmiana rozmiaru: prawa + górna krawędź naraz | Przekątna NE-SW |
| **Lewy dolny narożnik** | Zmiana rozmiaru: lewa + dolna krawędź naraz | Przekątna NE-SW |
| **Prawy dolny narożnik** | Zmiana rozmiaru: prawa + dolna krawędź naraz | Przekątna NW-SE |
| **Lewa krawędź** (nie narożnik) | Zmiana rozmiaru: tylko szerokość, prawa krawędź stoi w miejscu | Pozioma podwójna strzałka |
| **Prawa krawędź** (nie narożnik) | Zmiana rozmiaru: tylko szerokość, lewa krawędź stoi w miejscu | Pozioma podwójna strzałka |
| **Górna krawędź** (nie narożnik) | Zmiana rozmiaru: tylko wysokość, dolna krawędź stoi w miejscu | Pionowa podwójna strzałka |
| **Dolna krawędź** (nie narożnik) | Zmiana rozmiaru: tylko wysokość, górna krawędź stoi w miejscu | Pionowa podwójna strzałka |

> **Dokładność chwytu zależy od zoomu.** Przy zoomie 1:1 strefa chwytu jest szersza i łatwiej złapać. Przy dużym zoomie strefa jest węższa w pikselach oryginału i trzeba najechać dokładniej. Dlatego do precyzyjnej poprawki przybliż obraz scrollem, ale celuj dokładnie w narożnik / krawędź / środek, a nie „gdzieś w okolice”.

### Krok 3. Złap i przeciągnij lewym przyciskiem

Gdy kursor już pokazuje właściwy chwyt:

1. **Wciśnij i trzymaj lewy przycisk myszy.**
2. **Przeciągnij** — ramka podąża za myszą na żywo, a zamazanie też się przesuwa / rozciąga na bieżącej klatce.
3. **Puść lewy przycisk**, gdy ramka leży dobrze.

Nie klikaj i nie puszczaj od razu — samo kliknięcie bez przeciągnięcia nic nie zmieni, a przy kliknięciu poza chwytem program potraktuje to jako próbę dodania nowego zamazania (klik na zieloną ramkę = AUTO, przeciągnięcie w pustym miejscu = nowy MANUAL).

### Zmiana położenia — chwyt za środek

- Złap za **środek** (kursor 4 strzałki).
- Przeciągnij ramkę w nowe miejsce.
- **Rozmiar się nie zmienia.** Zmienia się tylko `x` i `y` (lewy górny róg).
- Ramka nie wyjdzie poza kadr: program ogranicza ją do `x` od `0` do `szerokość_kadru − szerokość_ramki` i `y` od `0` do `wysokość_kadru − wysokość_ramki`. Jeśli przeciągniesz za krawędź kadru, ramka zatrzyma się na krawędzi.
- Przy wielokącie LINE przesunięcie ramki przesuwa też cały wielokąt, bo punkty wielokąta są zapamiętane względem ramki.

Kiedy używać: zamazanie jest dobrego rozmiaru, ale leży za wysoko / za nisko / w bok od twarzy, bo twarz się przesunęła albo tracker lekko „odjechał”.

### Zmiana rozmiaru — chwyt za krawędź albo narożnik

- Złap za **krawędź** albo **narożnik** (kursor strzałki zmiany rozmiaru).
- Przeciągnij na zewnątrz, żeby powiększyć, do środka, żeby pomniejszyć.

Jak zachowuje się przeciwległa strona:

| Złapany chwyt | Co się rusza | Co stoi w miejscu |
| --- | --- | --- |
| Prawa krawędź | Prawa krawędź | Lewa krawędź |
| Dolna krawędź | Dolna krawędź | Górna krawędź |
| Lewa krawędź | Lewa krawędź | Prawa krawędź |
| Górna krawędź | Górna krawędź | Dolna krawędź |
| Prawy dolny narożnik | Prawa + dolna | Lewy górny narożnik |
| Lewy górny narożnik | Lewa + górna | Prawy dolny narożnik |
| Prawy górny narożnik | Prawa + górna | Lewy dolny narożnik |
| Lewy dolny narożnik | Lewa + dolna | Prawy górny narożnik |

Ograniczenia przy zmianie rozmiaru:

- **Minimalny rozmiar to 10 × 10 px.** Mniejszej ramki nie zrobisz. Jeśli przeciągniesz krawędź za drugą stronę, ramka zatrzyma się na 10 px.
- **Ramka nie wyjdzie poza kadr.** Nie da się jej rozciągnąć poza obraz.
- Przy zmianie rozmiaru **nie trzyma proporcji** — możesz zrobić ramkę szeroką i niską albo wąską i wysoką, niezależnie.
- Przy wielokącie LINE zmiana rozmiaru ramki **rozciąga też wielokąt proporcjonalnie**: punkty są przeliczane jako `nowy_rozmiar / stary_rozmiar` osobno dla szerokości i wysokości. Czyli jeśli poszerzysz ramkę 2 razy, wielokąt też poszerzy się 2 razy.

Kiedy używać: twarz jest bliżej / dalej od kamery niż na klatce startowej, osoba się obróciła i potrzebuje szerszej / węższej ramki, albo początkowa ramka była za ciasna i widać uszy / włosy / brodę.

### Co się dzieje po puszczeniu przycisku — to najważniejsze

To, co się stanie po puszczeniu, zależy od tego, **na której klatce edytujesz**.

#### Wariant A — edytujesz na klatce startowej segmentu

Edytujesz ten sam segment od początku:

- Nowa ramka staje się ramką startową tego segmentu.
- Program tworzy od nowa tracker CSRT od tej nowej ramki na tej klatce.
- Wcześniejsze klatki nie istnieją (to jest start), więc nic się nie psuje.
- Na liście nie pojawia się nowy wpis. Ten sam `ID` zostaje, tylko z nową ramką.

#### Wariant B — edytujesz na późniejszej klatce (nie na startowej)

Program **dzieli segment na dwa**:

- Stary segment kończy się na klatce **poprzedzającej** bieżącą (`end_frame = bieżąca_klatka − 1`).
- Powstaje nowy segment z tym samym numerem `Twarz`, ale z nowym `ID`, który zaczyna się na bieżącej klatce z Twoją poprawioną ramką i nowym trackerem CSRT.
- Wcześniejsze klatki zachowują starą, dobrą ramkę. Późniejsze klatki dostają nową ramkę.

Dzięki temu poprawka w środku filmu nie psuje zamazania we wcześniejszych klatkach, gdzie stara ramka była dobra.

Przykład:
- Było: `Twarz 1 [ID3] [100-...]`.
- Edytujesz ramkę na klatce 200.
- Po puszczeniu jest: `Twarz 1 [ID3] [100-199]` i `Twarz 1 [ID4] [200-...]`.

#### Wariant C — edytujesz zdjęcie

Zdjęcie ma tylko 1 klatkę (klatka 0), więc nie ma podziału. Nowa ramka po prostu zastępuje starą.

### Jak sprawdzić po edycji, czy jest dobrze

1. Zostań na tej klatce i sprawdź, czy ramka / zamazanie leży dokładnie na twarzy. Przy owalach (CIRCLE) zostaw mały zapas wokół twarzy — włosy, uszy i broda nie mogą wystawać.
2. Przewiń o kilka klatek w przód (`+1`, `+5`) i wstecz (`−1`, `−5`). Czy tracker od nowej ramki trzyma się twarzy? Czy nie przeskoczył na tło?
3. Sprawdź listę po prawej: jeśli edytowałeś nie na starcie, pojawił się nowy wpis z nowym `ID`. To normalne — to podział, nie duplikat.
4. Jeśli po edycji tracker zaczyna „pływać” albo odjeżdżać, cofnij poprawkę (skasuj nowy segment przyciskiem **X** i dodaj tracker od nowa na tej klatce) albo zmniejsz **Tolerancję skoku**.

### Typowe sytuacje — którą poprawkę wybrać

| Problem na podglądzie | Co złapać |
| --- | --- |
| Zamazanie dobrego rozmiaru, ale przesunięte w bok / w górę / w dół | Środek — przesuń całość |
| Zamazanie za ciasne, widać uszy / włosy / brodę | Narożnik — powiększ w obie strony naraz |
| Zamazanie za szerokie, zahacza o drugą osobę | Lewa albo prawa krawędź — zwęź tylko w poziomie |
| Zamazanie za wysokie / za niskie, zahacza o czoło / szyję | Górna albo dolna krawędź — skróć tylko w pionie |
| Twarz zbliżyła się do kamery i urosła | Prawy dolny narożnik — powiększ szerokość i wysokość |
| Tracker odjechał i ramka leży na tle | Nie poprawiaj starej ramki w nieskończoność. Skasuj zły segment (X / prawy przycisk) i dodaj tracker od nowa na dobrej klatce. |

### Najczęstsze błędy przy ręcznej edycji ramki

| Błąd | Dlaczego nie działa |
| --- | --- |
| Klikasz bez czekania na zmianę kursora | Nie złapałeś chwytu. Program potraktuje to jako rysowanie nowego MANUAL albo klik AUTO. Najpierw najedź i poczekaj na turkusową ramkę i zmianę kursora. |
| Trzymasz Shift podczas przeciągania | Shift + lewy przycisk to Pan (przesuwanie widoku), nie edycja ramki. Puść Shift. |
| Masz rozpoczęte punkty wielokąta LINE | Lewy przycisk doda punkt wielokąta, nie złapie ramki. Skasuj punkty prawym przyciskiem. |
| Edytujesz na klatce, gdzie segment nie jest aktywny | Nie ma chwytów do złapania. Przejdź na klatkę z przedziału `[start-koniec]` tego segmentu albo kliknij **IDŹ** przy nim na liście. |
| Edytujesz, a po puszczeniu pojawia się drugi wpis na liście | To nie błąd, to podział segmentu (Wariant B). Stary segment kończy się przed Twoją edycją, nowy zaczyna się od niej. |
| Poprawiasz ramkę na każdej klatce z osobna | Nie trzeba. Wystarczy poprawić raz na klatce, gdzie ramka zaczęła odjeżdżać — tracker CSRT poprowadzi nową ramkę dalej. Poprawianie klatka po klatce tworzy mnóstwo segmentów. |
| Robisz ramkę idealnie „na styk” z twarzą | Zostaw zapas. Przy ruchu głowy twarz wyjdzie spod za ciasnej ramki. Lepiej trochę za duża niż za mała. |

## Skróty klawiaturowe

| Klawisz | Działanie |
| --- | --- |
| `←` / `→` | Poprzednia / następna klatka. |
| `Shift + ←` / `Shift + →` | Skok o 10 klatek wstecz / w przód. |
| `↑` / `↓` | Skok o 30 klatek w przód / wstecz. |
| `Delete` | **Cofnij** — usuń ostatnio dodany tracker. |

---

## Jak działa śledzenie

1. **Detekcja (AI):** na wybranych klatkach detektor znajduje twarze i rysuje zielone ramki-kandydatów. Detekcja nie jest uruchamiana na każdej klatce odtwarzania — tylko przy zatrzymaniu, skoku i odświeżeniu.
2. **Zatwierdzenie (Ty):** klik / przeciągnięcie / przycisk zamienia kandydata w tracker.
3. **Śledzenie (CSRT):** tracker `cv2.TrackerCSRT` prowadzi ramkę z klatki na klatkę. Program pamięta trajektorię (`traj`) — czyli położenie ramki na każdej klatce, którą już policzył.
4. **Ponowne zakotwiczenie (AI re-anchoring):** gdy przeskoczysz o więcej niż 3 klatki (np. suwakiem, przyciskiem `+30`, kliknięciem w oś czasu), program nie liczy CSRT klatka po klatce, tylko pyta AI o twarze na docelowej klatce i szuka najbliższej twarzy w zasięgu **Tolerancji skoku** i o podobnym rozmiarze. Jeśli ją znajdzie — tracker „przeskakuje” na nią. Jeśli nie — licznik zgubień rośnie.
5. **Zgubienie:** jeśli tracker 2 razy z rzędu nie da rady (skok za duży, rozmiar zmienił się ponad 2,8×, ramka wyszła poza kadr w ponad 80%), a **Stop gdy zgubi** jest zaznaczone, tracker kończy segment (`end_frame`) i przestaje zamazywać. Jeśli **Stop gdy zgubi** jest odznaczone, tracker trzyma ostatnie położenie.
6. **Ręczne zakończenie:** PAUSE, usunięcie trackera, podział segmentu przez poprawkę na późniejszej klatce.

> Tracker nie rozumie, co to jest „twarz z przodu”, „twarz z tyłu”, „ręka” ani „plakat”. On śledzi **wzór pikseli**, który mu wskazałeś. Dlatego potrafi dalej śledzić odwróconą głowę (patrz: Problem 2) i dlatego czasem „przeskoczy” na podobny wzór w tle (patrz: Problem 1). Twoja kontrola jest częścią działania programu, nie dodatkiem.

---

## Eksport wideo i zdjęć

### Eksport wideo

1. Kliknij **Eksportuj / Zapisz**.
2. Wybierz nazwę pliku `.mp4`.
3. Program pokazuje okno postępu (`%` i licznik klatek).
4. Program przetwarza film od nowa, klatka po klatce, nakładając zamazania zgodnie z trackerami i ich przedziałami `[start_frame – end_frame]` oraz bieżącymi ustawieniami suwaków i checkboxów (Rozmycie, Wtapianie, CIRCLE / BLACKBOX / PIXEL, LINE, Tolerancja skoku, Stop gdy zgubi).
5. Jeśli jest `ffmpeg.exe`, program dokleja oryginalny dźwięk:
   - bez zaznaczonych fragmentów: kopiuje / przekodowuje całą ścieżkę audio z oryginału,
   - z zaznaczonymi fragmentami: wycina dźwięk dla każdego fragmentu (`atrim`) i łączy fragmenty w tej samej kolejności co wideo.
6. Jeśli nie ma `ffmpeg.exe`, eksport zapisze wideo **bez dźwięku**.

**Ważne zasady eksportu:**

- Eksportuje się to, co jest na liście trackerów. Zielone ramki, które nie zostały zatwierdzone, nie są eksportowane.
- Jeśli nie ma znaczników eksportu — eksportowany jest cały film.
- Jeśli są znaczniki — eksportowane są tylko zaznaczone fragmenty, połączone w kolejności.
- Ustawienia zamazania z momentu eksportu dotyczą całego eksportowanego materiału. Nie da się mieć innego Rozmycia na początku filmu, a innego na końcu — chyba że wyeksportujesz film na raty i połączysz zewnętrznie.
- Podczas eksportu nie klikaj w program — poczekaj na komunikat `Gotowe`.

### Eksport zdjęcia

Jak eksport wideo, ale dla jednej klatki: program zapisuje zdjęcie z nałożonymi zamazaniami i bieżącymi ustawieniami. Nazwa domyślna: `zamazane_<oryginalna_nazwa>`.

---

## Zrzut ekranu

- Przycisk **📸 Zrób zrzut ekranu** zapisuje bieżącą klatkę.
- Checkbox **z blur** decyduje, czy zrzut ma zamazania (zaznaczony) czy jest czysty (odznaczony).
- Zrzut używa tych samych ustawień zamazania co podgląd (Rozmycie, Wtapianie, CIRCLE / BLACKBOX / PIXEL).
- Przydatny do: szybkiej kontroli jakości zamazania na zatrzymanej klatce, porównania przed / po, wysłania komuś jednej klatki do akceptacji bez eksportowania całego filmu.

---

## ⚠️ PROBLEM 1: Niepotrzebne zamazane obszary w losowych miejscach

### Jak to wygląda

Na filmie pojawia się zamazany owal / prostokąt tam, gdzie nie ma twarzy do zamazania — np. na ścianie, firance, plakacie, koszulce, dłoni, plecach, fragmencie mebla, na drugiej osobie, którą wcale nie chciałeś zamazywać, albo zamazanie „wisi” w powietrzu tam, gdzie twarz już wyszła z kadru.

### Dlaczego tak się dzieje

To nie jest jeden błąd, tylko kilka różnych mechanizmów, które dają ten sam efekt. Zanim zaczniesz naprawiać, ustal, który to przypadek — bo każdy naprawia się inaczej:

| Przyczyna | Jak ją rozpoznać |
| --- | --- |
| **1. Fałszywa detekcja AI** | Na bieżącej klatce jest zielona ramka tam, gdzie nie ma twarzy. AI uznało wzór / plakat / cień za twarz. |
| **2. Tracker „przeskoczył” (drift / jump)** | Zamazanie na początku leżało dobrze na twarzy, a po kilku klatkach „odjechało” w bok i przykleiło się do czegoś innego. Często po szybkim ruchu głowy, ruchu kamery albo zasłonięciu twarzy. |
| **3. Tracker nie zakończył się po zniknięciu twarzy** | Twarz wyszła z kadru / schowała się za kimś, a zamazanie zostało w miejscu, gdzie twarz była ostatnio. Często przy odznaczonym **Stop gdy zgubi** albo przy bardzo wysokiej **Tolerancji skoku**. |
| **4. Ślepe skanowanie AI** | Po **Zamaż cały film (AI)** program dodał zamazania do wszystkiego, co AI uznało za twarz — w tym do fałszywych wykryć. Lista po prawej ma wtedy dużo wpisów, których nie dodawałeś ręcznie. |
| **5. Stary tracker po poprawce** | Poprawiłeś ramkę na późniejszej klatce, program podzielił segment na dwa, a stary segment (przed poprawką) nadal zamazuje w swoim przedziale — i to właśnie ten stary fragment wygląda jak „losowe zamazanie”. |

### Procedura naprawy — krok po kroku

**Nie eksportuj, dopóki tego nie zrobisz. Naprawa po eksporcie oznacza eksport od nowa.**

#### Krok 1. Zatrzymaj odtwarzanie

Naciśnij **⏹ STOP** (jeśli film gra). Naprawiasz na zatrzymanych klatkach, nie w ruchu.

#### Krok 2. Znajdź pierwszą klatkę, na której pojawia się złe zamazanie

- Przewiń film suwakiem / przyciskami `+1` / `-1` albo klikaj w pasek znaczników.
- Zatrzymaj się dokładnie na pierwszej klatce, gdzie zamazanie jest już w złym miejscu.
- Zapamiętaj numer tej klatki (pole numeru klatki / licznik czasu).

#### Krok 3. Zidentyfikuj, który tracker to robi

Masz trzy równorzędne sposoby — wybierz ten, który jest najszybszy w danym momencie:

- **Sposób A — lista po prawej:** klikaj po kolei **IDŹ** przy każdym wpisie. Ten, który skacze do klatki w pobliżu Twojej złej klatki i którego ramka leży w złym miejscu, to winowajca.
- **Sposób B — pasek statusu:** na złej klatce sprawdź `Aktywne twarze: N`. Jeśli `N` jest większe niż liczba twarzy, które chcesz zamazać, masz co najmniej jeden zbędny tracker.
- **Sposób C — najechanie myszą:** najedź na złe zamazanie. Jeśli pojawi się turkusowa ramka z uchwytami — to jest aktywny tracker.

#### Krok 4. Usuń tylko ten jeden zły tracker

Wybierz **jeden** sposób — nie rób wszystkich naraz, bo usuniesz więcej niż trzeba:

- **Najszybszy:** prawy przycisk myszy na złym zamazaniu (klik bez przeciągania, przy zoomie 1:1). Tracker pod kursorem znika.
- **Najpewniejszy:** w liście po prawej kliknij **X** przy właściwym wpisie (`Twarz N [ID...]`).
- **Ostatni dodany:** jeśli złe zamazanie to właśnie to, co dodałeś przed chwilą, kliknij **Cofnij** (albo `Delete`).

**Sprawdź od razu:** przewiń o kilka klatek wstecz i w przód. Czy złe zamazanie zniknęło na całym podejrzanym fragmencie? Czy dobre zamazania zostały?

#### Krok 5. Jeśli złe zamazanie wraca w kolejnych klatkach — to nie jeden tracker, tylko seria

Zdarza się po skanowaniu AI: ta sama fałszywa „twarz” została wykryta jako kilka kolejnych segmentów albo tracker po usunięciu został odtworzony przez ponowne zakotwiczenie.

- Przewiń przez cały podejrzany fragment klatka po klatce (`+1` / `-1`).
- Usuwaj kolejne złe wpisy z listy (**X**), aż fragment będzie czysty.
- Jeśli lista jest bardzo zaśmiecona, rozważ **Wyczyść** i dodanie zamazań ręcznie od nowa — czasem to jest szybsze niż usuwanie 20 fałszywych wpisów.

#### Krok 6. Jeśli tracker „odjeżdża” z dobrej twarzy na zły obiekt — zmniejsz Tolerancję skoku

- Ustaw **Tolerancję skoku** niżej, np. z `130` na `70–100`.
- Wróć do klatki, gdzie tracker jeszcze leżał dobrze.
- Usuń zły segment (Krok 4) i dodaj tracker od nowa od tej dobrej klatki (klik na zieloną ramkę / przeciągnięcie).
- Przewiń podejrzany fragment i sprawdź, czy tracker tym razem trzyma się twarzy.

> Nie zmniejszaj Tolerancji do `0` „na wszelki wypadek” — wtedy tracker będzie się poddawał przy każdym szybszym ruchu głowy i zamazanie będzie znikać w połowie sceny. Szukaj wartości, przy której tracker trzyma twarz, ale nie przeskakuje na tło.

#### Krok 7. Jeśli zamazanie „wisi” tam, gdzie twarz już wyszła — włącz Stop gdy zgubi

- Zaznacz **Stop gdy zgubi** (powinno być zaznaczone domyślnie).
- Jeśli problem dotyczy już dodanego trackera, usuń go i dodaj od nowa z włączonym **Stop gdy zgubi** — zmiana checkboxa nie cofa wstecz już policzonej trajektorii, tylko wpływa na dalsze śledzenie i na eksport.
- Przy eksporcie program też respektuje ten checkbox: przy zaznaczonym przestaje zamazywać po 2–3 zgubionych klatkach, przy odznaczonym trzyma ostatnie położenie.

#### Krok 8. Jeśli to był błąd AI (zielona ramka na czymś, co twarzą nie jest)

- **Nie klikaj** tej zielonej ramki i **nie** używaj na tej klatce przycisku **Zamaż wszystkie zielone na bieżącej klatce** — bo to właśnie doda fałszywy tracker.
- Jeśli fałszywy tracker już jest — usuń go (Krok 4).
- Spróbuj zmienić model w liście `Model:` (np. YuNet → SCRFD 500M albo odwrotnie) i zobacz, czy fałszywa zielona ramka znika na tej klatce. Jeśli tak, pracuj dalej na tym modelu.
- Jeśli fałszywa ramka pojawia się tylko na jednej klatce i nie zamieniłeś jej w tracker, zignoruj ją — sama w sobie nie trafi do eksportu.

#### Krok 9. Kontrola końcowa przed eksportem

1. Przewiń cały film na przyspieszeniu (np. 50 kl./s) i patrz tylko na to, czy zamazania są tam, gdzie powinny.
2. Zatrzymaj się na każdym podejrzanym miejscu i sprawdź klatka po klatce.
3. Sprawdź listę po prawej: liczba wpisów powinna odpowiadać liczbie osób × liczbie segmentów. Jeśli jest wyraźnie za dużo wpisów — coś jeszcze jest do usunięcia.
4. Zrób zrzut ekranu (**📸 Zrób zrzut ekranu** + **z blur**) na najtrudniejszych klatkach i obejrzyj go na pełnym ekranie.
5. Dopiero wtedy **Eksportuj / Zapisz**.

### Czego NIE robić przy losowych zamazaniach

- **Nie** zakrywaj złego zamazania drugim, większym zamazaniem. W eksporcie będziesz mieć dwa zamazania, a problem zostanie.
- **Nie** licz na to, że „w eksporcie samo zniknie”. Eksport używa tych samych trackerów co podgląd. Jeśli widzisz złe zamazanie na podglądzie, będzie ono też w eksporcie.
- **Nie** używaj **Wyczyść**, jeśli masz już dużo dobrej pracy — najpierw usuń tylko złe wpisy (**X** / prawy przycisk). **Wyczyść** to ostateczność.
- **Nie** zmieniaj **Rozmycia** ani **Wtapiania**, żeby „ukryć” złe zamazanie. To zmienia wygląd wszystkich zamazań, a złe zamazanie nadal będzie w złym miejscu.

---

## ⚠️ PROBLEM 2: Twarz nadal śledzona, ale już odwrócona — czy dalej zamazywać?

### O co chodzi

Osoba odwraca głowę tyłem / profilem. Na podglądzie widzisz, że zamazany owal nadal podąża za głową — mimo że twarzy (oczu, nosa, ust) już nie widać. Pytanie: czy to zamazanie jest jeszcze potrzebne?

### Dlaczego program to robi — to nie jest błąd

Tracker CSRT nie rozpoznaje „twarzy”. On śledzi **wzór pikseli**, który mu wskazałeś na klatce startowej: kształt głowy, włosy, ucho, fragment szyi, cień.

Dlatego:

- gdy twarz odwraca się tyłem, tracker nie „wie”, że twarzy już nie widać — on nadal widzi głowę i ją śledzi,
- zielona ramka AI może zniknąć (bo AI nie wykrywa już twarzy z tyłu), ale turkusowe zamazanie trackera zostaje,
- program **sam z siebie nie przerwie** zamazania przy odwróceniu. To Ty musisz zdecydować, kiedy je zakończyć.

### Kiedy ZOSTAWIĆ zamazanie na odwróconej głowie

Zostaw, gdy odwrócona głowa nadal pozwala rozpoznać osobę albo gdy zamazanie jest częścią ciągłości sceny:

- widać **profil** — bok twarzy, nos, usta, oko z boku często wystarczą do rozpoznania,
- widać **ucho + charakterystyczną fryzurę / kolor włosów / łysinę / tatuaż na karku / znamię**,
- osoba jest blisko kamery i odwrócenie trwa tylko chwilę (1–2 sekundy) — cięcie zamazania na tak krótko i tak częste „mruganie” zamazania wygląda gorzej niż zostawienie go,
- w kadrze jest mało osób i odwrócona głowa jest głównym obiektem — widz i tak skojarzy ją z osobą sprzed chwili,
- zamazujesz ze względów prawnych / RODO i wolisz być po bezpiecznej stronie.

### Kiedy PRZERWAĆ zamazanie na odwróconej głowie

Przerwij, gdy zamazanie nie chroni już nic, a tylko psuje obraz:

- widać **tylko tył głowy** z daleka, w tłumie, bez żadnych cech charakterystycznych, a osoba za chwilę zniknie z kadru,
- głowa odwróciła się **na stałe** (osoba odchodzi, siada tyłem, rozmawia z kimś plecami do kamery) i nie wróci przodem w tej scenie,
- zamazanie na potylicy zasłania coś ważnego w kadrze (np. inną osobę, tablicę, przedmiot), a nie chroni prywatności,
- tracker po odwróceniu zaczyna „pływać” — bo tył głowy ma mniej charakterystyczny wzór niż twarz — i zamazanie odjeżdża na tło (wtedy wracasz do Problemu 1).

**Zasada praktyczna:** jeśli po odwróceniu nie da się rozpoznać osoby po samej głowie (bez kontekstu sceny), a odwrócenie trwa dłużej niż kilka sekund — przerwij. Jeśli da się rozpoznać albo odwrócenie jest chwilowe — zostaw.

### Procedura — jak przerwać zamazanie dokładnie w momencie odwrócenia

#### Wariant A — najprostszy: PAUSE (gdy odwrócenie jest na stałe)

1. Zatrzymaj odtwarzanie (**⏹ STOP**).
2. Przyciskami `+1` / `-1` (albo suwakiem) znajdź **ostatnią klatkę, na której twarz jest jeszcze rozpoznawalna** (np. ostatni profil, ostatnie półprofil).
3. Przejdź **o 1 klatkę dalej** — na pierwszą klatkę, na której twarz jest już odwrócona i nie chcesz jej zamazywać.
4. Kliknij **⏸ PAUSE**.
5. Sprawdź:
   - na bieżącej klatce zamazanie tej twarzy zniknęło,
   - na poprzedniej klatce zamazanie jeszcze jest,
   - w liście po prawej segment tej twarzy ma ustawiony koniec (np. `[120-245]` zamiast `[120-...]`).
6. Przewiń dalej i sprawdź, czy zamazanie nie wraca w kolejnych klatkach.

> **Ważne:** PAUSE kończy **wszystkie** aktywne trackery, nie tylko jedną twarz. Jeśli w kadrze są 2 osoby i tylko jedna się odwróciła, a druga nadal powinna być zamazana, **nie** używaj PAUSE dla wszystkich. Użyj Wariantu B.

#### Wariant B — precyzyjny: usuń / zakończ tylko tę jedną twarz

Gdy w kadrze jest więcej osób albo chcesz zakończyć tylko jeden segment:

1. Zatrzymaj odtwarzanie.
2. Znajdź klatkę, od której nie chcesz już zamazywać tej twarzy (jak w Wariancie A, krok 2–3).
3. **Opcja 1 — usunięcie całego segmentu:** w liście po prawej kliknij **X** przy segmencie tej twarzy. Uwaga: to usuwa **cały** segment od jego początku, nie tylko od bieżącej klatki. Używaj, gdy cały segment był błędny albo gdy twarz była odwrócona od samego początku segmentu.
4. **Opcja 2 — zakończenie segmentu w tym miejscu bez usuwania początku:** kliknij **⏸ PAUSE** tylko wtedy, gdy to jedyna aktywna twarz. Jeśli jest więcej twarzy, zamiast tego:
   - kliknij prawym przyciskiem na zamazanie tej twarzy (usuwa cały segment tej twarzy od początku — jak Opcja 1), **albo**
   - zostaw segment, ale po eksporcie sprawdź, czy nie trzeba go podzielić — w praktyce najczystsze jest: usuń ten segment (**X**) i dodaj go od nowa tylko na przedziale, gdzie twarz jest widoczna przodem (od klatki startowej do klatki odwrócenia), a potem go nie kontynuuj.

W praktyce większość użytkowników robi tak:

- **Jedna osoba w kadrze → PAUSE** w momencie odwrócenia.
- **Wiele osób w kadrze → X na liście** dla tej jednej twarzy + ponowne dodanie jej tylko na przedziale, gdzie ma być zamazana.

#### Wariant C — twarz odwraca się na chwilę i wraca przodem: PAUSE → RESUME

Gdy odwrócenie trwa tylko chwilę (np. osoba odwraca się, żeby coś podać, i wraca):

1. Na pierwszej klatce odwrócenia kliknij **⏸ PAUSE**.
2. Przewiń do klatki, na której twarz znowu jest widoczna przodem / profilem.
3. Kliknij **▶ RESUME**.
4. Program utworzy nowy segment dla tej twarzy od bieżącej klatki.
5. Sprawdź klatka po klatce, czy nowy segment leży dobrze na twarzy. Jeśli nie — popraw go, przeciągając środek / krawędzie (program podzieli segment, patrz: sekcja o poprawianiu trackera).

> **Pułapka RESUME:** RESUME wznawia **wszystkie** zakończone trackery. Jeśli wstrzymałeś 2 twarze, a chcesz wznowić tylko jedną, RESUME wznowi obie. Wtedy drugą usuń ręcznie (**X** / prawy przycisk) albo nie używaj RESUME, tylko dodaj tę jedną twarz od nowa kliknięciem w zieloną ramkę.

#### Wariant D — twarz odwraca się, ale tracker i tak „ciągnie” zamazanie dalej: wymuś zakończenie

Jeśli po PAUSE zamazanie nadal wraca w kolejnych klatkach (bo tracker został ponownie zakotwiczony przez AI albo bo segment nie został zakończony):

1. Sprawdź listę: czy segment tej twarzy ma koniec (`[start-koniec]`), czy jest otwarty (`[start-...]`)?
2. Jeśli jest otwarty — usuń go (**X**) i dodaj od nowa tylko na przedziale, gdzie twarz ma być zamazana.
3. Sprawdź, czy **Stop gdy zgubi** jest zaznaczone. Jeśli nie, zaznacz je — przy odwróceniu i gwałtownym ruchu tracker szybciej się podda zamiast „wisieć” na potylicy.
4. Zmniejsz **Tolerancję skoku** (np. do 70–100), jeśli tracker po odwróceniu przeskakuje na tło.
5. Przewiń cały fragment odwrócenia klatka po klatce i upewnij się, że po klatce odwrócenia nie ma już zamazania tej twarzy.

### Jak sprawdzić po naprawie, czy odwrócenie jest dobrze obsłużone

1. Znajdź klatkę odwrócenia.
2. Klatka przed: zamazanie jest (jeśli twarz była jeszcze rozpoznawalna).
3. Klatka odwrócenia: zamazania nie ma (jeśli zdecydowałeś przerwać) albo jest (jeśli zdecydowałeś zostawić — świadomie).
4. 5–10 klatek dalej: stan taki sam jak w punkcie 3 — zamazanie nie „wraca samo”.
5. Lista po prawej: segment ma poprawny koniec albo jest nowy segment od momentu powrotu twarzy.
6. Zrzut ekranu na klatce odwrócenia (z blur) — obejrzyj go na pełnym ekranie, zanim wyeksportujesz.

### Czego NIE robić przy odwróconej twarzy

- **Nie** zostawiaj zamazania „bo program sam tak zrobił”. Program nie podejmuje decyzji o prywatności — Ty ją podejmujesz.
- **Nie** klikaj RESUME „żeby zobaczyć, co się stanie”, jeśli odwrócenie jest na stałe — RESUME przywróci zamazywanie.
- **Nie** usuwaj całego filmu / wszystkich trackerów (**Wyczyść**) tylko dlatego, że jedna twarz się odwróciła — usuń / zakończ tylko ten jeden segment.
- **Nie** licz na to, że tracker sam zgubi odwróconą głowę. Tył głowy jest dla CSRT często wystarczająco podobny do przodu, żeby tracker trwał dalej przez długi czas.

---

## Dobre praktyki — kolejność pracy, która oszczędza poprawki

1. **Najpierw obejrzyj film raz bez zamazywania.** Zaznacz w głowie, kogo trzeba zamazać, a kogo nie.
2. **Potem dodawaj zamazania.** Ręcznie (AUTO / MANUAL) dla dokładności albo skanowaniem AI jako punktem startowym.
3. **Potem kontrola:** lista po prawej (`IDŹ` przy każdym wpisie) + przewinięcie całego filmu na przyspieszeniu + klatka po klatce w trudnych miejscach.
4. **Potem poprawki:** usuń fałszywe (Problem 1), skróć odwrócone (Problem 2), popraw pozycję / rozmiar.
5. **Potem parametry:** Rozmycie, Wtapianie, kształt (CIRCLE / PIXEL / BLACKBOX).
6. **Dopiero potem eksport.** Eksport to ostatni krok, nie pierwszy.

---

## Rozwiązywanie problemów

| Objaw | Najczęstsza przyczyna | Co zrobić |
| --- | --- | --- |
| Brak zielonych ramek, mimo że twarze widać | Zły model, brak pliku `.onnx`, za małe / za ciemne twarze | Zmień model, wskaż plik `.onnx` przyciskiem **Wskaż...**, przybliż zoom, dodaj twarz ręcznie (MANUAL). |
| Zamazanie „skacze” na inną osobę / tło | Za wysoka Tolerancja skoku, szybki ruch, zasłonięcie | Zmniejsz Tolerancję skoku, włącz **Stop gdy zgubi**, dodaj tracker od nowa od dobrej klatki. |
| Zamazanie znika w połowie ruchu | Za niska Tolerancja skoku albo tracker zgubił twarz | Zwiększ Tolerancję skoku, dodaj tracker od nowa, sprawdź, czy twarz nie wyszła poza kadr. |
| Zamazanie wisi tam, gdzie twarzy już nie ma | Odznaczony **Stop gdy zgubi** albo tracker nie zakończony | Zaznacz **Stop gdy zgubi**, zakończ segment (PAUSE / X), usuń zły tracker. |
| Po eksporcie nie ma dźwięku | Brak `ffmpeg.exe` | Włóż `ffmpeg.exe` do folderu programu i wyeksportuj ponownie. |
| Eksportuje się cały film, mimo że zaznaczyłeś fragment | Znaczniki nie zostały zamknięte (kliknąłeś tylko **OD**) | Kliknij **Eksport DO** na klatce końcowej. Fragment pojawia się na pasku znaczników jako zielony prostokąt. |
| Program pokazuje `PAUZA ŚLEDZENIA – BRAK ROZMYCIA`, a Ty chcesz zamazywać | Śledzenie wstrzymane przyciskiem PAUSE | Kliknij **RESUME** albo dodaj twarz od nowa (klik / przeciągnięcie). |
| Po kliknięciu zielonej ramki nic się nie dzieje | Klik poza ramką (za duży margines) albo brak wczytanej klatki | Kliknij dokładnie w środek zielonej ramki, sprawdź, czy klatka jest wczytana. |
| Wielokąt LINE nie chce się zamknąć | Za mało punktów albo podwójne kliknięcie poza obrazem | Narysuj min. 3 punkty, zamknij podwójnym kliknięciem na obrazie, nie na czarnym pasku. |
| Wideo się nie otwiera | Nieobsługiwany kodek / uszkodzony plik | Spróbuj przekonwertować wideo na `.mp4` (H.264) zewnętrznym konwerterem. |

---

## Plik `settings.json`

Program zapisuje przy zamykaniu (i wczytuje przy starcie) tylko trzy ustawienia:

```json
{
  "blur_strength": 50,
  "feathering": 15,
  "jump_tolerance": 130
}
```

- `blur_strength` = suwak **Rozmycie**,
- `feathering` = suwak **Wtapianie**,
- `jump_tolerance` = suwak **Tolerancja skoku**.

Pozostałe ustawienia (model, kształt, prędkość, znaczniki, trackery) nie są zapisywane — są odtwarzane przy każdym uruchomieniu od wartości domyślnych.

---

## Ograniczenia

- Program nie rozpoznaje tożsamości ani nie odróżnia „twarzy z przodu” od „twarzy z tyłu” — decyzja o zakończeniu zamazania należy do użytkownika.
- Śledzenie CSRT działa najlepiej przy stabilnej kamerze i umiarkowanym ruchu. Gwałtowne cięcia, szybkie panoramy i częste zasłanianie twarzy wymagają ręcznych poprawek.
- Detekcja AI nie działa na każdej klatce odtwarzania — tylko przy zatrzymaniu / skoku / odświeżeniu. To celowy kompromis na rzecz płynności podglądu.
- Eksport zawsze używa ustawień z momentu kliknięcia **Eksportuj / Zapisz**. Nie ma osobnych ustawień zamazania dla różnych fragmentów filmu.
- Bez `ffmpeg.exe` eksport wideo jest bez dźwięku.

---

## Pliki w repozytorium

- `face_blur_v37_multi_model.py` — główny program.
- `README.md` — ten plik.
- `ffmpeg.exe` — do doklejenia dźwięku (do pobrania osobno, patrz: Wymagania).
- `*.onnx` — pliki modeli AI (do pobrania / włożenia osobno, patrz: Pliki modeli AI).
- `settings.json` — tworzony automatycznie, przechowuje 3 ustawienia suwaków.
