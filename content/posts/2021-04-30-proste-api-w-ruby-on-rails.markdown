---
layout: post
title:  "Proste API w Ruby on Rails"
date:   2021-04-30 08:31:56 +0100
categories: ruby
---
{{< youtube  EoBC2mY0aDo >}}

W czasach rozbudowanych frameworków frontendowych widoki renderowane po stronie Ruby on Rails coraz cześciej nie jest to to czego oczekujemy od backendowej aplikacji. W wielu przypadkach nasza aplikacja ma wystawiać tylko API. Zdarzają się też przypadki gdy nawet gdy widoki są renderowane po stronie railsów to potrzebujemy zrobić aplikacje mobilną. Wtedy również przyda nam się API. Ale nie tylko, może potrzebujemy go do komunikacji z innymi aplikacjami pisanymi przez zewnętrznych developerów.

<!--more-->

JavaScript Object Notation pomimo nazwy jest to niezależny od konkretnego języka format tekstowy. Przykład widzicie właśnie na ekranie.

```ruby
{
  "menu": {
    "id": "file",
    "value": "File",
    "popup": {
      "menuitem": [
        {"value": "New", "onclick": "CreateNewDoc()"},
        {"value": "Open", "onclick": "OpenDoc()"},
        {"value": "Close", "onclick": "CloseDoc()"}
      ]
    }
  }
}
```

jest to tablica asosjacyjna Wszystkie dane są zmiennymi (nie stanowią kodu wykonywalnego), a nazwy składników (właściwości) obiektów są otoczone cudzysłowami. Wartości mogą być typu string (napis otoczony cudzysłowem), number (liczba typu double), stanowić jedną ze stałych: false null true, być tablicą złożoną z takich elementów lub obiektem. Obiekty i tablice mogą być dowolnie zagnieżdżane.

Skoro wiem już co jest co to zaczynamy naszą aplikacje

Zróbmy więc API prostego bloga, który pozwoli nam stworzyć, edytować i usuwać posty.

```ruby
rails new blog-api --api -d postgresql
```

flaga api informuje generatory, że nasza aplikacja będzie odpowiadać tylko za api

flaga d oraz nazwa bazy to wybór naszej bazy danych

Użyjmy sobie generatora scaffold `rails generate scaffold Post title:string body:text`

Post to nazwa naszego modelu, jednocześnie utworzy nam tabele, o nazwie posts

Następnie podajemy atrybuty naszego modelu, tytuł który będzie stringiem i body które będzie tekstem

Generator uaktualni też nasz router

Przyjrzyjmy się zmianą w plikach

Jak widzicie powstał kontroler ze wszystkimi interesującymi nas akcjami

Widoków nie ma ponieważ renderujemy tylko jsony

utwórzmy bazę danych `rails db:create` a następnie migracje `rails db:migrate`

teraz w konsoli `rails c` utwórzmy sobie post testowy. `Post.create(title: "Test", body: "test")`

albo lepiej 10 postów

```ruby
10.times do |i|
	Post.create(title: "title-#{i}", body: "Foooooo#{i}")
end
```

włączmy nasz serwer

`rails s`

jak wejdziemy sobie na [http://localhost:3000/posts](http://localhost:3000/posts) zobaczymy nasze posty

a jak wejdziemy na [http://localhost:3000/posts](http://localhost:3000/posts)/1 zobaczymy post o id 1

jak korzystać z api?

można np w terminalu użyć komendy curl [http://localhost:3000/posts](http://localhost:3000/posts)  tym samym curlem można też dane do nasze aplikacji wyslać

wspaniałym i pełnym  możliwości narzędziem jest postman [https://www.postman.com/](https://www.postman.com/) o którym nagram osobny film

ale na dzisiaj jego postawowe opcje nam wystarczą

możemy wykonać ten sam get

ale możemy zmienić akcje na post

[http://localhost:3000/posts](http://localhost:3000/posts)

a jako nasze parametry ustawmy coś takiego

`post[title] = foo`

`post[body] = bar`

utworzy to nam nowy post

tak samo możemy posty edytować czy kasować

zmiana na delete i dodanie id w adresie post nam usunie

a jak dacie to drugi raz to będzie error, że nie znaleziono elementu

oczywiście macie kontrole nad danymi które nam się wyświetlają. Jak zauważyliście w zwrotce mamy created_at i updated_at
załóżmy, że nie chcemy tej informacji

zobaczcie na metode `index` w naszym kontrolerze

```ruby
def index
    @posts = Post.all

    render json: @posts
  end
```

możemy użyć Active Recorda do tego

```ruby
def index
    @posts = Post.all.only(:title, :body)

    render json: @posts
  end
```

możemy też użyć metody map która wywołuje kod dla każdego elementu wewnątrz siebie. Na koniec zwraca nowy element, Array, z wartości które zostały zwrócone

```ruby
@posts = Post.all.map do |post|
	{
		id: post.id,
		title: post.title,
		body: post.title,
	}
end
```

ten drugi przykład jest lepszym chociaż by dlatego, ze możemy transformować nasze dane. Gdybyśmy jednak chcieli mieć created_at ale w jakimś dziwnym formacie to w tym drugim przykładzie możemy to zrobić. Pierwszy przykład będzie za to działać szybciej

Na dzisiaj to tyle

Dziękuje za oglądanie

życzę miłego kodowania

[kod aplikacji](https://github.com/rubypopolsku/blog-api)
