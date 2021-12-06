---
layout: post
title:  "Sprawdzanie kodu w ruby - linter - Rubocop"
date:   2021-02-13 19:38:56 +0100
categories: ruby
---
{{< youtube _PUtUkw3JWo >}}

cześć

w dzisiejszym odcinku chciałbym pokazać wam jak sprawdzać składnie naszego kodu.
<!--more-->

w językach które się kompilują sprawdzanie kodu jest czymś normalnym, jeżeli mieliście do czynienia z javascriptem lub typescriptem to tam linter jest domyślnie dostępnym narzędziem. W językach dynamicznych jak ruby czy wspomniany przed chwilą javascript trzeba się posiłkować dodatkowymi narzędziami. Najpopularniejszym z nich jest RuboCop. Nie sprawdza on wprawdzie czy nasz kod ma sens. Ale sprawdza składnie naszego kodu. Warunki jakie musimy spełnić są rozwijane przez społeczność. Więc w większości przypadków możemy śmiało im zaufać. Dodatkowo RuboCop może za nas poprawić bardzo dużo problemów. Jeżeli jeszcze nie używacie RuboCopa w swoich projektach. Jego dodanie powinno być pozycją obowiązkową!

# Instalacja

tak jak każdy gem wystarczy odpalić gem install rubocop ale prawda jest taka, że bardzo rzadko w ten sposób używamy gemów

najczęściej chcemy je dodać bezpośrednio do naszego projektu

`gem 'rubocop', '~> 1.9', require: false`

`require` jako parametr sprawia, że pomimo iż gem się zainstlował to nie jest ładowany razem z pozostałymi gemami (szczególnie przydatne w przypadku railsów)

# Sprawdzanie składni

`rubocop /path/to/your/file`

np `rubocop /app/controller/costam p`

wyświetli nam wszystkie błędy jakie popełniliśmy (albo ktoś przed nami popełnił)

teraz możemy je wszystkie poprawić

albo na końcu komendy dodać `-A` wtedy z automatu poprawi nam błędy. Ale na razie nie będę tego używać bo chce wam pokazać inne rozwiązania.

Ale to nie jedyny sposób na sprawdzanie naszej składni

jeżeli używacie Visual studio code możecie zainstalować wtyczkę do rubocopa

[https://marketplace.visualstudio.com/items?itemName=misogi.ruby-rubocop](https://marketplace.visualstudio.com/items?itemName=misogi.ruby-rubocop)

wtedy składnia będzie sprawdzana bezpośrednio w waszym edytorze. Podobne wtyczki istnieją też dla innych, popularnych edytorów

# Dostosowywanie do naszych potrzeb

Jak już wspomniałem zasady sprawdzania składni są ustanowione przez społeczność więc nie trzeba nic ustawiać  by to działało. Ale niektóre rzeczy chcielibyśmy zmienić

w przykładowym pliku wyświetla, że mamy za długą linie, 80 znaków. To był dobry warunek jak monitory miały po 15" w proporcjach 4:3, ale dzisiaj gdy wszyscy mamy panoramiczne ekrany nie ma to sensu. Możemy to zmienic na 100 a nawet 120

w tym przypadku w pliku `.rubocop.yml` musimy dodać

```html
Metrics/LineLength:
  Max: 100
```

chcemy by metody miały więcej znaków, nie ma problemu

```html
Metrics/MethodLength:
  Max: 15
```

możemy też dodać by wykluczyło jakiś plik lub pliki z tej zasady

```html
Metrics/MethodLength:
  Max: 15
  Exclude:
    - 'app/foo/*.rb'
```

wtedy wszystkie pliki rb z katalogu `app/foo` nie będą sprawdzane przez rubocopa

możemy też wewnątrz jakiegoś pliku wyłączyć sprawdzanie

```html
# rubocop:disable Metrics/ClassLength
```

ale trzeba pamiętać by włączyć to ponownie

```html
# rubocop:enable Metrics/ClassLength
```

oczywiście nie sugeruje, że macie zmieniać cokolwiek na to co pokazałem. To tylko przykłady jak działa rubocop.

Moja rada jest taka, by podążać za narzuconymi zasadami i rozważnie podchodzić do zmian.

Dzięki rubocopowi wasz kod będzie czytelniejszy. Wam będzie się z nim lepiej pracowało. Przywyczaicie się do dobrych praktyk. A i praca w grupie będzie przyjemniejsza

Na dzisiaj to tyle

Dziękuje za oglądanie

Miłego kodowania
