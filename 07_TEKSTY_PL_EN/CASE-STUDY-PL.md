# PolTiny PL — model językowy na Amidze 500

Amiga 500 to domowy komputer z 1987 roku — dziś sprzęt zabytkowy. Około 7 MHz, pół megabajta pamięci i dyskietki zamiast współczesnego dysku. Uruchomienie na nim polskiego modelu językowego brzmi jak science fiction. Od tego pomysłu zaczęliśmy.

Fabryczna Amiga 500: 0,5 MB RAM (512 KiB), bez rozszerzeń pamięci, akceleratora i FPU.

## Komputer z 1987 roku. Pomysł z ery AI.

Amiga 500 to domowy komputer z 1987 roku, dziś sprzęt zabytkowy: około 7 MHz, pół megabajta RAM i dyskietki. Polski model językowy na takim komputerze brzmi jak science fiction. Taki był cel — fabryczna A500, bez dodatków, z obliczeniami wykonywanymi lokalnie.

512 KiB RAM, procesor 68000 około 7,09 MHz, Kickstart 1.3 i dyskietka DD 880 KiB. Wolny i ograniczony model został świadomie zaakceptowany.

## Zaczęło się od Tiny. Potem padło: „na Amigę”.

Punktem wyjścia był artykuł PurePC o Tiny 2.1 oraz kod projektu WollyMonjac/Tiny-2.1. To mały znakowy transformator przygotowany pierwotnie dla DOS-u i komputerów x86.

Oryginał: 366 528 parametrów, 4 warstwy, alfabet 97 znaków. Kod MIT. Wagi oryginalnego Tiny były już zapisane jako int8 — późniejsza optymalizacja dotyczyła także sposobu liczenia.

## Pierwszy sukces: działa. Pierwszy problem: czeka.

Pojawiło się okno programu na Amidze, ale nawet proste pytanie wymagało minut. Na screenach postęp przesuwał się z 7/31 do 9/31: program pracował, choć z perspektywy użytkownika wyglądał na zawieszony.

Każdy znak pytania i odpowiedzi wymagał przeliczenia warstw oraz ponownego odczytu wag z dyskietki. Bez FPU nawet krótkie operacje float32 były kosztowne.

## Wkładasz ADF. Program startuje sam.

Pierwszy obraz służył jako dysk danych. Kolejna iteracja otrzymała bootblock zgodny z Kickstartem 1.x, system plików OFS i Startup-Sequence uruchamiający program z odpowiednim stosem.

Samobootujący ADF 880 KiB. Osobna dyskietka Workbench nie jest potrzebna. Wymagany jest Kickstart 1.3; obraz nie zawiera ROM-u ani plików dyskietki Workbench.

## Mniej kosztownych obliczeń. Ta sama testowa odpowiedź.

Zmieniliśmy iloczyny macierzy i obliczenia uwagi: aktywacje skalowane do int16, wagi int8, akumulacja int32. Zmniejszyliśmy również zbędne projekcje wyjścia i skoki odczytu wag.

Porównanie dotyczy tego samego angielskiego modelu, pytania i pierwszych 4 znaków odpowiedzi „Thre”. To pomiar cykli CPU w emulacji 68000; bez fizycznej stacji i pełnego GUI. Nie jest porównaniem z późniejszym, większym PolTiny.

## 2 + 5 nie powinno czekać na transformator.

Największa zmiana użytkowa: rozpoznane działanie trafia do zwykłego parsera i dokładnego kalkulatora. Wynik pojawia się bez ładowania modelu, a panel pokazuje wykonane kroki.

Obsługa +, −, ×, /, potęg i nawiasów; dokładne ułamki w ograniczonym zakresie. 0,1 + 0,2 daje 0,3. Dzielenie przez zero zgłaszane jest od razu. Około 0,020 s dotyczy cykli CPU, bez fizycznego ładowania programu z dyskietki.

## Od polskich dodatków do nowego modelu PL.

Po drodze powstał szybki tryb z kalkulatorem i lokalną bazą: 86 polskich zestawów odpowiedzi oraz objaśnienia zjawisk. Następny etap spełniał inny cel: własne polskie wagi, zamiast samego wyszukiwania odpowiedzi.

PolTiny trenowano od losowej inicjalizacji. 914 wpisów materiału, 4 123 pytania treningowe i 1 337 odłożonych sformułowań. 115 znaków obejmuje polskie litery. Trening odbywał się na współczesnym komputerze; generowanie działa offline na Amidze.

## Polskie objaśnienia. Widoczne kroki obliczeń.

Dodaliśmy polski interfejs, krótkie wyjaśnienia w materiale modelu i jawne kroki kalkulatora. Podczas generowania można śledzić aktywność warstw, uwagę i prawdopodobieństwo następnego znaku oraz przerwać obliczenia.

Wykresy to rzeczywista telemetria silnika, nie tekstowy zapis myśli. Model nie ma osobnego modułu ogólnego rozumowania. Prawdopodobieństwo znaku nie jest miarą prawdziwości odpowiedzi.

## Większy model. Nadal jedna dyskietka.

PolTiny ma 687 616 parametrów — około 1,88× więcej niż pierwowzór. Wagi int8 z osobnymi skalami na wiersz zajmują 708 442 bajty. Cała dyskietka jest zapełniona w 99,55% użytecznymi plikami i narzutem OFS.

Silnik rezerwuje 186 KiB na stercie: cache KV, bufor macierzy i gainy. Program, stos, ekran i system potrzebują dodatkowego RAM. Większa sieć zwiększa koszt generowania tekstu; kalkulator nadal omija ten koszt.

## Co udało się poprawić. Co pozostaje ograniczeniem.

Wynik końcowy to mały eksperymentalny model językowy po polsku, z szybkim kalkulatorem i narzędziami obserwacji. Dobrze odtwarza pytania treningowe, ale słabiej reaguje na ich nowe sformułowania.

Test po kwantyzacji, pełna zgodność tekstu odpowiedzi. Odłożone pytania dotyczą znanych faktów; to nie pomiar wiedzy na dowolny temat. Odpowiedź „stolica polski” → „Warszawa.” kosztowała około 7,5 min samych cykli 68000. Fizyczna dyskietka i pełne GUI nie są zawarte w tym czasie.

## Sprawdzone w WinUAE. Start bez dyskietki Workbench.

Autor projektu potwierdził uruchomienie i działanie w WinUAE. Celem konfiguracji pozostaje Amiga 500 z 0,5 MB RAM, bez rozszerzeń. Obraz ADF jest samobootujący i program działa po polsku.

Potwierdzenie WinUAE pochodzi od autora projektu. Oddzielnie sprawdzono kod na 68000 w VAMOS, kalkulator, model, testy pytań oraz OFS i bootblock. Nie deklarujemy niezależnego testu na fizycznej A500. Rozwój z pomocą OpenAI Codex; rdzeń i architektura na podstawie Tiny 2.1 / WollyMonjac (MIT).


Tiny 2.1 / WollyMonjac: https://github.com/WollyMonjac/Tiny-2.1
Dane / Data: mledoze/countries (ODbL), Periodic-Table-JSON (CC BY-SA 3.0), original Polish examples.
