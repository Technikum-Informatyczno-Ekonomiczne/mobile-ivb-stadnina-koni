# 🐎 Projekt: "StajniaPlus" – System Zarządzania Stadniną Koni "Złoty Kłusak"

Witajcie w zespole deweloperskim! Wasz dwuosobowy software house właśnie wygrał przetarg na stworzenie aplikacji mobilnej w **Jetpack Compose** dla najbardziej wymagającego klienta w powiecie. 

Przejdziecie przez pełny cykl życia oprogramowania (**SDLC – Software Development Life Cycle**) – od analizy chaotycznych wymagań klienta przy wsparciu AI, przez zaprojektowanie architektury systemu, organizację pracy na tablicy **Kanban**, prototypowanie UI/UX i stworzenie własnego motywu, aż po implementację w parach i testy akceptacyjne.

---

## 🧠 0. Inżynieria Oprogramowania w dobie AI (Wasza Rola w SDLC)

W tym projekcie korzystanie z narzędzi AI (ChatGPT, Claude, Gemini, Copilot) jest **dozwolone i wskazane**. Pamiętajcie jednak, że w nowoczesnym SDLC rola programisty drastycznie się zmienia. Podczas obrony projektu oceniane będzie Wasze podejście jako **Architektów Systemu** w oparciu o 5 filarów:

1. **Poziom abstrakcji:** Przechodzimy na wyższy poziom pracy — tak jak dawniej zrezygnowano z pisania w asemblerze na rzecz języków wyższego poziomu (jak Kotlin), tak dziś praca przesuwa się z ręcznego pisania kodu na **zarządzanie całościowym konceptem systemu**, przepływem stanu (*State Hoisting*) i spójnością doświadczenia użytkownika (UX).
2. **Architektura systemu i powiązania komponentów:** Musicie rozumieć wysokopoziomowy schemat tego, jak współpracują ze sobą poszczególne elementy aplikacji: model danych konia, centralny stan nawigacji w `Scaffold`, lokalne stany ekranów (`STABLE`, `FEEDING`, `BOOKING`), motyw kolorystyczny `MaterialTheme` oraz globalny system powiadomień (`SnackbarHost`).
3. **Definiowanie granic i warunków brzegowych (Edge Cases):** Jasne określenie, jakie dane, pliki i scenariusze aplikacja obsługuje, a jakich nie. Co się stanie, gdy użytkownik spróbuje zarezerwować jazdę w czasie sjesty koni ($13:00\text{--}14:00$)? Co jeśli nie wybierze daty w `DatePicker`, a kliknie „Zarezerwuj”? Co gdy koń nie ma przypisanego zdjęcia?
4. **Świadomość konsekwencji i przewidywanie ryzyka:** To kluczowy czynnik ludzki polegający na ocenie, czy dane rozwiązanie wygenerowane przez AI nie niesie ze sobą niebezpiecznych skutków architektonicznych lub biznesowych, których model może nie uwzględnić z braku pełnego kontekstu (np. przypadkowe współdzielenie jednego stanu `expanded` między wszystkimi kartami koni, co spowoduje otwarcie całego katalogu naraz, albo utrata danych formularza przy przełączeniu ekranu).
5. **Obsługa braku determinizmu:** Pamiętajcie o tym, że modele językowe generują odpowiedzi na podstawie prawdopodobieństwa matematycznego, a nie rzeczywistego rozumienia logiki. W Jetpack Compose AI często halucynuje nieistniejące parametry, miesza stare API `Material 2` z `Material 3` lub używa eksperymentalnych komponentów (np. `DatePicker`, `TimePicker`, `ExposedDropdownMenuBox`) bez adnotacji `@OptIn(ExperimentalMaterial3Api::class)`. **Kluczowe jest weryfikowanie przez Was, czy końcowe rozwiązanie kompiluje się i działa zgodnie z oczekiwaniami.**

---

## 📋 1. Faza Analizy Wymagań i Odwrócenie Ról (Requirements)

### A. Notatka ze spotkania z Klientem
**Klient:** Janusz Podkówka, właściciel Ekskluzywnej Stadniny Koni "Złoty Kłusak" (dawniej PGR Parzydełko).  
**Budżet:** Dwa worki owsa i darmowa jazda na kucyku (czyli robimy to na ocenę).

> *"Słuchajcie młodzi, potrzebuję apki na ten cały Android. Ludzie dzwonią, pytają o konie, rezerwują jazdy na karteczkach, a potem mój kasztan 'Piorun' ma trzech jeźdźców naraz i gryzie ze złości! Ma być nowocześnie: zdjęcia koni, suwaczki do siana, kalendarze i żeby coś wyskakiwało na ekranie, jak klikną! I błagam, żadnych fioletowych kolorków jak w jakimś banku – ma być swojsko: kolor siana, drewna i łąki! A zanim zaczniecie stukać w te klawiatury, narysujcie mi najpierw na kartce albo w komputerze, jak to będzie wyglądać, i zróbcie sobie jakąś tablicę z zadaniami jak w prawdziwej firmie, żebym widział, kto się obija! Mój siostrzeniec już wam zrobił pusty projekt i przełączanie ekranów, musicie tylko dorobić resztę!"*

### B. Technika Odwrócenia Ról z AI (Role Reversal) – KROK OBOWIĄZKOWY!
Zanim napiszecie pierwszą linijkę kodu, musicie doprecyzować wymagania i wyłapać dziury logiczne w pomyśle Pana Janusza. Zamiast pytać AI *„Jak napisać ten kod?”*, zastosujcie **metodę odwróconych ról**, w której to **AI przepytuje Was jako głównych projektantów systemu**.

**Jak to zrobić krok po kroku:**
1. Otwórzcie wybrane narzędzie AI i wklejcie poniższy prompt startowy:
   > *"Wciel się w rolę doświadczonego Architekta Systemów Mobilnych (Jetpack Compose) oraz analityka biznesowego. Tworzymy w parze aplikację 'StajniaPlus' dla właściciela stadniny koni (3 ekrany: Katalog Koni z kartami i listą VIP, Paszarnia z suwakiem owsa 0-10 kg i certyfikatem w Dialogu, Rezerwacja jazdy z DatePickerem, TimePickerem, Dropdownem, AlertDialogiem i Snackbarem). Zastosuj technikę odwrócenia ról: NIE pisz dla nas kodu. Zamiast tego zadaj nam w 2 turach po 3 trudne pytania dotyczące: 1) architektury systemu i przepływu stanu między komponentami, 2) granic systemu i warunków brzegowych (edge cases) w formularzach i suwakach, 3) potencjalnych ryzyk i konsekwencji naszych decyzji projektowych. Po naszych odpowiedziach pomóż nam spisać podsumowanie architektury."*
2. Odpowiedzcie wspólnie na pytania AI, podejmując świadome decyzje projektowe (np. jak blokujecie godzinę sjesty $13:00\text{--}14:00$, czy zmiana dawki owsa w Paszarni zapisuje się w obiekcie konia, czy tylko lokalnie na ekranie).
3. **Dostarczenie:** Skopiujcie zapis Waszej rozmowy oraz wypracowane w niej warunki brzegowe i wklejcie na samym dole tego pliku `README.md` w sekcji dokumentacji!

### C. Tabela przełożenia wymagań klienta na strukturę aplikacji (Mapowanie SDLC)

| Czego oczekuje Pan Janusz (Wymaganie biznesowe) | Ekran w aplikacji (`AppScreen`) | Komponenty Jetpack Compose | Logika, Stan i Granice Systemu |
| :--- | :--- | :--- | :--- |
| *"Swojskie kolory: siano, drewno, łąka, żaden fiolet!"* | **Globalnie** (`ui/theme`) | `MaterialTheme`, `lightColorScheme`, `darkColorScheme` | Własna paleta barw (brązy, złociste żółcie, leśna zieleń) przekazywana do całej aplikacji. |
| *"Szyld stadniny na wejściu i wyróżnienie najlepszych koni"* | `STABLE` (Nasza Stajnia) | `Surface`, `LazyRow`, `Image` | Nagłówek z cieniem i mottem stadniny oraz pozioma lista koni z flagą `isVip == true`. |
| *"Katalog wszystkich koni ze zdjęciami, charakterem i ukrytymi wadami"* | `STABLE` (Nasza Stajnia) | `LazyColumn`, `Card`, `Image`, `Icon`, `AnimatedVisibility` | Pionowa lista kart. Kliknięcie w kartę przełącza lokalny stan `expanded: Boolean` przypisany wyłącznie do klikniętej karty. |
| *"Suwaczek do siana, żebym widział, czy koń jest najedzony, czy zaraz zrobi bunt"* | `FEEDING` (Paszarnia) | `Slider`, `LinearProgressIndicator` (lub `CircularProgressIndicator`) | Stan `oatsAmount: Float` (granice: `0f..10f`) powiązany bezpośrednio z paskiem postępu (`progress = { oatsAmount / 10f }`). |
| *"Papier z pieczątką i rodowodem konia do pokazania klientowi"* | `FEEDING` (Paszarnia) | `Dialog` (Custom Dialog) | Stan `showCertificate: Boolean`. Wyświetla niestandardowe okno z ozdobną ramką i danymi przodków konia. |
| *"Koniec z karteczkami! Wybór konia, dnia i godziny jazdy"* | `BOOKING` (Rezerwacja) | `ExposedDropdownMenuBox`, `DatePicker`, `TimePicker` | Stany przechowujące wybranego konia/trening, datę oraz godzinę jazdy wraz z walidacją granic (np. sjesta $13:00\text{--}14:00$). |
| *"Ostrzeżenie przed groźnym koniem i potwierdzenie na dole ekranu"* | `BOOKING` + `Scaffold` | `AlertDialog`, `Snackbar` (`SnackbarHost`) | Kliknięcie "Zarezerwuj" otwiera `AlertDialog`. Akceptacja zamyka okno i wywołuje `snackbarHostState.showSnackbar(...)`. |

---

## 🎨 2. Faza Projektowania (Design): Architektura, Prototyp i Motyw

### A. Prototyp aplikacji i Schemat Powiązań (`prototyp.png`) – KROK OBOWIĄZKOWY!
Zgodnie z życzeniem klienta i zasadami inżynierii oprogramowania, **przed przystąpieniem do kodowania** musicie stworzyć wizualny prototyp wszystkich 3 ekranów (`STABLE`, `FEEDING`, `BOOKING`) oraz prosty schemat powiązań komponentów.
- **Narzędzie dowolne:** Figma, Excalidraw, Canva, Balsamiq lub czytelny szkic na kartce papieru (sfotografowany telefonem).
- **Co musi zawierać prototyp:** 
  1. Rozmieszczenie wszystkich 15 wymaganych komponentów UI na 3 ekranach.
  2. Strzałki pokazujące przepływ interakcji i powiązania komponentów (np. skąd `Scaffold` wie, jaki ekran wyświetlić, co wywołuje `AlertDialog`, jak `AlertDialog` komunikuje się ze `SnackbarHost`).
- **Dostarczenie:** Zapiszcie plik graficzny z prototypem w głównym folderze repozytorium jako `prototyp.png` i podlinkujcie go na samym dole tego pliku `README.md`!

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
Aby nie pogubić się w zadaniach i udowodnić Panu Januszowi, że pracujecie zgodnie z metodykami zwinnymi (Agile), musicie założyć i prowadzić **tablicę Kanban** (np. w zakładce **Projects** na GitHubie lub w **Trello**).
1. Utwórzcie minimum 3 kolumny: **`To Do` (Do zrobienia)**, **`In Progress` (W trakcie)** oraz **`Done` (Gotowe)**.
2. Rozbijcie projekt na pojedyncze zadania (karty) – np. *"Wywiad architektoniczny z AI (Role Reversal)"*, *"Stworzenie prototypu i schematu"*, *"Własny motyw kolorystyczny"*, *"Ekran Stajni - LazyRow i LazyColumn"*, *"Obsługa warunków brzegowych w Rezerwacji"* itd.
3. **Przypiszcie wykonawców** (Osoba A / Osoba B) do poszczególnych kart i przesuwajcie je w miarę postępu prac.
4. **Dostarczenie:** Zróbcie zrzut ekranu Waszej tablicy, zapiszcie go w repozytorium jako `kanban.png` i podlinkujcie na dole tego `README.md`!

### B. Architektura Nawigacji
W projekcie zastosowano **ultraprostą nawigację opartą na stanie (`State-based Navigation`)** bez użycia `NavHost`. Cała nawigacja opiera się na klasie wyliczeniowej `AppScreen` oraz zmiennej stanu w głównym komponencie `Scaffold`:

~~~kotlin
enum class AppScreen(val title: String) {
    STABLE("Nasza Stajnia"),
    FEEDING("Paszarnia i Kondycja"),
    BOOKING("Rezerwacja Jazdy")
}
~~~

Przełączanie widoków odbywa się przez zwykłą instrukcję `when (currentScreen)` wewnątrz `Scaffold`. Zastanówcie się podczas projektowania architektury: *czy po przełączeniu ekranu z `FEEDING` na `STABLE` i powrocie suwak owsa powinien pamiętać swoją wartość, czy się resetować? Od czego to zależy w Jetpack Compose?*

### C. Podział pracy w parze (żebyście się nie stratowali w Gicie!)
- **Osoba A (Architekt Stajni, Paszarni i Motywu):** Tworzy własny motyw `ZlotyKlusakTheme`, model danych konia oraz odpowiada za ekrany `STABLE` i `FEEDING` (listy koni, animacje szczegółów, suwaki paszy, paski postępu, własny `Dialog`).
- **Osoba B (Architekt UX, Rezerwacji i Powiadomień):** Konfiguruje tablicę Kanban, przygotowuje plik `prototyp.png` (we współpracy z Osobą A) oraz odpowiada za ekran `BOOKING`, walidację warunków brzegowych rezerwacji, globalny `Snackbar` i komponenty formularza (`AlertDialog`, `DatePicker`, `TimePicker`, `ExposedDropdownMenuBox`).

---

## 💻 4. Faza Implementacji (Specyfikacja Techniczna Komponentów)

Aby Pan Janusz odebrał projekt, aplikacja **musi** wykorzystywać wszystkie poniższe komponenty Jetpack Compose w określonym kontekście biznesowym:

### A. Ekran 1: Nasza Stajnia (`AppScreen.STABLE`)
1. **`Surface`** – tło sekcji nagłówkowej stadniny z odpowiednim zaokrągleniem (`shape`) i cieniem (`tonalElevation` / `shadowElevation`), zawierające motto stadniny: *"U nas konie chodzą szybciej niż Twój kod"*.
2. **`LazyRow`** – pozioma galeria **"VIP Konie Miesiąca"** na górze ekranu (małe kafelki z imieniem i awatarem konia).
3. **`LazyColumn`** – główna, przewijana pionowo lista wszystkich koni w stadninie.
4. **`Card`** – pojedyncza karta konia wewnątrz `LazyColumn`.
5. **`Image` oraz `Icon`** – na każdej karcie musi znaleźć się zdjęcie konia (`Image` z `painterResource` i modyfikatorem `clip`) oraz ikona (`Icon`) oznaczająca temperament wierzchowca (np. płomień dla narwanego ogiera, serduszko dla łagodnej klaczy).
6. **`AnimatedVisibility`** – po kliknięciu w kartę konia (`Card`), karta płynnie rozwija ukrytą sekcję ze szczegółami (np. ulubiony przysmak, historia kontuzji, ostrzeżenie: *"Gryzie, gdy widzi tanie jabłka"*).

### B. Ekran 2: Paszarnia i Kondycja (`AppScreen.FEEDING`)
7. **`Slider`** – suwak pozwalający stajennemu ustawić dzienną dawkę owsa dla wybranego konia (w zakresie od $0$ do $10\text{ kg}$).
8. **`LinearProgressIndicator` / `CircularProgressIndicator` (ProgressBar)** – pasek postępu wizualizujący **"Poziom zadowolenia konia"** lub **"Ryzyko buntu w stajni"**, który zmienia swoją wartość dynamicznie w zależności od ustawienia suwaka (`Slider`).
9. **`Dialog` (Custom Dialog)** – po kliknięciu przycisku *"Pokaż certyfikat hodowlany"*, wyświetla się niestandardowe okienko `Dialog` zawierające ozdobną kartę z rodowodem konia i przyciskiem zamknięcia.

### C. Ekran 3: Rezerwacja Jazdy (`AppScreen.BOOKING`)
10. **`ExposedDropdownMenuBox`** – rozwijana lista wyboru konia lub typu treningu (np. *Spacer po lesie*, *Skoki przez przeszkody*, *Galop po bułki do Biedronki*).
11. **`DatePicker` (np. wewnątrz `DatePickerDialog`)** – wybór daty planowanej jazdy konnej.
12. **`TimePicker`** – wybór godziny rozpoczęcia jazdy (pamiętajcie o warunku brzegowym: między $13:00$ a $14:00$ konie mają sjestę!).
13. **`AlertDialog`** – okno potwierdzenia rezerwacji przed ostatecznym zapisem (np. Tytuł: *"Czy masz aktualne ubezpieczenie?"*, Treść: *"Wybrałeś konia 'Piorun'. Ostatni jeździec nadal szuka swojego kasku. Czy potwierdzasz rezerwację?"*, przyciski: *Potwierdzam* / *Uciekam*).
14. **`Snackbar` (z `SnackbarHost`)** – po kliknięciu *"Potwierdzam"* w `AlertDialog`, okno się zamyka, a na dole ekranu wyskakuje `Snackbar` z podsumowaniem (np. *"Zarezerwowano jazdę na koniu Piorun dnia 24.10 o 15:30!"*).

---

## 🧪 5. Faza Testowania i Weryfikacji Determinizmu (Checklista QA)

Pamiętajcie, że kod wygenerowany przy wsparciu AI może wyglądać poprawnie, ale nie działać zgodnie z logiką biznesową. Przed wypchnięciem ostatecznej wersji (`git push`) zweryfikujcie swój system:

- [ ] Przeprowadzono wywiad z AI metodą **Odwróconych Ról** i uzupełniono sekcję dokumentacji architektonicznej na dole tego pliku.
- [ ] W repozytorium znajduje się zrzut ekranu tablicy zadań **`kanban.png`**.
- [ ] W repozytorium znajduje się plik **`prototyp.png`** pokazujący projekt interfejsu i powiązania komponentów.
- [ ] Aplikacja posiada **własny motyw kolorystyczny** (brak domyślnego fioletu Material3).
- [ ] **Weryfikacja granic i ryzyk:** Aplikacja kompiluje się bez błędów, obsługuje warunki brzegowe (np. sjesta koni, puste pola formularza, granice suwaka $0\text{--}10\text{ kg}$) i nie rzuca wyjątkiem `NullPointerException`.
- [ ] Nawigacja między 3 ekranami działa płynnie bez użycia `NavHost`.
- [ ] Wszystkie **15 wymaganych komponentów** zostało użytych w standardzie **Material 3** i działa interaktywnie (nie są tylko "martwymi" makietami wygenerowanymi przez AI).
- [ ] Stan aplikacji (`remember` / `mutableStateOf`) jest poprawnie odseparowany (np. kliknięcie jednej karty konia rozwija tylko tę jedną kartę, a przesunięcie `Slidera` faktycznie zmienia `ProgressBar`).
- [ ] W historii repozytorium widać commity **obu osób z pary**.

---

## 🚀 6. Wdrożenie i Dokumentacja Projektowa (Wypełnia Zespół)

Gotowy projekt zatwierdźcie i wypchnijcie na zdalne repozytorium przypisane przez Classroom. Upewnijcie się, że wrzuciliście pliki `kanban.png` oraz `prototyp.png` do głównego katalogu projektu oraz uzupełniliście poniższy raport architektoniczny:

### 1. Nasza Tablica Kanban:
![Tablica Kanban zespołu](kanban.png)

### 2. Nasz Prototyp UI/UX i Schemat Powiązań:
![Prototyp aplikacji StajniaPlus](prototyp.png)

### 3. Raport Architektoniczny i Wywiad z AI (Odwrócenie Ról):

#### A. Zapis dialogu z AI (lub lista pytań, które zadało nam AI i nasze decyzje):
> *(Wklejcie tutaj zapis Waszej sesji Role Reversal z AI)*

#### B. Zdefiniowane granice systemu i warunki brzegowe (Edge Cases):
1. **Warunek brzegowy 1 (np. Sjesta koni 13:00–14:00):** *(Opiszcie, jak aplikacja reaguje)*
2. **Warunek brzegowy 2 (np. Próba rezerwacji bez wyboru daty):** *(Opiszcie, jak aplikacja reaguje)*
3. **Warunek brzegowy 3:** *(Opiszcie)*

#### C. Weryfikacja AI (Gdzie AI się pomyliło lub wymagało nadzoru człowieka?):
* *(Opiszcie krótko 1-2 sytuacje, w których kod od AI nie zadziałał od razu, miał zły poziom abstrakcji, użył złego API lub wymagał Waszej korekty architektonicznej)*

---
Powodzenia i niech Compose będzie z Wami! 🏇
