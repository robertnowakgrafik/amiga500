# Licencje danych PolTiny

PolTiny jest nowym modelem trenowanym od losowych wag. Kod i autorskie
polskie przykłady w `training/polish_core.tsv` są na licencji MIT.
Architektura i silnik wywodzą się z Tiny 2.1 autorstwa WollyMonjac (MIT):
https://github.com/WollyMonjac/Tiny-2.1

`training/sources/geo.tsv` pochodzi z Tiny 2.1 (MIT).
Polskie nazwy państw w `training/sources/countries.json` pochodzą z
mledoze/countries: https://github.com/mledoze/countries
Licencja: ODC Open Database License (ODbL) 1.0:
https://opendatacommons.org/licenses/odbl/1-0/

Symbole i liczby atomowe w `training/sources/elements.tsv` pochodzą przez
Tiny 2.1 z Bowserinator/Periodic-Table-JSON:
https://github.com/Bowserinator/Periodic-Table-JSON
Licencja CC BY-SA 3.0: https://creativecommons.org/licenses/by-sa/3.0/
Polskie nazwy pierwiastków opracowano w skrypcie `training/prepare.py`.

Pochodne części korpusu i katalogów zachowują warunki odpowiednich licencji.
Model zawiera informacje z mledoze/countries (ODbL) oraz Periodic-Table-JSON
(CC BY-SA 3.0). Wagi PolTiny nie są kopią angielskiego modelu TINY.BIN.
Pełne źródła użytych danych i skrypt transformacji są w paczce źródłowej.
Baza geograficzna jest statyczna; może nie odzwierciedlać najnowszych zmian.

Kod programowych operacji float32 GCC ma osobną licencję GPLv3 z GCC
Runtime Library Exception; teksty COPYING3 i COPYING.RUNTIME są dołączone.
