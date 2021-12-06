---
layout: post
title:  "Wypełnianie bazy danych danymi. Seeds w Ruby on Rails"
date:   2021-03-27 20:31:56 +0100
categories: ruby
---
{{< youtube  tMw3AlUxhe4 >}}

Cześć
w dzisiejszym odcinku chciałbym pokazać wam, jak zapełnić naszą developerską bazę danymi. Jest za to odpowiedzialny task db:seed i pliks seeds.rb.

Jest to bardzo przydatne narzędzie. Gdy pracujemy w kilka osób i po instalacji aplikacji chcemy by jakieś dane w niej były. Szczególnie gdy są to bardziej złożone dane.
Możemy mieć użytkowników z różnymi rolami, jakieś domyślne ustawienia, treści, itp

Z punktu widzenia bloga, to może być administrator, czytelnik. Kilka postów i kilka komentarzy do tego.

<!--more-->

[Kod który tutaj używam pochodzi z pierwszego filmu który nagrałem.](https://www.youtube.com/watch?v=CXNCHUA3-4k)

Zajrzyjmy do pliku `db/seeds.rb`

domyślnie jest tam zakomentowany przykładowy kod

```ruby
# This file should contain all the record creation needed to seed the database with its default values.
# The data can then be loaded with the bin/rails db:seed command (or created alongside the database with db:setup).
#
# Examples:
#
#   movies = Movie.create([{ name: 'Star Wars' }, { name: 'Lord of the Rings' }])
#   Character.create(name: 'Luke', movie: movies.first)
```

my musimy zrobić coś podobnego

na pierwszy ogień stwórzmy naszego autora bloga

```ruby
autor = User.create(email: "kontakt@rubypopolsku.pl", password: "foobar2000", author: true)
```

po za autorem, utwórzmy jeszcze komentującego

```ruby
komentujący = User.create(email: "foo@bar.dev", password: "Qwerty1", author: false)
```

zanim utworzymy posty potrzebujemy kategorie

```ruby
kategoria = Category.create(name: "Kategoria testowa")
```

skoro mamy użytkowników to możemy utworzyć kilka postów

```ruby
10.times do
	Post.create(title: "Post testowy", body: "tresc testowa", category: kategoria, user: autor)
end
```

ale niech każdy post ma też komentarze

wewnątrz tej pętli dodajmy jeszcze ich tworzenie

```ruby

2.times do
  Comment.create(body: "komentarz", post: post, user: User.all.sample)end
```

w ten sposób autorem komentarza będzie losowy użytkownik

mamy już wszystkie potrzebne dane

przejdźmy do terminala

odpalmy `rails db:seed`

w ten sposób zapełnimy naszą bazę danych danymi

włączmy `rails server`

przejdźmy na `localhost:3000`

jak widzicie są nasze dane

ale

wszystkie treści wyglądają tak samo

możemy to trochę urozmaicić korzystając z gema "faker"

[https://github.com/faker-ruby/faker](https://github.com/faker-ruby/faker)

dodajmy go do naszego gemila

`gem 'faker'`

pamiętajcie o `bundle install`

faker ma bardzo dużo gotowych rozwiązań, praktycznie na każdy rodzaj pola

i tak zamiast komentarza możemy zmienić to na cytat z przyjaciół

```ruby
Comment.create(body: Faker::TvShows::Friends.quote, post: post, user: User.all.sample)
```

a nasze posty, na fragmenty z twórczości lovecrafta

```ruby
post = Post.create(title: Faker::Books::Lovecraft.sentence, body: Faker::Books::Lovecraft.paragraph(sentence_count: 5), category: kategoria, user: autor)
```

na koniec zmieńmy jeszcze kategorie, niech będą dwie i niech się lepiej nazywają

```ruby
2.times do
  Category.create(name: Faker::Books::Lovecraft.fhtagn)
end
```

a kategoria do posta też niech przypisuje się losowo

```ruby
post = Post.create(title: Faker::Books::Lovecraft.sentence, body: Faker::Books::Lovecraft.paragraph(sentence_count: 5), category: Category.all.sample, user: autor)
```

a na koniec sformatujmy jeszcze ten kod by czytelniej wyglądał

```ruby
autor = User.create(email: "kontakt@rubypopolsku.pl", password: "foobar2000", author: true)

komentujący = User.create(email: "foo@bar.dev", password: "Qwerty1", author: false)

2.times do
  Category.create(name: Faker::Books::Lovecraft.fhtagn)
end

10.times do
	Post.create(
    title: Faker::Books::Lovecraft.sentence,
    body: Faker::Books::Lovecraft.paragraph(sentence_count: 5),
    category: Category.all.sample,
    user: autor
  ).tap do |post|
    3.times do
      Comment.create(
        body: Faker::TvShows::Friends.quote,
        post: post,
        user: User.all.sample
      )
    end
  end
end
```

jeżeli jeszcze raz odpalicie `rails db:seed` to dane z naszego pliku się dopiszą, co zależnie od walidacji może powodować problemy

na wszelki wypadek zresetuję bazę danych

`rails db:reset`

odpalę jeszcze raz migracje

`rails db:migrate`

i na koniec nasze seedy

`rails db:seed`

teraz jak włączę serwer (`rails server`)

to na [http://localhost:3000](http://localhost:3000) mamy bardziej przejrzyste dane

Na dzisiaj to już tyle

Dziękuje za oglądanie.
Miłego kodowania!

[Kod aplikacji](https://github.com/rubypopolsku/Wype-nianie-bazy-danych-danymi.-Seeds-w-Ruby-on-Rails)
