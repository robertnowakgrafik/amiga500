# PolTiny PL dla Amigi 500

Nowy, mały transformator znakowy wytrenowany od losowej inicjalizacji na
polskich pytaniach i odpowiedziach. Pochodzi architektonicznie od Tiny 2.1.
Pracuje całkowicie offline na 68000, bez FPU, z 512 KiB RAM i Kickstartem 1.3.
To ograniczony eksperymentalny model językowy; nie ma możliwości współczesnego
dużego LLM ani szerokiej znajomości języka polskiego.

## Uruchomienie

W WinUAE wybierz A500, procesor 68000, 512 KB Chip RAM, bez Slow/Fast RAM,
Kickstart 1.3. Włóż `dist/PolTiny-A500-512K-boot.adf` do DF0 i uruchom.
Dysk jest OFS z bootblockiem 1.x. Program otwiera standardowy ekran Workbencha
bez wymagania osobnej dyskietki systemowej. ROM nie jest dołączony.
Na prawdziwej Amidze zapisz obraz ADF na dyskietce DD zgodnym narzędziem.

Do szybszych prób w WinUAE można ustawić CPU „Fastest possible” i zwiększyć
„Floppy Drive Emulation Speed”. Program nadal działa offline, ale taki test
nie odtwarza szybkości fizycznej, fabrycznej A500.

Enter lub „Odpowiedz” uruchamia odpowiedź. Rachunki z cyframi i operatorami
przechodzą do dokładnego kalkulatora, np. `2+5`, `(12-3)/4`, `2^8`.
Kalkulator pokazuje wykonane działania. Pozostały tekst uruchamia sieć.
Pytanie ma maksymalnie 72 znaki; pytanie i odpowiedź łącznie mieszczą się w
kontekście 128 znaków. Można pisać bez polskich liter. Stop/Esc przerywa sieć.
Sieć nie uczy się podczas rozmowy i nie korzysta z historii poprzednich pytań.

Model nie mieści się w RAM, dlatego wczytuje kolejne macierze z dyskietki dla
każdego znaku. Odpowiedź tekstowa może wymagać wielu minut. Większy model
zajmuje więcej miejsca i wymaga więcej pracy niż pierwowzór. Matematyka omija
ten koszt. Kalkulator ma ograniczony zakres 32-bitowych ułamków, potęgi całkowite
od -16 do 16 i maksymalnie 6 cyfr wejścia po przecinku.

## Pomiary i wiedza

Szczegóły, wyniki testów i wykresy są w `dist/PolTiny-raport.html`.
Pełny katalog materiału treningowego jest w `dist/PolTiny-wiedza.html`
i `dist/PolTiny-wiedza.csv`. To lista przykładów użytych do uczenia, a nie
gwarancja, że model zawsze odtworzy poprawną odpowiedź. Na dyskietce jest
również czytelny katalog `WIEDZA.TXT` (ISO-8859-2).
Tekst generuje sieć, bez przeszukiwania tego katalogu lub bazy gotowych odpowiedzi.

## Budowanie i trenowanie

`make` wymaga m68k-amigaos-gcc z runtime nix13; ścieżkę zmienia `CROSS`.
`python3 tools/package.py` wymaga `amitools` (zmienna `XDFTOOL`).
Trening CPU: Python 3, PyTorch 2.5.1, NumPy.

```
python3 training/prepare.py
python3 training/train.py --steps 12000 --batch 32
python3 training/export.py
make
python3 training/evaluate.py
python3 tools/package.py
python3 tools/report.py
```

Architektura: D=128, 4 warstwy, 4 głowy, MLP=384, 115 znaków, kontekst=128.
Macierze: int8 z oddzielną skalą float32 na wiersz. Iloczyny: int8×int16,
akumulacja int32. Cache KV: int8 + skale. RMSNorm i softmax: programowe float32.
Silnik rezerwuje 190464 bajty na stercie; ta liczba nie zawiera programu,
buforów statycznych, stosu, GUI, ekranu, systemu ani buforów stacji dyskietek.

Wykresy nie pokazują toku rozumowania: pokazują średnią aktywację ReLU
względem najwyższej warstwy, uwagę ostatniej przeliczonej głowy i warstwy
(grupowaną do 32 słupków) oraz prawdopodobieństwo wybranego znaku.
Prawdopodobieństwo znaku nie jest pewnością poprawności faktu.

Kod: MIT; dodatkowy kod soft-float GCC: GPLv3 z GCC Runtime Library Exception.
Dane mają dodatkowe warunki i atrybucję opisane w `DATA_LICENSES.md`.
