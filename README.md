<img width="1918" height="1154" alt="FacelBlur_v1 37" src="https://github.com/user-attachments/assets/2e8a6614-1464-4d5b-a694-e723dd27b551" />


# Face Blur Tool

Narzędzie do zamazywania twarzy na wideo i zdjęciach. Pracuje lokalnie, na Twoim komputerze. Wykrywa twarze modelem AI, śledzi je klatka po klatce i nakłada rozmycie, pikselozę albo czarny prostokąt / elipsę.

Możesz działać na trzy sposoby:

1. **Ręcznie, jedna twarz na raz** - klikasz twarz albo rysujesz ramkę.
2. **Półautomatycznie** - klikasz „Zamaż wszystkie zielone na bieżącej klatce", a potem poprawiasz ręcznie.
3. **Automatycznie, cały film** - klikasz „Zamaż cały film (AI)" i potem czyścisz błędy.

Program nie wysyła wideo do internetu. Wszystko dzieje się lokalnie.

Repozytorium autora: https://github.com/marek7400

---

## Spis treści

- [Co potrafi program](#co-potrafi-program)
- [Wymagania i instalacja](#wymagania-i-instalacja)
- [Pliki modeli AI i FFmpeg](#pliki-modeli-ai-i-ffmpeg)
- [Szybki start](#szybki-start)
- [Układ okna](#układ-okna)
- [Górny pasek 1 - import, model, detekcja, eksport](#górny-pasek-1---import-model-detekcja-eksport)
- [Górny pasek 2 - śledzenie, zoom, edycja](#górny-pasek-2---śledzenie-zoom-edycja)
- [Podgląd wideo - myszka i rysowanie](#podgląd-wideo---myszka-i-rysowanie)
- [Panel prawy - lista twarzy i segmentów](#panel-prawy---lista-twarzy-i-segmentów)
- [Sterowanie odtwarzaniem](#sterowanie-odtwarzaniem)
- [Oś czasu i znaczniki eksportu](#oś-czasu-i-znaczniki-eksportu)
- [Dolny pasek - suwaki i kształt zamazania](#dolny-pasek---suwaki-i-kształt-zamazania)
- [Tryby dodawania twarzy: AUTO, MANUAL, LINE](#tryby-dodawania-twarzy-auto-manual-line)
- [Jak działa śledzenie](#jak-działa-śledzenie)
- [Eksport](#eksport)
- [Zrzut ekranu](#zrzut-ekranu)
- [Ustawienia zapisywane automatycznie](#ustawienia-zapisywane-automatycznie)
- [Skróty klawiszowe](#skróty-klawiszowe)
- [WAŻNE: Niepotrzebne zamazane obszary w losowych miejscach](#ważne-niepotrzebne-zamazane-obszary-w-losowych-miejscach)
- [WAŻNE: Twarz nadal śledzona, ale już odwrócona](#ważne-twarz-nadal-śledzona-ale-już-odwrócona)
- [Kontrola przed eksportem - checklista](#kontrola-przed-eksportem---checklista)
- [Rozwiązywanie problemów](#rozwiązywanie-problemów)

---

## Co potrafi program

- Wczytuje wideo: `.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`.
- Wczytuje zdjęcie: `.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`.
- Wykrywa twarze czterema modelami AI: YuNet, SCRFD 500M, SCRFD 2.5G, YOLO-Face (Nano). Gdy brak pliku modelu, używa zapasowego detektora Haar Cascade.
- Śledzi każdą wybraną twarz osobno, jako osobny segment z własnym zakresem klatek.
- Pozwala dodać twarz kliknięciem, przeciągnięciem ramki albo narysowaniem wielokąta (LINE).
- Pozwala przesuwać i zmieniać rozmiar już dodanej ramki w dowolnej klatce.
- Pozwala podzielić śledzenie na segmenty: uciąć je w danym miejscu i wznowić później.
- Pozwala usunąć jeden segment, jedną twarz albo wszystko.
- Zamazuje jako: rozmycie Gaussa, pikselozę (PIXEL), czarny prostokąt / elipsę (BLACKBOX).
- Wtapia krawędzie zamazania (feather), żeby nie było ostrej granicy.
- Eksportuje cały film albo tylko wybrane fragmenty (znaczniki OD / DO), z zachowaniem dźwięku przez FFmpeg.
- Eksportuje zdjęcie z zamazanymi twarzami.
- Robi zrzut bieżącej klatki z zamazaniem albo bez.
- Zapamiętuje trzy ustawienia: siłę rozmycia, wtapianie i tolerancję skoku.

---

## Wymagania i instalacja

### Wymagania

- Windows (program korzysta z ustawień DPI Windows i z `ffmpeg.exe`).
- Python 3.x, jeśli uruchamiasz z kodu źródłowego.
- Biblioteki: `opencv-python`, `numpy`, `Pillow`. `tkinter` jest częścią standardowej instalacji Pythona na Windows.
- `ffmpeg.exe` w katalogu programu - potrzebny do zachowania dźwięku przy eksporcie wideo. Bez niego eksport wideo nadal powstanie, ale bez dźwięku.

### Uruchomienie z kodu

```bash
pip install opencv-python numpy Pillow
python face_blur_v37_multi_model.py
```

### Uruchomienie jako program .exe

Jeśli masz wersję spakowaną, po prostu uruchom plik `.exe`. Pliki modeli `.onnx` i `ffmpeg.exe` muszą leżeć obok pliku `.exe` albo w tym samym katalogu co skrypt.

---

## Pliki modeli AI i FFmpeg

Program szuka modelu w tej kolejności: katalog wewnętrzny, katalog programu, katalog skryptu, bieżący katalog roboczy, `~/.face_detector`.

### YuNet (domyślny)

Szuka po kolei:

- `face_detection_yunet_2026may.onnx`
- `face_detection_yunet_2023mar.onnx`
- `yunet.onnx`

### SCRFD 500M

- `scrfd_500m_bnkps.onnx`
- `scrfd_500m.onnx`
- `500m.onnx`
- `det_500m.onnx`

### SCRFD 2.5G

- `scrfd_2.5g_bnkps.onnx`
- `scrfd_2.5g.onnx`
- `2.5g_bnkps.onnx`
- `det_2.5g.onnx`

### YOLO-Face (Nano)

- `yolov8n-face.onnx`
- `yolov8n_face.onnx`
- `yolo-face.onnx`
- `model.onnx`

### Gdy nie ma pliku modelu

Program pyta, czy chcesz wskazać plik ręcznie. Jeśli odmówisz albo plik się nie wczyta, przechodzi na Haar Cascade (`haarcascade_frontalface_default.xml`). Haar jest wyraźnie słabszy, szczególnie przy twarzach z profilu, z boku, w ruchu i przy słabym świetle. To częste źródło fałszywych detekcji.

### FFmpeg

Przy pierwszym uruchomieniu, jeśli nie ma `ffmpeg.exe`, program pokazuje okno z linkami do pobrania paczki FFmpeg. Wypakuj z niej tylko `ffmpeg.exe` do katalogu programu. Możesz też mieć FFmpeg w zmiennej PATH, program go wtedy znajdzie.

---

## Szybki start

### Wideo, jedna twarz ręcznie

1. Kliknij **Wybierz wideo** i wczytaj plik.
2. Zatrzymaj się na klatce, gdzie twarz jest dobrze widoczna.
3. Kliknij raz lewym przyciskiem myszy na twarz. Pojawi się zamazanie. Zielona ramka to tylko propozycja detektora. Zamazanie pojawia się dopiero po kliknięciu.
4. Odtwórz film przyciskiem **PLAY** i sprawdź, czy zamazanie trzyma się twarzy.
5. Jeśli trzeba, popraw ramkę, utnij śledzenie albo usuń błędny segment.
6. Kliknij **Eksportuj / Zapisz**.

### Wideo, wszystkie twarze automatycznie

1. Kliknij **Wybierz wideo**.
2. Kliknij **Zamaż cały film (AI)** i potwierdź.
3. Poczekaj na koniec skanowania.
4. Przejrzyj cały film klatka po klatce lub na podglądzie. Usuń błędne zamazania, utnij śledzenie tam, gdzie twarz się odwróciła.
5. Dopiero wtedy kliknij **Eksportuj / Zapisz**.

### Zdjęcie

1. Kliknij **Wybierz zdjęcie**.
2. Kliknij twarz albo narysuj ramkę przeciągnięciem.
3. Kliknij **Eksportuj / Zapisz** i wybierz miejsce zapisu.

---

## Układ okna

Od góry do dołu:

1. **Górny pasek 1:** import, wybór modelu, automatyczne zamazywanie, motyw, zrzut, eksport.
2. **Górny pasek 2:** pauza / wznowienie śledzenia, zoom, cofanie, czyszczenie.
3. **Środek:** duży podgląd wideo albo zdjęcia, po prawej lista twarzy i segmentów.
4. **Pod podglądem:** nazwa wczytanego pliku.
5. **Pasek odtwarzania:** czas, PLAY, skoki w tył i w przód.
6. **Oś czasu:** suwak klatek, skok na początek / koniec, numer klatki, znaczniki eksportu.
7. **Pasek parametrów:** suwaki rozmycia, wtapiania, tolerancji skoku, prędkości oraz kształt zamazania.
8. **Na samym dole:** pasek statusu z informacją o bieżącej klatce, zoomie, liczbie aktywnych twarzy i trybie.

Po prawej stronie, nad listą, jest zawsze widoczny panel **DETEKTOR AI**. Pokazuje nazwę aktywnego modelu i nazwę pliku modelu, np. `YuNet (face_detection_yunet_2023mar.onnx)`. Jeśli model nie został znaleziony, zobaczysz tam `Haar Cascade` albo `brak pliku`.

---

## Górny pasek 1 - import, model, detekcja, eksport

### Wybierz wideo

Wczytuje wideo. Obsługiwane: `.mp4`, `.mov`, `.avi`, `.mkv`, `.wmv`.

Co robi po wczytaniu:

- Czyści poprzednie trackery, znaczniki eksportu i cache klatek.
- Ustawia liczbę klatek, FPS i rozmiar obrazu.
- Odblokowuje przyciski odtwarzania, skoków, pauzy, eksportu i znaczników.
- Ustawia prędkość odtwarzania na FPS filmu.
- Pokazuje klatkę 0 i wykrywa twarze na niej.

Wczytanie nowego pliku usuwa wszystkie wcześniejsze zamazania z poprzedniego pliku. Jeśli chcesz zachować pracę, najpierw wyeksportuj.

### Wybierz zdjęcie

Wczytuje zdjęcie: `.jpg`, `.jpeg`, `.png`, `.bmp`, `.webp`.

W trybie zdjęcia program blokuje: odtwarzanie, suwak czasu, skoki, pauzę / wznowienie śledzenia, znaczniki eksportu i skanowanie całego filmu. Zdjęcie to zawsze jedna klatka, numer 0. Zamazanie działa tak samo jak na wideo, ale bez śledzenia w czasie.

### GitHub: marek7400

Link tekstowy. Kliknięcie otwiera w przeglądarce stronę repozytoriów autora: `https://github.com/marek7400?tab=repositories`. Nie zmienia nic w programie.

### Model: - lista rozwijana

Wybór modelu detekcji twarzy. Domyślnie: **YuNet**.

- **YuNet** - domyślny, dobry kompromis między szybkością a dokładnością. Dobry do większości nagrań z przodu i lekkiego profilu.
- **SCRFD 500M** - lżejszy, szybszy. Może gorzej łapać małe i odległe twarze.
- **SCRFD 2.5G** - cięższy, dokładniejszy. Lepszy przy małych, częściowo zasłoniętych albo odległych twarzach. Wolniejszy.
- **YOLO-Face (Nano)** - mały model YOLO. Dobry, gdy YuNet gubi twarze w ruchu.

Zmiana modelu przeładowuje detektor i odświeża bieżącą klatkę z nową detekcją. Nie usuwa już dodanych trackerów. Jeśli program nie znajdzie pliku `.onnx` dla wybranego modelu, pyta o ręczne wskazanie pliku. Jeśli odmówisz, wraca do Haar Cascade.

Praktyka: jeśli detektor znajduje twarze w losowych miejscach (np. na ścianie, ubraniu, dłoni), najpierw zmień model na inny i sprawdź tę samą klatkę. Często to nie jest wina śledzenia, tylko detekcji.

### Wskaż...

Otwiera okno wyboru pliku `.onnx` dla modelu wybranego na liście **Model:**. Używaj tego, gdy plik modelu ma inną nazwę albo leży w innym miejscu niż katalog programu. Po wskazaniu pliku program przeładowuje model i odświeża bieżącą klatkę.

### Zamaż wszystkie zielone na bieżącej klatce

Pomarańczowy przycisk. Zamienia wszystkie **zielone ramki** na bieżącej klatce w aktywne zamazania.

Ważne:

- Zielona ramka to tylko to, co detektor widzi w tej chwili. Nie jest jeszcze zamazaniem.
- Ten przycisk dodaje tracker dla każdej zielonej ramki na tej klatce.
- Jeśli detektor nie widzi żadnej twarzy, program pokazuje ostrzeżenie: „Detektor nie wykrył zielonych ramek twarzy na bieżącej klatce."
- Jeśli na klatce jest fałszywa zielona ramka (np. na plakacie, telewizorze, odbiciu w lustrze), ten przycisk doda też dla niej zamazanie. Potem musisz ją usunąć ręcznie. Dlatego ten przycisk jest dobry, gdy na klatce są same prawdziwe twarze.

### Zamaż cały film (AI)

Różowy przycisk z piorunem. Skanuje cały film od pierwszej do ostatniej klatki, wykrywa twarze i tworzy dla nich segmenty zamazania.

Jak działa:

- Pyta o potwierdzenie: „Czy przeskanować cały film (X klatek) modelem AI i automatycznie zamazać wszystkie wykryte twarze?"
- Pokazuje okno postępu z procentami i licznikiem klatek oraz przyciskiem **Anuluj**.
- Dla każdej klatki wykrywa twarze i łączy je w trajektorie po odległości środków i nakładaniu się ramek (IoU).
- Jeśli twarz znika na 6 klatek z rzędu, kończy jej segment.
- Na końcu odrzuca segmenty krótsze niż 3 klatki.
- Dodaje wszystkie pozostałe segmenty do listy po prawej.

To jest tryb „najpierw automat, potem ręczna korekta". Nigdy nie eksportuj od razu po tym skanowaniu bez przejrzenia filmu. Skanowanie AI tworzy najwięcej błędów z dwóch typów: fałszywe twarze w losowych miejscach oraz śledzenie odwróconej głowy. Oba przypadki są opisane szczegółowo w dalszej części README.

W trybie zdjęcia ten przycisk działa jak „Zamaż wszystkie zielone na bieżącej klatce".

### Przycisk motywu: 🌙 / ☀️

Przełącza jasny i ciemny motyw interfejsu. Nie zmienia wyglądu wideo ani zamazania. Zmienia tylko kolory okien, przycisków, suwaków i list. Przydaje się przy długiej pracy, żeby mniej męczyć oczy.

### 📸 Zrób zrzut ekranu

Zapisuje bieżącą klatkę do pliku `.jpg` albo `.png`. Otwiera okno wyboru miejsca zapisu. Domyślna nazwa: `zrzut_klatka_000123_blur.jpg` albo `..._czysta.jpg`, zależnie od checkboxa **z blur**.

Zrzut bierze klatkę źródłową i nakłada na nią bieżące zamazania z bieżącymi ustawieniami suwaków. Nie bierze podglądu z zielonymi ramkami ani z pomocniczymi liniami. To jest czysty obraz.

### Checkbox: z blur

Znajduje się tuż obok przycisku **Zrób zrzut ekranu**. Domyślnie zaznaczony.

- **Zaznaczony:** zrzut zapisuje klatkę z zamazaniami.
- **Odznaczony:** zrzut zapisuje czystą klatkę źródłową, bez zamazań.

Używaj odznaczonego wariantu, gdy chcesz zachować dowód, jak wyglądała oryginalna klatka, albo gdy chcesz porównać przed / po.

### 💾 Eksportuj / Zapisz

Niebieski przycisk po prawej stronie górnego paska. Główny przycisk zapisu.

- Jeśli nie ma wczytanego pliku albo nie ma żadnego trackera, pokazuje ostrzeżenie: „Wybierz plik i zaznacz przynajmniej jedną twarz!"
- W trybie zdjęcia: otwiera okno zapisu zdjęcia i zapisuje zdjęcie z zamazaniami.
- W trybie wideo: otwiera okno zapisu `.mp4` i eksportuje wideo.

Szczegóły eksportu są w rozdziale [Eksport](#eksport).

---

## Górny pasek 2 - śledzenie, zoom, edycja

### ⏸ PAUSE

Wstrzymuje śledzenie wszystkich aktywnych twarzy na bieżącej klatce.

Dokładnie: dla każdego trackera, który jest aktywny na bieżącej klatce, ustawia `end_frame = bieżąca klatka - 1`. Od bieżącej klatki w przód zamazanie znika. Wcześniejsze klatki zostają zamazane bez zmian.

Wskaźnik obok przycisków zmienia się na **PAUZA ŚLEDZENIA** na czerwono. Na podglądzie może pojawić się napis „PAUZA SLEDZENIA - BRAK ROZMYCIA", gdy nie ma już aktywnych trackerów.

PAUSE nie usuwa trackerów. To jest ucięcie, nie usunięcie. To jest podstawowe narzędzie do obsługi odwróconej twarzy.

Uwaga: PAUSE tnie **wszystkie** aktywne twarze naraz. Jeśli masz kilka twarzy i tylko jedna się odwróciła, nie używaj PAUSE. Użyj metody precyzyjnej z rozdziału o odwróconej twarzy (podział przez przeciągnięcie + usunięcie ogona).

Jeśli nie ma trackerów, PAUSE nic nie robi.

### ▶ RESUME

Wznawia śledzenie po pauzie.

Dla każdej twarzy, której segment się skończył (ma ustawiony `end_frame`), tworzy nowy segment od bieżącej klatki. Nowy segment startuje z ostatniego znanego położenia twarzy (`last_good_bbox` albo `init_bbox`) i z tym samym kształtem co poprzedni (np. wielokąt LINE).

W praktyce: stajesz na klatce, gdzie twarz znowu jest widoczna z przodu, klikasz RESUME i śledzenie rusza dalej. Wcześniejsza przerwa zostaje niezamazana.

Jeśli żaden tracker nie jest zakończony, RESUME nic nie robi.

PAUSE i RESUME razem tworzą przerwę w zamazywaniu. To jest normalny sposób pracy: pauza, gdy twarz znika albo się odwraca, wznowienie, gdy wraca.

### Wskaźnik: ● ŚLEDZENIE AKTYWNE / ⏸ PAUZA ŚLEDZENIA

Tylko informacja, nie przycisk.

- **● ŚLEDZENIE AKTYWNE** (zielony): śledzenie działa normalnie.
- **⏸ PAUZA ŚLEDZENIA** (czerwony): ostatnia operacja PAUSE ucięła aktywne segmenty. Nowe klatki nie będą zamazywane, dopóki nie klikniesz RESUME albo nie dodasz twarzy od nowa.

### Checkbox: Stop gdy zgubi

Domyślnie zaznaczony. Bardzo ważny dla jakości.

- **Zaznaczony:** gdy tracker straci twarz (skok za duży, ramka poza ekranem, brak dopasowania), program po 2-3 klatkach oznacza tracker jako `failed` i kończy jego segment. Zamazanie nie „wędruje" dalej po ekranie.
- **Odznaczony:** tracker trzyma się ostatniej znanej pozycji i dalej zamazuje to samo miejsce, nawet jeśli twarzy już tam nie ma. To jest częsta przyczyna „zamazanych obszarów w losowych miejscach", które wiszą w powietrzu.

Zalecenie: trzymaj **Stop gdy zgubi** zaznaczone prawie zawsze. Odznaczaj tylko wtedy, gdy świadomie chcesz, żeby zamazanie zostało w miejscu, w którym twarz na chwilę zniknęła za przeszkodą i zaraz wróci, a nie chcesz, żeby tracker się wyłączył.

Przy eksporcie ten checkbox też działa: jeśli tracker zgubi twarz, eksport przestaje go rysować po kilku klatkach, zamiast trzymać zamazanie w miejscu.

### 🔍 ZOOM 1:1

Resetuje zoom i wyśrodkowuje widok. Ustawia zoom na 1.0x i wraca do pełnej klatki. Używaj, gdy zgubiłeś się w powiększeniu albo chcesz zobaczyć całą klatkę.

### Cofnij

Usuwa ostatni dodany tracker z listy. Nie cofa innych operacji, np. zmiany rozmycia czy znaczników.

Dokładnie: usuwa ostatni element z listy trackerów. Jeśli usunięty tracker był kolejnym segmentem tej samej twarzy, program przywraca poprzedniemu segmentowi tej twarzy brak końca (`end_frame = None`), czyli „odcina" cofnięcie podziału.

Przykład: dodałeś twarz przez pomyłkę na bieżącej klatce. Klikasz **Cofnij** i znika tylko ten ostatni tracker. Wcześniejsze twarze zostają.

Skrót klawiatury: **Delete** robi to samo.

### Wyczyść

Usuwa **wszystkie** trackery, wszystkie wielokąty LINE i resetuje liczniki. Czyści całe zamazywanie z bieżącego pliku. Nie usuwa znaczników eksportu ani nie zamyka pliku. Nie pyta o potwierdzenie.

Używaj ostrożnie. Jeśli chcesz usunąć tylko jedną błędną twarz, użyj przycisku **X** przy niej na liście albo prawego przycisku myszy na niej. **Wyczyść** jest do sytuacji „zaczynam od zera".

---

## Podgląd wideo - myszka i rysowanie

Podgląd to główne miejsce pracy. Kolory mają znaczenie:

- **Zielona ramka** - to, co detektor AI widzi teraz. To nie jest jeszcze zamazanie. To tylko propozycja. Kliknięcie w nią zamienia ją w zamazanie.
- **Niebiesko-zielona ramka / elipsa** - tracker, nad którym trzymasz kursor albo który przeciągasz. Pokazuje środek (kółko z krzyżykiem) i narożniki (małe kwadraty). To jest ramka, którą możesz przesunąć albo zmienić jej rozmiar.
- **Pomarańczowo-niebieskie punkty i linie** - rysowany wielokąt w trybie LINE.

### Klik lewym przyciskiem - AUTO

Jedno kliknięcie lewym przyciskiem na twarzy dodaje tracker w trybie AUTO.

Program szuka w bieżącej detekcji ramki, która zawiera kliknięty punkt (z marginesem 15 pikseli). Jeśli znajdzie, tworzy tracker dokładnie na tej wykrytej ramce. Jeśli nie znajdzie, pokazuje w panelu informacyjnym „Klik (x,y) poza ramką."

Używaj AUTO, gdy detektor dobrze widzi twarz. To jest najszybsze i najdokładniejsze.

### Przeciągnięcie lewym przyciskiem - MANUAL

Przytrzymaj lewy przycisk i przeciągnij, żeby narysować prostokąt. Po puszczeniu program tworzy tracker w trybie MANUAL na narysowanym obszarze.

Używaj MANUAL, gdy:

- Detektor nie widzi twarzy (profil, tył głowy, maska, słabe światło, mała twarz).
- Chcesz zamazać coś, co nie jest twarzą (np. tablica rejestracyjna, dokument, tatuaż, ekran telefonu).
- Zielona ramka jest za mała, za duża albo przesunięta.

Ramka MANUAL musi mieć co najmniej 8x8 pikseli. Mniejsza nie zostanie dodana.

### Środek ramki - przesuwanie

Najedź kursorem na środek istniejącej ramki. Kursor zmieni się w strzałki (fleur). Przytrzymaj lewy przycisk i przeciągnij, żeby przesunąć ramkę.

Przesunięcie w środku filmu ma ważną konsekwencję: program **dzieli tracker**. Stary segment kończy się na klatce przed bieżącą (`end_frame = bieżąca - 1`), a od bieżącej klatki zaczyna się nowy segment z poprawioną ramką. Dzięki temu poprawka nie psuje wcześniejszych klatek, które były dobre.

Jeśli przesuwasz ramkę na tej samej klatce, na której tracker się zaczął, program po prostu aktualizuje ten tracker, bez dzielenia.

### Krawędź albo narożnik ramki - zmiana rozmiaru

Najedź na krawędź albo narożnik ramki. Kursor zmieni się w podwójną strzałkę. Przytrzymaj lewy przycisk i przeciągnij.

- Krawędź lewa / prawa: zmiana szerokości.
- Krawędź góra / dół: zmiana wysokości.
- Narożnik: zmiana szerokości i wysokości naraz.

Tak samo jak przy przesuwaniu, zmiana rozmiaru w środku filmu dzieli tracker na dwa segmenty: stary kończy się przed bieżącą klatką, nowy zaczyna się od bieżącej.

### Scroll myszki - Zoom

Kręć kółkiem myszki, żeby powiększyć albo pomniejszyć widok. Zoom działa od 1.0x do 12.0x, krok 1.25x. Zoom wyśrodkowuje się na miejscu kursora.

Zoom nie zmienia wideo ani zamazania. Zmienia tylko to, co widzisz. Przydaje się do precyzyjnego ustawienia ramki na małej twarzy albo do sprawdzenia, czy zamazanie dokładnie kryje oczy i usta.

### Prawy przycisk myszki - dwie funkcje

Zachowanie zależy od zoomu:

- **Zoom = 1.0x, klik prawym na ramkę:** usuwa tracker, który jest pod kursorem. To jest najszybszy sposób usunięcia jednej błędnej twarzy bez szukania jej na liście. Program usuwa cały segment tego trackera.
- **Zoom > 1.0x, przeciągnięcie prawym:** przesuwanie widoku (Pan). Przytrzymaj prawy przycisk i przeciągnij, żeby przesunąć powiększony obraz.
- **Zoom > 1.0x, klik prawym bez przeciągnięcia:** usuwa tracker pod kursorem, tak samo jak przy zoomie 1.0x.
- **Tryb LINE i są narysowane punkty:** prawy przycisk kasuje rysowany wielokąt i zaczyna od nowa.

Uwaga: prawy przycisk usuwa cały segment, nie tylko od bieżącej klatki. Jeśli chcesz uciąć śledzenie od bieżącej klatki, a nie usunąć cały segment, użyj PAUSE albo metody podziału z rozdziału o odwróconej twarzy.

### Shift + lewy przycisk - przesuwanie widoku (Pan)

Przytrzymaj **Shift**, przytrzymaj lewy przycisk i przeciągnij. Przesuwa powiększony widok bez rysowania ramki i bez przesuwania trackerów. Przydaje się, gdy zoom jest duży i chcesz obejrzeć inny fragment klatki.

### Podwójne kliknięcie lewym - zamykanie wielokąta LINE

W trybie LINE podwójne kliknięcie zamyka wielokąt i tworzy tracker. Szczegóły w rozdziale o LINE.

---

## Panel prawy - lista twarzy i segmentów

Po prawej stronie, pod panelem DETEKTOR AI, jest sekcja **Segmenty i Oś Czasu**.

Każdy wiersz to jeden segment śledzenia. Przykład napisu:

`Twarz 2 [ID5] (A) [120-...]`

Co oznacza:

- **Twarz 2** - numer twarzy (`face_id`). Ta sama osoba, która znika i wraca, może mieć kilka segmentów, ale ten sam numer twarzy, jeśli program połączył je jako tę samą twarz.
- **[ID5]** - unikalny numer segmentu (`id`). Dwa segmenty tej samej twarzy mają różne ID.
- **(A)** - typ dodania: `A` = AUTO, `M` = MANUAL, `L` = LINE (wielokąt).
- **[120-...]** - zakres klatek. `...` oznacza, że segment nie ma końca i trwa dalej. Liczba po myślniku to klatka końcowa, np. `[120-350]`.

Kolor wiersza:

- **Zielonkawy** - segment aktywny na bieżącej klatce.
- **Szary** - segment nieaktywny na bieżącej klatce (skończył się wcześniej albo zaczyna się później).

### Przycisk IDŹ

Zielony przycisk przy każdym segmencie. Przenosi podgląd na klatkę startową tego segmentu. Używaj, gdy chcesz szybko znaleźć, gdzie dany błędny segment się zaczął.

### Przycisk X

Czerwony przycisk przy każdym segmencie. Usuwa tylko ten segment. Nie usuwa innych segmentów tej samej twarzy.

Uwaga: jeśli usuniesz ostatni segment twarzy, a wcześniejszy segment miał ustawiony koniec, program przywraca mu brak końca (`end_frame = None`). Czyli usunięcie ogona „odblokowuje" wcześniejszy segment.

To jest kluczowe narzędzie do czyszczenia błędów: znajdujesz błędny segment na liście, klikasz **X**, a reszta zostaje.

---

## Sterowanie odtwarzaniem

### Licznik czasu

Pokazuje dokładny czas bieżącej klatki w formacie `GG:MM:SS.mmm`, np. `00:01:23.456`. To jest czas liczony z numeru klatki i FPS filmu.

### ▶ PLAY / ⏹ STOP

- **PLAY:** odtwarza wideo od bieżącej klatki z prędkością z suwaka prędkości. Podczas odtwarzania program nie robi detekcji AI na każdej klatce, tylko rysuje już dodane zamazania. Dzięki temu odtwarzanie jest płynne.
- **STOP:** zatrzymuje odtwarzanie i odświeża bieżącą klatkę z pełną detekcją.

Jeśli jesteś na ostatniej klatce i klikniesz PLAY, program wraca na klatkę 0.

W trybie zdjęcia PLAY jest zablokowany.

### Skoki w tył: -30, -20, -10, -5, -2, -1

Przenoszą o podaną liczbę klatek w tył. Używaj do precyzyjnego cofania się do momentu, gdzie twarz się odwróciła albo gdzie pojawiło się błędne zamazanie.

### Skoki w przód: +1, +2, +5, +10, +20, +30

Przenoszą o podaną liczbę klatek w przód. `+1` i `-1` to klatka po klatce, najlepszy tryb do dokładnego cięcia.

---

## Oś czasu i znaczniki eksportu

### Przycisk |<<

Skok na pierwszą klatkę (klatka 0). Zatrzymuje odtwarzanie.

### Suwak osi czasu

Duży suwak pod podglądem. Przeciąganie przenosi po filmie. Po puszczeniu program odświeża klatkę z pełną detekcją.

Nad suwakiem jest cienki pasek znaczników:

- **Czerwona kreska** - bieżąca klatka.
- **Zielony prostokąt** - wybrany fragment do eksportu (znacznik OD / DO).
- **Żółta kreska** - tymczasowy znacznik OD, gdy kliknąłeś OD, ale jeszcze nie kliknąłeś DO.

Kliknięcie w pasek znaczników też przenosi do tej klatki.

### Przycisk >>|

Skok na ostatnią klatkę. Zatrzymuje odtwarzanie.

### Pole numeru klatki i / total

Pole tekstowe z numerem bieżącej klatki. Wpisz numer i naciśnij **Enter**, żeby skoczyć do tej klatki. Obok jest napis `/ 1234`, czyli numer ostatniej klatki. W trybie zdjęcia pokazuje `/ 0 (Zdjęcie)`.

### Eksport OD

Ustawia początek fragmentu do eksportu na bieżącej klatce. Przycisk zmienia kolor na pomarańczowy, a na pasku znaczników pojawia się żółta kreska. Status na dole pokazuje: „Ustawiono znacznik OD na klatce X. Przejdź do klatki końcowej i kliknij 'Eksport DO'."

Sam przycisk OD jeszcze nic nie zapisuje. To tylko znacznik.

### Eksport DO

Zamyka fragment. Bierze wcześniejszy znacznik OD i bieżącą klatkę jako koniec. Dodaje fragment `[OD - DO]` do listy. Jeśli klikniesz DO bez wcześniejszego OD, program przyjmuje OD = 0.

Możesz dodać wiele fragmentów. Program sortuje je i scala nakładające się fragmenty w jeden. Przy eksporcie wyeksportuje tylko te fragmenty, połączone w jeden plik.

Przycisk OD wraca do koloru zielono-niebieskiego po dodaniu fragmentu.

### Kasuj znaczniki

Usuwa fragment eksportu, w którym znajduje się bieżąca klatka. Przycisk jest aktywny tylko wtedy, gdy bieżąca klatka jest wewnątrz jakiegoś zaznaczonego fragmentu. Jeśli nie jesteś w żadnym fragmencie, przycisk jest wyszarzony i nic nie robi.

Żeby usunąć fragment, stań na dowolnej klatce wewnątrz tego fragmentu i kliknij **Kasuj znaczniki**.

---

## Dolny pasek - suwaki i kształt zamazania

### Suwak: Rozmycie

Zakres: 0 do 100. Domyślnie: 50. Zapisywany w `settings.json`.

Co robi:

- W trybie Gaussa: im większa wartość, tym większe jądro rozmycia. W kodzie: `k = 3 + (wartość / 100) * 148`, zaokrąglone do nieparzystej. Przy 0 rozmycie jest minimalne, przy 100 bardzo mocne.
- W trybie PIXEL: im większa wartość, tym większe bloki pikseli. `block_size = 2 + (wartość / 100) * 38`.
- W trybie BLACKBOX: suwak nie ma znaczenia, bo zamazanie to czarny kolor.

Zmiana suwaka od razu odświeża bieżącą klatkę. Przy eksporcie program bierze wartość z momentu eksportu.

Praktyka: twarz jest naprawdę zamazana dopiero wtedy, gdy nie da się rozpoznać oczu, nosa, ust i kształtu. Jeśli przy 50 widać rysy, podnieś do 70-90. Jeśli zamazanie „wylewa" się poza twarz i wygląda nienaturalnie, zmniejsz wtapianie, a nie rozmycie.

### Suwak: Wtapianie

Zakres: 0 do 100. Domyślnie: 15. Zapisywany w `settings.json`.

To jest feather, czyli miękkie przejście na krawędzi zamazania.

- **0:** ostra granica. Widać dokładnie, gdzie kończy się zamazanie.
- **15 (domyślne):** lekkie zmiękczenie. Dobre do większości nagrań.
- **50-100:** bardzo miękkie, rozmyte przejście. Zamazanie „rozpływa" się na tło.

W trybie BLACKBOX wtapianie nie działa, bo czarny prostokąt jest wstawiany bez miękkiego przejścia.

Praktyka: jeśli na eksporcie widać „plamę" z ostrą krawędzią, podnieś wtapianie do 25-35. Jeśli zamazanie zaczyna odsłaniać brzegi twarzy, zmniejsz wtapianie.

### Suwak: Tolerancja skoku

Zakres: 0 do 300. Domyślnie: 130. Zapisywany w `settings.json`.

To jest najważniejszy suwak do walki z losowymi zamazaniami.

Mówi trackerowi, o ile pikseli środek ramki może się przesunąć między klatkami, żeby program uznał to za tę samą twarz.

- **Niska wartość (np. 30-60):** tracker jest „podejrzliwy". Przy szybkim ruchu głowy albo przy skoku kamery szybko uzna, że twarz została zgubiona, i zakończy segment. Mniej fałszywych przeskoków, ale więcej przerwań śledzenia przy szybkim ruchu.
- **Wysoka wartość (np. 200-300):** tracker jest „tolerancyjny". Pozwala na duże skoki. Śledzenie rzadziej się przerywa, ale częściej przeskakuje na inną osobę, na plakat, na rękę albo na tło. To jest przyczyna zamazań w losowych miejscach.
- **130 (domyślne):** kompromis do nagrań z umiarkowanym ruchem.

Jak używać:

- Film statyczny, kamera na statywie, osoba mało się rusza: możesz zejść do 80-100.
- Film z ręki, dużo ruchu, szybkie zwroty głowy: zostaw 130 albo podnieś do 150, ale wtedy częściej sprawdzaj błędy.
- Po zmianie tego suwaka musisz jeszcze raz przejść przez film, bo suwak działa na bieżąco przy śledzeniu, a nie wstecz na już zapisane trajektorie.

Przy skokach o więcej niż 3 klatki (np. przeciągnięcie suwaka czasu) program robi ponowną detekcję AI i szuka twarzy w pobliżu ostatniej pozycji, z limitem zależnym od tego suwaka. Im mniejsza tolerancja, tym mniejszy promień poszukiwań i mniejsza szansa na złapanie obcej twarzy.

### Suwak + pole: Prędkość odtwarzania (kl./s)

Zakres suwaka: 0.5 do 120 klatek na sekundę, krok 0.5. Domyślnie: FPS wczytanego filmu.

To jest tylko prędkość podglądu, nie prędkość eksportu. Eksport zawsze zachowuje oryginalny FPS.

- Przeciągasz suwak albo wpisujesz liczbę w polu obok i naciskasz **Enter**.
- Pole akceptuje też przecinek, np. `25,0`.
- Program zaokrągla do 0.5.

Praktyka:

- Do dokładnej kontroli: 5-10 kl./s. Film idzie wolno, łatwo złapać moment odwrócenia głowy.
- Do szybkiego przeglądu: 50-100 kl./s. Program pomija klatki, żeby nadążyć.
- Do normalnej pracy: zostaw FPS filmu.

W trybie zdjęcia suwak i pole są zablokowane.

### Checkbox: CIRCLE

Domyślnie zaznaczony.

- **Zaznaczony:** zamazanie ma kształt elipsy wpisanej w ramkę. To jest kształt najbliższy twarzy. Mniej zamazuje tła po bokach.
- **Odznaczony:** zamazanie ma kształt prostokąta, całej ramki.

CIRCLE działa tylko wtedy, gdy nie jest włączony LINE. Jeśli rysujesz wielokąt LINE, kształt wielokąta ma pierwszeństwo.

Jeśli odznaczysz CIRCLE i BLACKBOX jest odznaczony, dostajesz prostokątne rozmycie Gaussa albo prostokątną pikselozę.

### Checkbox: BLACKBOX

Domyślnie odznaczony.

- **Zaznaczony:** zamiast rozmycia wstawia czarny obszar. Jeśli CIRCLE jest zaznaczony, to czarna elipsa. Jeśli odznaczony, to czarny prostokąt. Wtapianie nie działa w tym trybie.
- **Odznaczony:** normalne rozmycie albo pikseloza.

BLACKBOX jest najmocniejszy, nic nie prześwituje. Używaj, gdy rozmycie przy 100 nadal pozwala rozpoznać osobę albo gdy chcesz styl „czarny pasek".

### Checkbox: LINE

Domyślnie odznaczony.

Włącza tryb rysowania wielokąta. Po włączeniu:

- Klikasz kolejne punkty na obrazie. Każdy klik dodaje punkt.
- Na ekranie widać linie łączące punkty oraz linię podglądu do kursora.
- Podwójne kliknięcie zamyka wielokąt i tworzy tracker o kształcie tego wielokąta.
- Prawy przycisk myszki kasuje rysowany wielokąt i zaczyna od nowa.
- Przełączenie LINE czyści narysowane punkty.

Wielokąt jest zapisywany jako punkty względne wobec ramki. Przy śledzeniu wielokąt skaluje się razem z ramką. Dzięki temu możesz zamazać np. tylko twarz bez włosów, tylko tablicę, tylko nieregularny kształt.

Jeśli włączysz LINE, a potem klikniesz w twarz bez rysowania wielokąta, nic się nie stanie. Musisz narysować co najmniej 3 punkty i zamknąć podwójnym kliknięciem.

### Checkbox: PIXEL

Domyślnie odznaczony.

- **Zaznaczony:** zamazanie to pikseloza (mozaika). Rozmiar bloków zależy od suwaka **Rozmycie**.
- **Odznaczony:** zamazanie to rozmycie Gaussa.

PIXEL jest czytelniejszy jako „celowe zamazanie" i często wygląda lepiej na nagraniach z internetu, gdzie rozmycie Gaussa można częściowo odtworzyć. Jeśli chcesz maksymalnej anonimowości, PIXEL z wysokim rozmyciem albo BLACKBOX są lepsze niż słabe rozmycie Gaussa.

---

## Tryby dodawania twarzy: AUTO, MANUAL, LINE

| Tryb | Jak dodać | Kiedy używać |
| --- | --- | --- |
| AUTO | Jedno kliknięcie na zielonej ramce | Detektor dobrze widzi twarz |
| MANUAL | Przeciągnięcie prostokąta | Detektor nie widzi twarzy, albo chcesz zamazać coś innego niż twarz |
| LINE | Klikasz punkty, 2x klik zamyka | Nieregularny kształt, chcesz ominąć włosy, rękę, tło |

Wszystkie trzy tryby tworzą ten sam rodzaj trackera. Różnią się tylko tym, jak powstaje początkowa ramka i kształt.

Typ jest widoczny na liście jako litera: `A`, `M`, `L`.

---

## Jak działa śledzenie

To jest ważne, żeby rozumieć błędy.

1. **Detekcja:** model AI znajduje twarze na bieżącej klatce i rysuje zielone ramki.
2. **Dodanie trackera:** klikasz twarz albo rysujesz ramkę. Program tworzy tracker CSRT dla tej ramki.
3. **Śledzenie klatka po klatce:** CSRT podąża za wzorcem obrazu z ramki. Nie rozpoznaje tożsamości. Nie wie, czy to ta sama osoba. On tylko podąża za tym, co wygląda podobnie w pobliżu.
4. **Ponowne zakotwiczenie AI (re-anchoring):** gdy przeskoczysz o więcej niż 3 klatki, program nie liczy CSRT klatka po klatce. Zamiast tego robi nową detekcję AI na docelowej klatce i szuka twarzy najbliżej ostatniej pozycji, w limicie zależnym od Tolerancji skoku. Jeśli znajdzie, przeskakuje na nią. Jeśli nie znajdzie, oznacza stratę.
5. **Zgubienie:** jeśli ramka skoczy za daleko, zmieni rozmiar za bardzo (ponad 2.8x) albo wyjdzie poza ekran, program liczy to jako zgubienie. Przy włączonym **Stop gdy zgubi** po 2-3 takich klatkach kończy segment.
6. **Trajektoria:** program zapisuje pozycję ramki dla każdej klatki w segmencie w słowniku `traj`. Dzięki temu cofanie się po osi czasu nie liczy śledzenia od nowa.

Wniosek: tracker nie wie, że twarz się odwróciła. Dla CSRT tył głowy, włosy i kark to nadal „ten sam wzorzec". Dlatego musisz ręcznie ucinać śledzenie, gdy twarz się odwraca. Program sam tego nie zrobi.

---

## Eksport

### Eksport wideo

1. Kliknij **Eksportuj / Zapisz**.
2. Wybierz miejsce i nazwę pliku, np. `zamazane.mp4`.
3. Pojawi się okno postępu z procentami i licznikiem klatek.
4. Program przechodzi przez cały film klatka po klatce, od nowa liczy trackery i nakłada zamazania z bieżącymi ustawieniami suwaków.
5. Na końcu, jeśli jest `ffmpeg.exe`, program dokleja dźwięk z oryginalnego pliku.

Ważne:

- Eksport używa ustawień z momentu kliknięcia eksportu. Jeśli zmienisz suwaki po kliknięciu, eksport tego nie uwzględni.
- Eksport liczy śledzenie od nowa, z bieżącymi ustawieniami **Tolerancja skoku** i **Stop gdy zgubi**. Jeśli w podglądzie było dobrze, a na eksporcie jest błąd, sprawdź, czy nie zmieniłeś tych ustawień między podglądem a eksportem.
- Jeśli masz znaczniki OD / DO, program wyeksportuje tylko te fragmenty, połączone w jeden plik. Dźwięk dla każdego fragmentu jest wycinany i łączony osobno.
- Jeśli nie masz znaczników, program eksportuje cały film.
- Jeśli eksport się nie uda z dźwiękiem, program próbuje zapisać wideo bez dźwięku albo przepisać dźwięk do AAC 192k.

### Eksport zdjęcia

1. W trybie zdjęcia kliknij **Eksportuj / Zapisz**.
2. Wybierz miejsce zapisu. Domyślna nazwa: `zamazane_nazwa_pliku.jpg`.
3. Program zapisuje zdjęcie z bieżącymi zamazaniami.

### Jakość i FPS eksportu

Eksport zachowuje oryginalny rozmiar i FPS filmu. Kodek wideo to `mp4v`. Dźwięk, jeśli jest FFmpeg, to AAC 192k albo kopia oryginalnej ścieżki, zależnie od trybu.

---

## Zrzut ekranu

Opisany w górnym pasku. Dodatkowa uwaga: zrzut używa bieżących trackerów i bieżących ustawień. Jeśli na bieżącej klatce tracker jest w trakcie rysowania albo masz włączony zoom, zrzut i tak zapisuje pełną klatkę źródłową, bez zoomu i bez pomocniczych ramek.

---

## Ustawienia zapisywane automatycznie

Przy zamykaniu programu zapisywany jest plik `settings.json` w katalogu programu. Zawiera tylko trzy wartości:

```json
{
  "blur_strength": 50,
  "feathering": 15,
  "jump_tolerance": 130
}
```

Przy następnym uruchomieniu program wczytuje te wartości i ustawia suwaki. Pozostałe ustawienia (model, kształt, prędkość, checkboxy) nie są zapisywane i wracają do domyślnych.

---

## Skróty klawiszowe

| Klawisz | Działanie |
| --- | --- |
| Strzałka w lewo | Klatka -1 |
| Strzałka w prawo | Klatka +1 |
| Shift + strzałka w lewo | Klatka -10 |
| Shift + strzałka w prawo | Klatka +10 |
| Strzałka w górę | Klatka +30 |
| Strzałka w dół | Klatka -30 |
| Delete | Cofnij - usuwa ostatni tracker |

Myszka:

| Akcja | Działanie |
| --- | --- |
| Lewy klik | AUTO - dodaj twarz z detekcji |
| Lewy przeciągnij | MANUAL - narysuj ramkę |
| Lewy na środku ramki + przeciągnij | Przesuń ramkę |
| Lewy na krawędzi / narożniku + przeciągnij | Zmień rozmiar ramki |
| Scroll | Zoom 1.0x - 12.0x |
| Prawy klik na ramce | Usuń ten tracker |
| Prawy przeciągnij przy zoomie | Pan - przesuń widok |
| Shift + lewy przeciągnij | Pan - przesuń widok |
| Podwójny lewy klik w trybie LINE | Zamknij wielokąt |
| Prawy klik w trybie LINE | Anuluj rysowany wielokąt |

---

## WAŻNE: Niepotrzebne zamazane obszary w losowych miejscach

To jest najczęstszy problem po automatycznym skanowaniu całego filmu. Objaw: na filmie pojawia się zamazana plama na ścianie, na ubraniu, na dłoni, na plakacie, na telewizorze, na odbiciu w lustrze, na drugiej osobie, która nie miała być zamazana, albo plama „wisi" w miejscu, gdzie twarz już dawno zniknęła.

### Dlaczego tak się dzieje

Masz do czynienia z jedną z czterech przyczyn. Diagnoza decyduje o naprawie.

**Przyczyna 1: Fałszywa detekcja AI.**
Model uznał, że coś jest twarzą, choć nią nie jest. Plakat, zdjęcie na ścianie, manekin, pies, wzór na koszulce, dłoń, cień. Jeśli kliknąłeś „Zamaż wszystkie zielone" albo „Zamaż cały film (AI)", program dodał tracker dla tej fałszywej detekcji.

Jak poznać: na klatce startowej segmentu widać zieloną ramkę w miejscu, gdzie nie ma prawdziwej twarzy.

**Przyczyna 2: Przeskok trackera na inny obiekt.**
CSRT podążał za twarzą, ale twarz szybko się ruszyła, kamera drgnęła albo ktoś przeszedł przed kamerą. Tracker „przeskoczył" na podobny wzorzec obok: inną twarz, rękę, fragment ubrania. Od tego momentu zamazuje już nie to, co trzeba.

Jak poznać: początek segmentu jest dobry, twarz jest zamazana prawidłowo, a w pewnym momencie zamazanie nagle „skacze" w inne miejsce i tam zostaje.

**Przyczyna 3: Tracker nie został zatrzymany, gdy twarz zniknęła.**
Jeśli **Stop gdy zgubi** jest odznaczony albo Tolerancja skoku jest za wysoka, tracker trzyma się ostatniej pozycji i dalej zamazuje to samo miejsce, nawet gdy osoba wyszła z kadru. Zamazanie wisi w powietrzu.

Jak poznać: zamazanie stoi w miejscu, a osoba już wyszła albo schowała się za kimś.

**Przyczyna 4: Zła trajektoria po skoku na osi czasu.**
Gdy przeciągasz suwak czasu o wiele klatek, program robi ponowną detekcję i szuka twarzy w pobliżu ostatniej pozycji. Jeśli w pobliżu jest inna twarz albo obiekt podobny do twarzy, może złapać zły obiekt i od tego miejsca prowadzić błędną trajektorię.

Jak poznać: błąd zaczyna się dokładnie w miejscu, do którego przeskoczyłeś suwakiem.

### Procedura naprawy - krok po kroku

Nie używaj **Wyczyść**, bo stracisz też dobre zamazania. Naprawiaj tylko błędny segment.

**Krok 1: Zatrzymaj odtwarzanie.**
Kliknij **STOP**, jeśli film gra. Błędy naprawia się na zatrzymanym filmie, klatka po klatce.

**Krok 2: Znajdź pierwszą błędną klatkę.**
Używaj przycisków `+1`, `-1`, `+10`, `-10` albo suwaka czasu, żeby znaleźć dokładną klatkę, od której zamazanie jest już złe. Zapisz sobie ten numer klatki. To jest punkt cięcia.

**Krok 3: Zidentyfikuj, który to tracker.**
Spójrz na panel prawy. Segment aktywny na bieżącej klatce jest podświetlony na zielono. Jego napis to np. `Twarz 3 [ID7] (A) [100-...]`. To jest podejrzany.

Możesz też kliknąć **IDŹ** przy każdym segmencie, żeby zobaczyć, gdzie się zaczyna. Błędny segment często zaczyna się dokładnie tam, gdzie jest fałszywa detekcja.

**Krok 4: Wybierz jeden z trzech wariantów naprawy.**

**Wariant A: Cały segment jest błędny od początku do końca.**
Przykład: zamazanie od początku jest na plakacie, a nie na twarzy.
- Na liście po prawej kliknij **X** przy tym segmencie.
- Sprawdź, czy po usunięciu nie zniknęło też dobre zamazanie. Jeśli to był jedyny segment tej twarzy, nic więcej się nie stanie.
- Jeśli po usunięciu chcesz dodać prawidłową twarz, stań na klatce startowej i kliknij ją albo narysuj ramkę.

**Wariant B: Początek jest dobry, a od pewnej klatki zamazanie jest złe.**
To jest najczęstszy przypadek przeskoku trackera. Nie usuwaj całego segmentu, bo stracisz dobry początek.
- Stań na pierwszej złej klatce.
- Kliknij **PAUSE**. To ucina wszystkie aktywne segmenty na klatce poprzedniej.
- Jeśli masz tylko jedną twarz, to wystarczy. Dobry początek zostaje, a zła końcówka znika.
- Jeśli masz kilka twarzy i PAUSE ucięło też dobre twarze, to dla każdej dobrej twarzy stań na tej samej klatce i kliknij **RESUME** albo dodaj ją od nowa kliknięciem. Dobre twarze wrócą, a błędna nie, bo jej nie wznowisz.
- Dokładniejsza metoda dla wielu twarzy jest w Wariancie C.

**Wariant C: Masz kilka twarzy i tylko jedna jest błędna, a reszta jest dobra. Nie chcesz używać PAUSE, bo tnie wszystko.**
- Stań na pierwszej złej klatce.
- Najedź na środek błędnej ramki i lekko ją przesuń albo zmień jej rozmiar (nawet o 1 piksel). Program podzieli ten tracker: stary segment kończy się na klatce przed bieżącą, a od bieżącej zaczyna się nowy segment.
- Teraz na liście po prawej masz dwa segmenty tej samej twarzy: dobry stary i zły nowy. Kliknij **X** przy nowym, złym segmencie.
- Dobry początek zostaje. Dobre twarze nie zostały ruszone. Zła końcówka znika.
- Jeśli błędna ramka po przeskoku jest daleko od prawdziwej twarzy, to przesunięcie jej z powrotem na twarz też jest naprawą. Zamiast usuwać ogon, możesz przesunąć ramkę na właściwą twarz. Program od tej klatki będzie śledził już dobrą twarz.

**Krok 5: Sprawdź, czy nie ma drugiego błędnego segmentu tej samej fałszywej twarzy.**
Po skanowaniu AI fałszywa twarz może mieć kilka segmentów, bo znikała i wracała. Przejrzyj listę. Jeśli widzisz kilka segmentów o tym samym numerze twarzy, które zaczynają się w tym samym miejscu (np. ten sam plakat), usuń każdy z nich przyciskiem **X**.

**Krok 6: Zapobiegaj powtórce.**
- Zmniejsz **Tolerancja skoku** z 130 na 80-100, jeśli film jest statyczny. Tracker będzie mniej skakał.
- Sprawdź, czy **Stop gdy zgubi** jest zaznaczony. Ma być zaznaczony.
- Jeśli problem się powtarza w tym samym miejscu, zmień model na inny (np. z YuNet na SCRFD 2.5G albo YOLO-Face) i sprawdź tę samą klatkę. Jeśli nowy model nie widzi tam twarzy, to była fałszywa detekcja.
- Nie używaj „Zamaż cały film (AI)" bez późniejszej kontroli. To jest narzędzie do wstępnego zamazania, a nie do gotowego eksportu.

**Krok 7: Sprawdź naprawę przed eksportem.**
Odtwórz film od klatki przed cięciem do klatki po cięciu. Sprawdź, czy:
- Dobre zamazanie nadal jest.
- Błędne zamazanie zniknęło i nie wraca dalej.
- Nie ma nowej plamy w innym miejscu.

Dopiero wtedy eksportuj.

### Czego NIE robić przy losowych zamazaniach

- Nie klikaj **Wyczyść**, bo stracisz całą pracę.
- Nie klikaj **Cofnij** w ciemno, bo usunie ostatni tracker, a niekoniecznie ten błędny. Błędny może być w środku listy.
- Nie próbuj „zakryć" błędu rysowaniem drugiej ramki na tym samym miejscu. Dostaniesz dwa zamazania na siebie i chaos w segmentach.
- Nie eksportuj z nadzieją, że „na eksporcie będzie dobrze". Eksport liczy to samo, co podgląd. Jeśli w podglądzie jest plama, na eksporcie też będzie.

---

## WAŻNE: Twarz nadal śledzona, ale już odwrócona

Objaw: osoba odwraca się tyłem, pokazuje tył głowy, kark, plecy, a zamazanie nadal za nią chodzi. Albo osoba odwraca głowę w profil, twarzy już nie widać, ale zamazanie nadal jest. Czasem to jest w porządku, czasem nie ma już potrzeby zamazywać.

### Dlaczego program to robi

Tracker CSRT nie rozpoznaje twarzy. On śledzi wzorzec pikseli. Tył głowy, włosy, kark, czapka - dla niego to nadal ten sam obiekt, który śledził. Program nie wie, że twarz się odwróciła. Nie ma funkcji „przestań, gdy nie widać twarzy".

Detektor AI też czasem wykrywa twarz z profilu albo z tyłu, jeśli widzi fragment ucha, żuchwy albo kształt głowy. Dlatego sama zmiana modelu nie rozwiąże tego problemu.

Decyzja, czy zamazywać odwróconą głowę, jest Twoja i zależy od celu:

- **Zamazuj dalej, jeśli:** tył głowy nadal pozwala rozpoznać osobę (charakterystyczna fryzura, tatuaż na karku, ubranie, sylwetka w kontekście) albo jeśli osoba zaraz się odwróci z powrotem i nie chcesz przerwy w zamazywaniu.
- **Przestań zamazywać, jeśli:** twarzy już nie widać, tył głowy jest nierozpoznawalny, a zamazanie tyłu głowy wygląda dziwnie i zwraca uwagę. Wtedy przerwa w zamazywaniu jest naturalna i mniej rzuca się w oczy niż pływająca plama na plecach.

### Procedura - jedna twarz na filmie

To jest najprostszy przypadek.

**Krok 1: Znajdź dokładną klatkę odwrócenia.**
Zatrzymaj film. Cofaj się klatka po klatce przyciskami `-1` i `+1`, aż znajdziesz pierwszą klatkę, na której twarzy już nie widać, a widać tylko tył głowy. To jest klatka, od której nie chcesz już zamazywać.

Przykład: twarz widać do klatki 350. Na klatce 351 widać już tylko tył głowy. To klatka 351 jest pierwszą klatką bez twarzy.

**Krok 2: Stań na pierwszej klatce bez twarzy i kliknij PAUSE.**
PAUSE ustawia koniec segmentu na `bieżąca klatka - 1`. Czyli zamazanie kończy się na klatce 350, a od 351 już go nie ma. Wcześniejsze zamazanie zostaje.

Wskaźnik zmieni się na **PAUZA ŚLEDZENIA**.

**Krok 3: Sprawdź przerwę.**
Przewiń od klatki 345 do 360. Powinno być tak:
- 345-350: zamazanie jest.
- 351 i dalej: zamazania nie ma, widać tył głowy bez plamy.

Jeśli zamazanie znika za wcześnie albo za późno o 1-2 klatki, cofnij się o te klatki i popraw. Możesz użyć **Cofnij**? Nie, bo Cofnij usuwa cały ostatni tracker. Zamiast tego:
- Jeśli uciąłeś za wcześnie, usuń ogon? W tym przypadku nie ma ogona. Musisz dodać nowy segment od klatki, gdzie twarz jeszcze była, i uciąć go w dobrym miejscu. Prościej: stań na klatce 350, kliknij twarz od nowa (stworzy nowy segment od 350), a potem stań na 351 i kliknij PAUSE. To naprawi cięcie o jedną klatkę.

**Krok 4: Gdy osoba odwróci się z powrotem, wznów.**
Gdy osoba znowu pokazuje twarz, stań na pierwszej klatce, gdzie twarz znowu jest dobrze widoczna. Kliknij **RESUME**. Program utworzy nowy segment od tej klatki. Przerwa między PAUSE a RESUME zostaje niezamazana.

Nie klikaj RESUME na klatce, gdzie widać jeszcze tył głowy. Poczekaj na klatkę z twarzą. Inaczej nowy segment zacznie się od tyłu głowy i znowu będziesz miał ten sam problem.

**Krok 5: Sprawdź na odtwarzaniu.**
Odtwórz fragment od 5 klatek przed pauzą do 5 klatek po wznowieniu. Zamazanie powinno znikać i pojawiać się naturalnie, bez skoków.

### Procedura - kilka twarzy, tylko jedna się odwróciła

PAUSE tnie wszystkie twarze naraz, więc nie używaj go tutaj. Użyj metody podziału i usunięcia ogona.

**Krok 1: Znajdź pierwszą klatkę bez twarzy dla tej jednej osoby.** Tak samo jak wyżej, klatka po klatce.

**Krok 2: Podziel tylko ten tracker.**
Stań na pierwszej klatce bez twarzy. Najedź na środek ramki tej jednej odwróconej osoby i lekko ją przesuń (nawet o 1 piksel) albo zmień jej rozmiar. Program podzieli tylko ten tracker:
- Stary segment: od startu do klatki przed bieżącą. Zostaje zamazany prawidłowo.
- Nowy segment: od bieżącej klatki w przód. To jest ogon, który zamazuje tył głowy.

Inne twarze nie zostały ruszone.

**Krok 3: Usuń tylko ogon.**
Na liście po prawej znajdź nowy segment tej twarzy. Będzie miał ten sam numer twarzy, ale nowe ID i start równy bieżącej klatce, np. `Twarz 2 [ID8] (A) [351-...]`. Kliknij przy nim **X**.

Stary segment zostaje. Inne twarze zostają. Ogon z tyłem głowy znika.

**Krok 4: Gdy ta osoba odwróci się z powrotem, dodaj ją od nowa.**
Stań na klatce, gdzie jej twarz znowu jest widoczna. Kliknij tę twarz (AUTO) albo narysuj ramkę (MANUAL). Powstanie nowy segment. Program nada mu ten sam numer twarzy, jeśli rozpozna, że to ta sama okolica, albo nowy numer, jeśli nie. To nie ma znaczenia dla eksportu. Ważne, że zamazanie wraca dopiero od tej klatki.

Nie używaj RESUME w tym wariancie, bo RESUME wznowi też inne segmenty, które wcześniej uciąłeś.

### Procedura - osoba odwraca się, ale za chwilę wraca, a Ty chcesz ciągłości

Jeśli osoba odwraca się tylko na 5-10 klatek i zaraz wraca, czasem lepiej zostawić zamazanie na tył głowy, żeby nie było migotania: zamazanie znika na chwilę i wraca. Migotanie bardziej zwraca uwagę niż ciągłe zamazanie.

W takim przypadku nic nie rób. Zostaw tracker. Dopiero gdy odwrócenie trwa dłużej (np. powyżej 0.5 sekundy, czyli powyżej 12-15 klatek przy 25 FPS), warto robić przerwę.

### Procedura - osoba odwraca się na stałe i już nie wraca

- Utnij segment metodą PAUSE (jedna twarz) albo podział + X (wiele twarzy).
- Nie musisz nic więcej robić. Segment po prostu się kończy.
- Sprawdź na liście, czy segment ma koniec, np. `[100-350]`, a nie `[100-...]`. Trzy kropki oznaczają, że segment nadal trwa i będzie zamazywał dalej. Jeśli widzisz trzy kropki, to znaczy, że nie uciąłeś.

### Czego NIE robić przy odwróconej twarzy

- Nie usuwaj całego trackera prawym przyciskiem myszy ani przyciskiem **X** przy starym segmencie, bo usuniesz też dobry początek, gdzie twarz była widoczna.
- Nie używaj **Wyczyść**, bo usuniesz wszystkie twarze.
- Nie licz na to, że program sam przestanie, gdy twarz się odwróci. On nie przestanie. Musisz mu powiedzieć, gdzie uciąć.
- Nie klikaj RESUME na klatce z tyłem głowy. RESUME ma być na klatce z twarzą.

---

## Kontrola przed eksportem - checklista

Zrób to zawsze, zanim klikniesz **Eksportuj / Zapisz**:

1. Obejrzyj cały film od początku do końca na podglądzie, przynajmniej na przyspieszeniu. Nie ufaj, że skanowanie AI było idealne.
2. Sprawdź listę po prawej. Czy nie ma segmentów, które zaczynają się w losowym miejscu? Kliknij **IDŹ** przy podejrzanych.
3. Sprawdź, czy każdy segment ma koniec tam, gdzie powinien. Segment, który ma `...` na końcu, zamazuje do końca filmu. Czy na pewno ma zamazywać do końca?
4. Sprawdź odwrócenia głowy. Czy po każdym odwróceniu jest przerwa, jeśli tego chciałeś?
5. Sprawdź suwaki: Rozmycie, Wtapianie, Tolerancja skoku. Czy to są wartości, które chcesz na eksporcie?
6. Sprawdź kształt: CIRCLE / BLACKBOX / PIXEL / LINE. Czy na eksporcie ma być ten sam kształt co na podglądzie?
7. Sprawdź znaczniki eksportu. Czy na pasku znaczników nie ma przypadkowego fragmentu? Jeśli nie chcesz wycinać fragmentów, lista znaczników ma być pusta, a pasek ma nie mieć zielonych prostokątów.
8. Sprawdź, czy **Stop gdy zgubi** jest zaznaczony, jeśli chcesz, żeby eksport też przestawał przy zgubieniu.
9. Zrób jeden zrzut ekranu z zamazaniem na trudnej klatce i obejrzyj go na pełnym ekranie. Czy twarzy na pewno nie da się rozpoznać?
10. Dopiero wtedy eksportuj.

Po eksporcie obejrzyj wyeksportowany plik, przynajmniej fragmenty, gdzie były problemy. Eksport liczy śledzenie od nowa, więc w rzadkich przypadkach może się różnić od podglądu o 1-2 klatki.

---

## Rozwiązywanie problemów

| Objaw | Najczęstsza przyczyna | Co zrobić |
| --- | --- | --- |
| Zamazanie „skacze" na inną osobę | Tolerancja skoku za wysoka, szybki ruch | Zmniejsz Tolerancję skoku, utnij błędny ogon, dodaj twarz od nowa |
| Zamazanie wisi w miejscu, gdzie nikogo nie ma | Stop gdy zgubi odznaczony | Zaznacz Stop gdy zgubi, utnij segment na klatce, gdzie twarz zniknęła |
| Detektor znajduje twarze na plakacie, telewizorze, lustrze | Fałszywa detekcja | Usuń ten segment przyciskiem X, zmień model, nie używaj „Zamaż wszystkie zielone" na tej klatce |
| Twarz z profilu nie jest wykrywana | Model nie widzi profilu | Użyj MANUAL (przeciągnij ramkę) albo zmień model na SCRFD 2.5G / YOLO-Face |
| Zamazanie znika po kilku klatkach | Tracker zgubił twarz, Stop gdy zgubi kończy segment | Stań na klatce, gdzie twarz wraca, dodaj ją od nowa albo kliknij RESUME |
| Po PAUSE nic się nie zamazuje | PAUSE ucięło wszystko | Kliknij RESUME na klatce z twarzą albo dodaj twarze od nowa |
| Po RESUME zamazuje tył głowy | RESUME kliknięte za wcześnie | Usuń ten nowy segment, stań na klatce z twarzą, kliknij RESUME jeszcze raz |
| Eksport nie ma dźwięku | Brak ffmpeg.exe | Wgraj ffmpeg.exe do katalogu programu, wyeksportuj jeszcze raz |
| Program nie widzi modelu | Brak pliku .onnx | Kliknij Wskaż... i wskaż plik .onnx albo wgraj plik do katalogu programu |
| Zielone ramki są, ale nie ma zamazania | Nie kliknąłeś twarzy | Zielona ramka to tylko propozycja. Kliknij ją, żeby dodać tracker, albo kliknij „Zamaż wszystkie zielone" |
| Ramka jest za mała albo za duża | Detekcja niedokładna | Najedź na krawędź i zmień rozmiar, albo narysuj ramkę MANUAL od nowa |
| Wtapianie robi „ducha" wokół twarzy | Wtapianie za wysokie | Zmniejsz Wtapianie do 10-20 |
| Rozmycie za słabe, widać oczy | Rozmycie za niskie | Podnieś Rozmycie do 70-90 albo włącz PIXEL / BLACKBOX |

---

## Uwagi końcowe

Program jest narzędziem do pomocy, a nie do w pełni automatycznej anonimizacji. Automatyczne skanowanie zawsze wymaga ręcznej kontroli. Najlepsza jakość to: automatyczne wykrywanie jako punkt startowy, ręczne poprawki w miejscach błędów, kontrola przed eksportem.

Jeśli nagrywasz w trudnych warunkach (szybki ruch, dużo osób, słabe światło), pracuj małymi odcinkami: zamazuj 10-20 sekund, sprawdzaj, poprawiaj, dopiero potem idź dalej. To jest szybsze niż naprawianie godzinnego filmu po jednym skanowaniu.

---

## Licencja i autor

Autor: marek7400 - https://github.com/marek7400

Program do użytku własnego. Używaj go zgodnie z prawem. Zamazywanie twarzy nie zwalnia z obowiązku uzyskania zgody na publikację, jeśli taka zgoda jest wymagana.




