---
layout: post
title:  "UUID zamiast ID w Ruby on Rails"
date:   2021-01-27 19:38:56 +0100
categories: ruby
---
{{< youtube 60n7z9Yc7Pw >}}

W tym odcinku chciałbym pokazać wam jak i dlaczego używać uuid zamiast id jako klucza podstawowego w Ruby on Rails.

Na początek może czym jest uuid - uniwersalny, unikalny identyfikator.
id domyślnie jest kolejną liczbą w bazie, uuid natomiast jest stringiem

`1` vs `ccbb63c0-a8cd-47b7-8445-5d85e9c80977`

tutaj widzicie dwa przykłady, pierwszy to id, drugie to właśnie uuid.

ok to tyle w teorii, po co używać uuid?

dla bezpieczeństwa

np link do notatek z tego odcinka, każdy kto ma link może to otworzyć bez logowania, ale w gruncie rzeczy nie każdy powinien mieć w to wgląd

albo serwis do udostępniania plików, chcecie coś podesłać znajomemu ale nie całemu światu

jeżeli używamy id, każdy może dodać lub zmniejszyć twój numer i otworzyć url nie koniecznie dostępny dla niego

oczywiście w dobrze by było dodać jakieś zabezpieczenie jeżeli treści nie powinny być publiczne, ale nie to jest tematem tego odcinka

inną przydatną rzeczą jest ukrywanie informacji o ilości "danych" w aplikacji

jeżeli mamy url users/13 to wiadomo, że przed nami rejestrowało się 12 użytkowników, w przypadku uuida takiej informacji nie mamy

tak więc najważniejsze, jest to, że ukrywamy poufne informacje

ok, czas na implementacje

na początek w naszej bazie potrzebujemy rozszerzenia `pgcrypto`

tworzymy nową migracje

`rails generate migration EnableUUID`

```bash
class EnableUUID < ActiveRecord::Migration
  def change
    enable_extension 'pgcrypto'
  end
end
```

i uruchamiamy migracje

`rails db:migrate`

jeżeli chcemy by w przyszłości domyślnie tworząc migracje używać `uuid` należy w pliku `config/initializers/generators.rb` dodać

```bash
Rails.application.config.generators do |g|
  g.orm :active_record, primary_key_type: :uuid
end
```

aktualnie korzystam z nowej aplikacji więc utwórzmy sobie jakąś tabele, oraz model przy okazji

`rails generate scaffold Post title:string body:text`

odpalmy migracje

`rails db:migrate`

i włączmy serwer `rails s`

w przedlądarce włączmy [http://localhost:3000/posts](http://localhost:3000/posts)

utwórzmy nowy post

jak widzicie w adresie url, zamiast `id`, jest `uuid`

To by było na tyle

Miłego kodowania

[Kod aplikacji](https://github.com/rubypopolsku/uuid-zamiast-id-w-ruby-on-rails)
