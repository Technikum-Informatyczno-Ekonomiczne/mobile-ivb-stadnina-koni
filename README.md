# 🐎 Projekt: "StajniaPlus" – System Zarządzania Stadniną Koni "Złoty Kłusak"

Witajcie w zespole deweloperskim! Wasz dwuosobowy software house właśnie wygrał przetarg na stworzenie aplikacji mobilnej w **Jetpack Compose** dla najbardziej wymagającego klienta w powiecie. 

Przejdziecie przez pełny cykl życia oprogramowania (**SDLC**) – od analizy nieco chaotycznych wymagań klienta, przez organizację pracy na tablicy **Kanban**, prototypowanie UI/UX i stworzenie własnego motywu, aż po implementację w parach i testy akceptacyjne.

---

## 📋 1. Analiza Wymagań (Notatka ze spotkania z Klientem)

**Klient:** Janusz Podkówka, właściciel Ekskluzywnej Stadniny Koni "Złoty Kłusak" (dawniej PGR Parzydełko).  
**Budżet:** Dwa worki owsa i darmowa jazda na kucyku (czyli robimy to na ocenę).

> *"Słuchajcie młodzi, potrzebuję apki na ten cały Android. Ludzie dzwonią, pytają o konie, rezerwują jazdy na karteczkach, a potem mój kasztan 'Piorun' ma trzech jeźdźców naraz i gryzie ze złości! Ma być nowocześnie: zdjęcia koni, suwaczki do siana, kalendarze i żeby coś wyskakiwało na ekranie, jak klikną! I błagam, żadnych fioletowych kolorków jak w jakimś banku – ma być swojsko: kolor siana, drewna i łąki! A zanim zaczniecie stukać w te klawiatury, narysujcie mi najpierw na kartce albo w komputerze, jak to będzie wyglądać, i zróbcie sobie jakąś tablicę z zadaniami jak w prawdziwej firmie, żebym widział, kto się obija! Mój siostrzeniec już wam zrobił pusty projekt i przełączanie ekranów, musicie tylko dorobić resztę!"*

### Tabela przełożenia wymagań klienta na strukturę aplikacji (Mapowanie SDLC)

Zanim napiszecie pierwszą linijkę kodu, przeanalizujcie, jak "język biznesowy" Pana Janusza przekłada się na konkretną architekturę w Jetpack Compose:

| Czego oczekuje Pan Janusz (Wymaganie biznesowe) | Ekran w aplikacji (`AppScreen`) | Komponenty Jetpack Compose | Logika i zachowanie (Stan / Dane) |
| :--- | :--- | :--- | :--- |
| *"Swojskie kolory: siano, drewno, łąka, żaden fiolet!"* | **Globalnie** (`ui/theme`) | `MaterialTheme`, `lightColorScheme`, `darkColorScheme` | Własna paleta barw (brązy, złociste żółcie, leśna zieleń) przekazywana do całej aplikacji. |
| *"Szyld stadniny na wejściu i wyróżnienie najlepszych koni"* | `STABLE` (Nasza Stajnia) | `Surface`, `LazyRow`, `Image` | Nagłówek z cieniem i mottem stadniny oraz pozioma lista koni z flagą `isVip == true`. |
| *"Katalog wszystkich koni ze zdjęciami, charakterem i ukrytymi wadami"* | `STABLE` (Nasza Stajnia) | `LazyColumn`, `Card`, `Image`, `Icon`, `AnimatedVisibility` | Pionowa lista kart. Kliknięcie w kartę przełącza lokalny stan `expanded: Boolean`, rozwijając sekcję z opisem. |
| *"Suwaczek do siana, żebym widział, czy koń jest najedzony, czy zaraz zrobi bunt"* | `FEEDING` (Paszarnia) | `Slider`, `LinearProgressIndicator` (lub `CircularProgressIndicator`) | Stan `oatsAmount: Float` (0..10 kg) powiązany bezpośrednio z paskiem postępu (`progress = oatsAmount / 10f`). |
| *"Papier z pieczątką i rodowodem konia do pokazania klientowi"* | `FEEDING` (Paszarnia) | `Dialog` (Custom Dialog) | Stan `showCertificate: Boolean`. Wyświetla niestandardowe okno z ozdobną ramką i danymi przodków konia. |
| *"Koniec z karteczkami! Wybór konia, dnia i godziny jazdy"* | `BOOKING` (Rezerwacja) | `ExposedDropdownMenuBox`, `DatePicker`, `TimePicker` | Stany przechowujące wybranego konia/trening, wybraną datę oraz godzinę jazdy. |
| *"Ostrzeżenie przed groźnym koniem i potwierdzenie na dole ekranu"* | `BOOKING` + `Scaffold` | `AlertDialog`, `Snackbar` (`SnackbarHost`) | Kliknięcie "Zarezerwuj" otwiera `AlertDialog`. Akceptacja zamyka okno i wywołuje `snackbarHostState.showSnackbar(...)`. |

---

## 🎨 2. Projektowanie UI/UX: Prototyp oraz Własny Motyw

### A. Prototyp aplikacji (Wireframe / Mockup) – KROK OBOWIĄZKOWY!
Zgodnie z życzeniem klienta, **przed przystąpieniem do kodowania** musicie stworzyć prototyp wszystkich 3 ekranów (`STABLE`, `FEEDING`, `BOOKING`).
* **Narzędzie dowolne:** Figma, Excalidraw, Canva, Balsamiq lub czytelny szkic na kartce papieru (sfotografowany telefonem).
* **Co musi zawierać prototyp:** Rozmieszczenie wszystkich 15 wymaganych komponentów oraz strzałki pokazujące, co pojawia się po kliknięciu (np. rozwinięcie karty, wyskoczenie `AlertDialog`).
* **Dostarczenie:** Zapiszcie plik graficzny z prototypem w głównym folderze repozytorium jako `prototyp.png` i podlinkujcie go na samym dole tego pliku `README.md`!

### B. Własny motyw aplikacji (`ZlotyKlusakTheme`)
Domyślny fioletowy motyw Androida jest surowo zabroniony! W folderze `ui.theme` (lub w osobnym pliku `Theme.kt`) stwórzcie własny motyw stadniny:
1. Zdefiniujcie własne kolory w `Color.kt`, np.:
   * **Primary:** Brąz siodła (`#8B4513`) lub Leśna zieleń (`#2E7D32`)
   * **Secondary:** Złoty owies (`#FBC02D`)
   * **Background / Surface:** Jasny beż / kolor słomy (`#FFFDF7`)
2. Skonfigurujcie `lightColorScheme` (oraz opcjonalnie `darkColorScheme` jako *"Tryb Nocnego Stajennego"*).
3. Owińcie całą aplikację w `ZlotyKlusakTheme { ... }` wewnątrz `MainActivity`.

---

## 🏗️ 3. Organizacja Pracy (Kanban) i Architektura Nawigacji

### A. Tablica Kanban – KROK OBOWIĄZKOWY!
Aby nie pogubić się w zadaniach i udowodnić Panu Januszowi, że pracujecie zgodnie z metodykami zwinnych (Agile), musicie założyć i prowadzić **tablicę Kanban** (np. w zakładce **Projects** na GitHubie lub w **Trello**).
1. Utwórzcie minimum 3 kolumny: **`To Do` (Do zrobienia)**, **`In Progress` (W trakcie)** oraz **`Done` (Gotowe)**.
2. Rozbijcie projekt na pojedyncze zadania (karty) – np. *"Stworzenie prototypu"*, *"Własny motyw kolorystyczny"*, *"Ekran Stajni - LazyRow i LazyColumn"*, *"Okno AlertDialog i Snackbar"* itd.
3. **Przypiszcie wykonawców** (Osoba A / Osoba B) do poszczególnych kart i przesuwajcie je w miarę postępu prac.
4. **Dostarczenie:** Zróbcie zrzut ekranu Waszej tablicy (najlepiej w trakcie pracy lub po jej zakończeniu), zapiszcie go w repozytorium jako `kanban.png` i podlinkujcie na dole tego `README.md`!

### B. Architektura Nawigacji
W projekcie zastosowano **ultraprostą nawigację opartą na stanie (`State-based Navigation`)** bez użycia `NavHost`. Cała nawigacja opiera się na klasie wyliczeniowej `AppScreen` oraz zmiennej stanu w głównym komponencie `Scaffold`:

```kotlin
enum class AppScreen(val title: String) {
    STABLE("Nasza Stajnia"),
    FEEDING("Paszarnia i Kondycja"),
    BOOKING("Rezerwacja Jazdy")
}
```

Przełączanie widoków odbywa się przez zwykłą instrukcję `when (currentScreen)` wewnątrz `Scaffold`.

### C. Podział pracy w parze (żebyście się nie stratowali w Gicie!)
* **Osoba A (Architekt Stajni, Paszarni i Motywu):** Tworzy własny motyw `ZlotyKlusakTheme` oraz odpowiada za ekrany `STABLE` i `FEEDING` (listy koni, animacje szczegółów, suwaki paszy, paski postępu, własny `Dialog`).
* **Osoba B (Architekt UX, Rezerwacji i Powiadomień):** Konfiguruje tablicę Kanban, przygotowuje plik `prototyp.png` (we współpracy z Osobą A) oraz odpowiada za ekran `BOOKING`, globalny `Snackbar` i komponenty formularza (`AlertDialog`, `DatePicker`, `TimePicker`, `ExposedDropdownMenuBox`).

---

## 💻 4. Implementacja (Specyfikacja Techniczna Komponentów)

Aby Pan Janusz odebrał projekt, aplikacja **musi** wykorzystywać wszystkie poniższe komponenty Jetpack Compose w określonym kontekście biznesowym:

### A. Ekran 1: Nasza Stajnia (`AppScreen.STABLE`)
1. **`Surface`** – tło sekcji nagłówkowej stadniny z odpowiednim zaokrągleniem (`shape`) i cieniem (`tonalElevation` / `shadowElevation`), zawierające motto stadniny: *"U nas konie chodzą szybciej niż Twój kod"*.
2. **`LazyRow`** – pozioma galeria **"VIP Konie Miesiąca"** na górze ekranu (małe kafelki z imieniem i awatarem konia).
3. **`LazyColumn`** – główna, przewijana pionowo lista wszystkich koni w stadninie.
4. **`Card`** – pojedyncza karta konia wewnątrz `LazyColumn`.
5. **`Image` oraz `Icon`** – na każdej karcie musi znaleźć się zdjęcie konia (`Image` z `painterResource` i modyfikatorem `clip`) oraz ikona (`Icon`) oznaczająca temperament wierzchowca (np. płomień dla narwanego ogiera, serduszko dla łagodnej klaczy).
6. **`AnimatedVisibility`** – po kliknięciu w kartę konia (`Card`), karta płynnie rozwija ukrytą sekcję ze szczegółami (np. ulubiony przysmak, historia kontuzji, ostrzeżenie: *"Gryzie, gdy widzi tanie jabłka"*).

### B. Ekran 2: Paszarnia i Kondycja (`AppScreen.FEEDING`)
7. **`Slider`** – suwak pozwalający stajennemu ustawić dzienną dawkę owsa dla wybranego konia (w zakresie od 0 do 10 kg).
8. **`LinearProgressIndicator` / `CircularProgressIndicator` (ProgressBar)** – pasek postępu wizualizujący **"Poziom zadowolenia konia"** lub **"Ryzyko buntu w stajni"**, który zmienia swoją wartość dynamicznie w zależności od ustawienia suwaka (`Slider`).
9. **`Dialog` (Custom Dialog)** – po kliknięciu przycisku *"Pokaż certyfikat hodowlany"*, wyświetla się niestandardowe okienko `Dialog` zawierające ozdobną kartę z rodowodem konia i przyciskiem zamknięcia.

### C. Ekran 3: Rezerwacja Jazdy (`AppScreen.BOOKING`)
10. **`ExposedDropdownMenuBox`** – rozwijana lista wyboru konia lub typu treningu (np. *Spacer po lesie*, *Skoki przez przeszkody*, *Galop po bułki do Biedronki*).
11. **`DatePicker` (np. wewnątrz `DatePickerDialog`)** – wybór daty planowanej jazdy konnej.
12. **`TimePicker`** – wybór godziny rozpoczęcia jazdy (pamiętajcie, że między 13:00 a 14:00 konie mają sjestę!).
13. **`AlertDialog`** – okno potwierdzenia rezerwacji przed ostatecznym zapisem (np. Tytuł: *"Czy masz aktualne ubezpieczenie?"*, Treść: *"Wybrałeś konia 'Piorun'. Ostatni jeździec nadal szuka swojego kasku. Czy potwierdzasz rezerwację?"*, przyciski: *Potwierdzam* / *Uciekam*).
14. **`Snackbar` (z `SnackbarHost`)** – po kliknięciu *"Potwierdzam"* w `AlertDialog`, okno się zamyka, a na dole ekranu wyskakuje `Snackbar` z podsumowaniem (np. *"Zarezerwowano jazdę na koniu Piorun dnia 24.10 o 15:30!"*).

---

## 🧪 5. Testowanie i Kryteria Akceptacji (Checklista QA)

Przed wypchnięciem ostatecznej wersji (`git push`) upewnijcie się, że Wasz kod przechodzi testy Pana Janusza:

- [ ] W repozytorium znajduje się zrzut ekranu tablicy zadań **`kanban.png`**.
- [ ] W repozytorium znajduje się plik **`prototyp.png`** pokazujący projekt interfejsu.
- [ ] Aplikacja posiada **własny motyw kolorystyczny** (brak domyślnego fioletu Material3).
- [ ] Aplikacja kompiluje się bez błędów i nie rzuca wyjątkiem `NullPointerException`, gdy koń nie ma zdjęcia.
- [ ] Nawigacja między 3 ekranami działa płynnie bez użycia `NavHost`.
- [ ] Wszystkie **15 wymaganych komponentów** zostało użytych i działa interaktywnie (nie są tylko "martwymi" makietami).
- [ ] Stan aplikacji (`remember` / `mutableStateOf`) jest poprawnie obsługiwany (np. przesunięcie `Slidera` faktycznie zmienia `ProgressBar`, a wybór daty w `DatePicker` wyświetla się w formularzu).
- [ ] W historii repozytorium widać commity **obu osób z pary**.

---

## 🚀 6. Wdrożenie i Dokumentacja Projektowa

Gotowy projekt zatwierdźcie i wypchnijcie na zdalne repozytorium przypisane przez Classroom. Upewnijcie się, że wrzuciliście pliki `kanban.png` oraz `prototyp.png` do głównego katalogu projektu, aby wyświetliły się poniżej:

### 1. Nasza Tablica Kanban:
![Tablica Kanban zespołu](kanban.png)

### 2. Nasz Prototyp UI/UX:
![Prototyp aplikacji StajniaPlus](prototyp.png)

Powodzenia i niech Compose będzie z Wami! 🏇
