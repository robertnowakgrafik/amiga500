# Jak działa Tiny na Amidze i co zawierają jego dane

## Dlaczego `2+5` zajmowało minuty

Oryginalny Tiny nie przekazuje takiego pytania do kalkulatora. Czyta trzy znaki
`2`, `+`, `5`, przetwarza separator pytania, a następnie generuje odpowiedź
znak po znaku. Odpowiedź może zaczynać się od angielskiego `It is...`, więc
sieć musi wygenerować także te dodatkowe litery. Każdy znak wymaga kolejnego
przebiegu czterech warstw i odczytu setek kilobajtów wag z dyskietki.

W nowym trybie **Szybko** kalkulator wykonuje arytmetykę bez modelu i bez bazy:
`2+5` daje `7`, a `2+3*4` daje `14` ze skróconymi krokami `3*4=12`, `2+12=14`.
Obliczenia używają dokładnych ułamków z ograniczonym zakresem, więc `0.1+0.2`
daje `0.3`. Błędy, np. dzielenie przez zero, są zgłaszane od razu.

Test całego polecenia TinyQuick `2+5` na emulowanym 68000 zużył około 156 tys.
cykli. To około 0,022 s czasu CPU przy 7,09 MHz. Ten wynik nie obejmuje
odczytu pliku programu z fizycznej dyskietki ani rysowania pełnego interfejsu.
W oknie kalkulator jest już częścią uruchomionego programu.

## Co faktycznie robi sieć

Tiny ma 4 warstwy o szerokości 96, po 4 głowice uwagi i warstwę pośrednią
o szerokości 256. Zbiór parametrów ma 366 528 elementów, zwykle zaokrąglanych
do 367 tysięcy. Tekst obsługuje znakowo, ze słownikiem 97 tokenów.

1. Zamienia znak na wektor i dodaje informację o jego pozycji.
2. Mechanizm uwagi łączy informacje z dotychczasowych pozycji kontekstu.
3. Warstwy przekształcają te wartości przy użyciu wytrenowanych wag.
4. Na wyjściu powstają oceny kolejnych znaków; wybierany jest znak z najwyższą oceną.
5. Powtarza to do zakończenia odpowiedzi lub wyczerpania kontekstu 128 znaków.

Wagi zostały wytrenowane wcześniej. Program nie uczy się podczas rozmowy,
nie przegląda internetu i nie aktualizuje swojej wiedzy. Wykres uwagi jest
rozkładem liczbowym, a pewność znaku nie jest pewnością prawdziwości odpowiedzi.
Sieć nie ma osobnego, dostępnego tekstowego toku rozumowania.

## RAM, CPU i dysk

| Element | Wielkość / znaczenie |
|---|---|
| `TINY.BIN` | 381 808 bajtów, 372,9 KiB; 42,37% pojemności dyskietki 880 KiB |
| Bufory modelu w trybie Sieć EN | 131 456 bajtów, 128,4 KiB; 25,07% RAM 512 KiB |
| W tym cache kluczy/wartości | 102 400 bajtów |
| Bieżąca tabela wag i skal | 25 600 bajtów |
| Pozostałe alokowane parametry | 3456 bajtów |
| Stos programu | 16 KiB; 3,125% RAM |
| Indeks szybkiej bazy | 54 276 bajtów; ładowany przy pytaniach tekstowych |
| `TINY.KB` na dysku | 99 361 bajtów w tej wersji |
| Kalkulator | Nie ładuje `TINY.BIN` ani indeksu bazy |

Do tych liczb dochodzą kod, dane statyczne, buforowanie C, okno i system.
Dokładnego całkowitego wykorzystania pamięci użytkownika nie zmierzono.
Indeks szybkiej bazy jest zwalniany przy przejściu do sieci, a bufory sieci
przy powrocie do trybu szybkiego.

Nie ma rzetelnego pomiaru procentowego obciążenia CPU z samego zrzutu ekranu.
Podczas obliczeń program może wykorzystywać prawie cały dostępny czas 68000;
podczas operacji dyskowych czeka na urządzenie. Procent CPU procesu WinUAE
w Windows dotyczy komputera gospodarza i nie oznacza tego samego co obciążenie
emulowanego 68000. Wskaźnik `disk 82%` aplikacji oznacza postęp czytania tabeli
wag, nie zajętość dysku, przepustowość ani procent obciążenia napędu.

Tryb neuronowy odczytuje około 348 KiB na znak pytania i 357 KiB na znak
odpowiedzi. Wypowiedź zawierająca łącznie 40 przetworzonych znaków może więc
wymagać łącznie około 14 MiB odczytów z tej samej dyskietki. Nie jest to
14 MiB nowych danych zapisanych na dysku.

## Jaka wiedza była w treningu

W źródłach generatora są **2872 wpisy tabelaryczne, 626 zestawów pytań i
odpowiedzi tekstowych oraz osobno 194 listy sąsiedztwa państw**. To materiał
treningowy. Nie oznacza, że sieć zapamiętała każdy wpis poprawnie.

| Temat | Przykładowy zakres |
|---|---|
| Geografia | 194 państwa: stolice, kontynenty, języki, waluty, mieszkańcy, regiony, granice i domeny; 49 wpisów stolic stanów USA |
| Chemia | Symbole i liczby atomowe 118 pierwiastków; częściowo typ i stan skupienia |
| Zwierzęta | 55 wpisów klasyfikacji; nogi, młode, odgłosy, pokarm, środowisko, nazwy grup i płci |
| Kosmos | 34 zestawy: planety, Słońce, Księżyc, galaktyka, pierwsi astronauci |
| Ciało człowieka | 37 zestawów: narządy, zmysły, podstawowe funkcje organizmu |
| Przyroda i nauka | 73 + 30 zestawów: zjawiska, materiały, jednostki, narzędzia i proste doświadczenia |
| Komputery i DOS | 52 + 35 zestawów: pamięć, procesor, programowanie, języki, pliki i polecenia |
| Historia | 46 zestawów o osobach, wynalazkach i wydarzeniach |
| Żywność i sport | 26 + 20 zestawów |
| Kształty | 37 zestawów o podstawowych figurach i własnościach |
| Czas | 33 zestawy tekstowe oraz relacje dni tygodnia i miesięcy |
| Języki i słowa | Przeciwieństwa, synonimy, liczby mnogie, czas przeszły, stopniowanie; proste słowa po francusku, niemiecku, hiszpańsku i włosku |
| Matematyka | Proste działania, tabliczka mnożenia, porównywanie liczb, parzystość i liczby rzymskie |
| Rozmowa i pozostałe | 30 zestawów rozmowy, 29 o świecie i 104 różne |

Model nie ma stałej liczby gotowych odpowiedzi: składa je z kolejnych znaków.
Ten sam fakt może pojawiać się w wielu pytaniach. Autor podał około 61%
trafności w 780 niewidzianych pytaniach i około 90% dla znanych sformułowań.
Testy portu sprawdzają zgodność odpowiedzi z oryginałem; nie są nowym pomiarem
trafności całej wiedzy.

## Co dodaje szybki polski tryb

`TINY.KB` ma 4523 klucze wyszukiwania i 2676 współdzielonych rekordów wartości
lub odpowiedzi. Wykorzystuje oryginalne dane oraz **86 nowych polskich
zestawów odpowiedzi**. Obejmuje wiele tematów, a nie tylko stolice. Polskie
odpowiedzi zawierają także krótkie objaśnienia zjawisk, np. dnia i nocy,
pór roku, fotosyntezy czy pływania lodu.

To lokalne wyszukiwanie z szablonami, a nie wytrenowany polski model ogólnego
rozumowania. Pełna swobodna rozmowa po polsku wymagałaby innych wag i większych
zasobów. Nieznane pytanie w trybie szybkim kończy się informacją o braku
pewnej odpowiedzi. Dopiero świadome użycie **Sieć EN** uruchamia powolny model.

Pełny katalog zawiera 3778 wierszy źródłowych wraz z dodatkami PL. Jest dostępny
w `dist/Tiny-wiedza.html` i `dist/Tiny-wiedza.csv`. To pliki do przeglądania na
współczesnym komputerze; nie zajmują miejsca na obrazie dyskietki Amigi.
