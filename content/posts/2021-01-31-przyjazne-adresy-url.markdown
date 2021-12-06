---
layout: post
title:  "Przyjazne adresy URL w Ruby on Rails"
date:   2021-01-31 19:38:56 +0100
categories: ruby
---
{{< youtube n5im8F5RB4Q >}}

Cześć!

w tym odcinku chciałbym pokazać wam jak dodać tak zwany "slug" do naszego adresu url. W tej chwili jeżeli popatrzymy na nasz adres url zawiera on id z bazy. Nie jest to rozwiązanie przyjazne użytkownikom oraz wyszukiwarkom. Slug dodany do naszego modelu nada każdemu elementowi łatwy do odczytania identyfikator.

```bash
# tradycyjny url
http://example.com/states/4323454

# po dodaniu suga
http://example.com/states/washington
```
<!--more-->

By dodać slug do modelu najprościej było by dodać nową kolumnę w bazie danych i samemu generować slug na podstawie innego pola. Np tytułu naszego posta. Jednak jest wiele problemów które trzeba by wtedy ogarnąć. Slug jest skróconą wersja innego pola, jak mamy post `testujemy rozwiązanie a` oraz `testujemy rozwiązanie b` to skrócona wersja `testujemy` mogła by nam generować wiele problemów. Dlatego w celu obsługi takich adresów użyje gema [friendly_id](https://github.com/norman/friendly_id). Jest to bardzo popularny gem, regularnie aktualizowany i istnieje od 8 lat.

do Gemifile dodajemy

`gem 'friendly_id', '~> 5.4.0'`

odpalamy `bundle install`

gem jest zainstalowany

musimy jeszcze dodać nową migracje

`rails g migration AddSlugToPosts slug:uniq`

oraz `rails generate friendly_id`

na koniec odpalamy migracje

`rails db:migrate`

zajrzyjmy do naszego modelu `post.rb`

musimy dodać informacje związane z friendly id

```bash
class Post < ApplicationRecord
  extend FriendlyId
  friendly_id :title, use: :slugged
end
```

najważniejsze to poinformować z jakiego pola ma tworzyć slug

`friendly_id :title, use: :slugged`

zajrzyjmy też do kontrolera `posts_controller.rb`

musimy zmienić metodę `set_post`, by szukała postów przy pomocy sluga

```bash
def set_post
  @post = Post.friendly.find(params[:id])
end
```

utwórzmy nowy Post

i zobaczmy czy zadziała

Jeżeli nasza aplikacja zawiera już jakieś dane powinniśmy ponownie zapisać każdy rekord, by wygenerować slug

`Post.find_each(&:save)`

To by było na tyle

Nowy odcinek już wkrótce.

Miłego kodowania

[Kod aplikacji](https://github.com/rubypopolsku/przyjazne-adresy-url)
[Gem friendly_id](https://github.com/norman/friendly_id)
