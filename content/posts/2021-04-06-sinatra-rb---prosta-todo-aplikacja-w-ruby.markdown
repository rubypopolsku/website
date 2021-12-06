---
layout: post
title:  "Sinatra.rb - prosta todo aplikacja w Ruby"
date:   2021-04-06 08:31:56 +0100
categories: ruby
---
{{< youtube  _tBuaLiDO7A >}}

Cześć,
w dzisiejszym odcinku chciałbym pokazać wam jak zbudować bardzo prostą aplikacje bez użycia Ruby on Rails.

Do tego celu użyjemy gema sinatra

[http://sinatrarb.com/](http://sinatrarb.com/)

nie jest to żaden framework a biblioteka która pozwala szybko tworzyć aplikacje internetowe w języku Ruby

<!--more-->

na początek utwórzmy sobie nowy plik `todo.rb`

```ruby
require 'sinatra'

get '/' do
  'Hello world!'
end
```

zainstalujemy gem sinatra

`gem install sinatra`

teraz możemy uruchomić nasz wcześniej utworzony plik

`ruby todo.rb`

jeżeli wejdziemy na [http://localhost:4567](http://localhost:4567/) zobaczymy napis hello world który to ustaliliśmy w pierwszym pliku

teoretycznie jeden plik wystarczy by aplikacja działała

ale utwórzmy sobie `Gemfile` bo za już za chwilę okażę się, że kilka gemów potrzebujemy

```ruby
source 'https://rubygems.org'

gem 'rack'
gem 'sinatra'
```

jak widzicie po za sinatrą jest jeszcze rack. Czym jest rack - [https://github.com/rack/rack](https://github.com/rack/rack)

rack to najprostszy webserwer w języku ruby, dodatkowo większość popularnych rozwiązań również go wykorzystuje.

utwórzmy nowy plik

`config.ru`

a w nim

```
require 'rubygems'
require 'bundler'

Bundler.require

require './todo'
run Todo
```

musimy jeszcze edytować todo.rb

```ruby
class Todo < Sinatra::Base
	get '/' do
	  'Hello world!'
	end
end
```

od teraz naszą aplikacje będziemy uruchamiać `bundle exec rackup`

zmieni się też url na [http://localhost:9292/](http://localhost:9292/)

naszym zadaniem jest utworzenie todolisty tak więc będziemy potzebować:

- wyświetlić wszystkie elementy
- utworzyć nowy element
- oznaczyć element jako ukończony
- skasować element

wróćmy więc do naszego pliku`todo.rb`

edytujmy go tak

```ruby

get '/' do
  #.. show something ..
end
```

to będzie wyświetlać naszą listę

```
post '/item' do
 # .. create something ..
end
```

to będzie dodawać nowy element

```
post '/done' do
 # .. finish item ..
end

```

to będzie oznaczać element jako ukończony

```ruby
delete '/:id' do
  #.. annihilate something ..
end
```

to będzie usuwać zbędne elementy

w sumie powinniśmy mieć plik wyglądający tak

```ruby
class Todo < Sinatra::Base
  get '/' do
  # .. show something ..
  end

  post '/item' do
    #.. create something ..
  end

  post '/done' do
    #.. replace something ..
  end

  delete '/:id' do
  # .. annihilate something ..
  end
end
```

W pierwszym przykładzie widzieliście, że wyświetlało nam Hello World

my potrzebujemy trochę bardziej skomplikowanego widoku, dlatego potrzebujemy obsługę ERB

na szczęście nic więcej nie trzeba dodawać do jego obsługi, sinatra będzie szukać widoków w katalogu `views` utwórzmy więc taki katalog a w nim dodajmy sobie plik `index.html.erb`

a w naszym pliku `todo.rb`

```ruby
...
get '/' do
	erb :index
end
...
```

w naszym widoku przydało by się dodać jeszcze jakąś zawartość.

```ruby
<html>
  <head>
    <title>Todo</title>
  </head>
  <body>
    <h1>Witaj</h1>
  </body>
</html>
```

bym zapomniał, po wprowadzeniu zmian trzeba zrestartować serwer by zmiany zobaczyć. Na szczęście można łatwo tozmienić

musimy zainstalować odpowiedni gem `gem 'sinatra-contrib'`

teraz dodajmy kilka zmian w naszej aplikacji (todo.rb)

```ruby
require "sinatra/reloader"

class Todo < Sinatra::Base
  configure :development do
    register Sinatra::Reloader
  end

	...
end
```

od teraz gdy wprowadzimy jakieś zmiany wystarczy, że odświeżymy naszą stone.

Następnym krokiem zapewne by była baza danych

w trakcie tego filmu chce zbudować coś naprawdę prostego więc w gruncie rzeczy możemy operować  na prostym pliku data.json zamiast całej bazie danych. Unikniemy dodatkowej konfiguracji, instalacji czy migracji

utwórzmy ten plik bez żadnej zawartości `data.json`

dodatkowo przydała by nam się obsługa jsona w naszej aplikacji więc do `Gemfile` dodajmy

```ruby
...
gem 'json'
```

najłatwiej będzie zacząć od wyświetlania

do naszego `data.json` dodajmy pierwsze wpisy

```json
[
  {
    "id": "1",
    "name": "test",
    "completed": false
  },
  {
    "id": "2",
    "name": "test test",
    "completed": false
  }
]
```

teraz możemy zacząć korzystać z tego pliku

zacznijmy od zczytania jego zawartości

`file = File.read('./data.json')`

teraz JSONa przeróbmy na hasha

`@items = JSON.parse(file)`

teraz możemy z niego skorzystać w naszym widoku

dodajmy

```json
<ul class="list-group">
      <% @items.each do |item| %>
        <li class="list-group-item"><%= item["name"] %></li>
      <% end %>
    </ul>
```

musicie pamiętać, że nasze dane to hash, nie obiekt active recorda jak w railsach więc zamiast `item.` używamy `item["nazwa_klucza"]`

spróbujmy teraz dodawać nowy element do naszej listy

```json
<form method="POST" action="/item">
      <p>Nazwa: <input type="text" name="name"></p>
      <input type="submit" value='Dodaj'>
    </form>
```

takim prostym formularzem będziemy dodawać element

samo zapisywanie będzie trochę trudniejsze niż w przypadku active recorda

sam formularz przekazuje nam tylko

```json
{"name"=>"to co wpiszemy w formularz"}
```

nie ma tam id elementu i nie mamy stanu

utwórzmy coś takiego

```json
new_tem = {
      id: SecureRandom.uuid,
      name:  params["name"],
      completed: false,
    }
```

teraz musimy to dopisać do naszego pliku

otwórzmy go

`file = File.read('./data.json')`

zróbmy z niego hash

`data_hash = JSON.parse(file)`

dodać nasz nowy task

`data_hash << new_tem`

i na koniec zapisać

`File.write('./data.json', JSON.dump(data_hash))`

jeszcze jedna rzecz na koniec

trzeba wrócić na naszą stronę główną

`redirect '/`'

możemy wejść na [http://localhost:9292/](http://localhost:9292/) i zobaczyć czy działa

oczywiście taki rodzaj zapisu jest bardzo nie efektywny, po za budowaniem jakiegoś proof of concept nie polecam użycia czegoś takiego. Ale na potrzeby tego video jest to dobre rozwiązanie.

Gdy mamy już dowanie nowych elementów, może jako następny krok zrobimy ich usuwanie

Jako, że formularze w HTMLu nie wspierają metody delete zmienimy ją na post. Oczywiście możemy się wesprzeć javascriptem, ale nie będziemy teraz tego robić

```json
post '/delete' do
  # .. annihilate something ..
end
```

```json
<form method="POST" action="/delete">
	<input type="hidden" name="delete_id" value="<%= item['id'] %>">
	<button name="delete_data">Usuń</button>
</form>
```

w naszej metodzie usuwania ponownie musimy otworzyć nasz plik

```json
file = File.read('./data.json')
data_hash = JSON.parse(file)
```

a następnie wyszukać i skasować nasz element. Po to właśnie dodajemy id do naszego elementu

```json
data_hash.delete_if { |element| element["id"] == params["id"] }
```

musimy też pamiętać by dane zapisać

```json
File.write('./data.json', JSON.dump(data_hash))
```

na koniec musimy jeszcze wrócić na naszą stronę główną

```json
redirect '/'
```

Na koniec została nam tylko jedna akcja

oznaczanie jako wykonane

formularz będzie bardzo podobny do poprzedniego

```json
<form method="POST" action="/done">
	<input type="hidden" name="id" value="<%= item['id'] %>">
	<input type="submit" value='Ukończone'>
</form>
```

podobnie jak w poprzedniej metodzie musimy otworzyć nasz plik

```json
file = File.read('./data.json')
data_hash = JSON.parse(file)
```

i wyszukać element który nas interesuje

`element = data_hash.find{ |e| e['id'] == params['id'] }`

edytujemy to co nas interesuje

`element["completed"] = true`

i zapiszmy plik

i oczywiście musim pamiętać o powrocie na stronę główną

```json
redirect '/'
```

teraz jeszcze tylko trzeba nasz widok zmienić tak by widać było który element jest ukończony

```json
<% if item["completed"] %>
            <p><s><%= item["name"] %></s></p>
          <% else %>
            <p><%= item["name"] %></p>
            <form method="POST" action="/done">
              <input type="hidden" name="id" value="<%= item['id'] %>">
              <input type="submit" value='Ukończone'>
            </form>
          <% end %>
```

z takim ifem, jeżeli mamy ukończony element, tekst będzie przekreślony a przycisk nie będzie nam się wyświetlał

Dodam jeszcze jedną rzecz na koniec

sortowanie. Niech wszystkie elementy do wykonania będą na górze

jest tylko jeden mały problem

nie możemy zrobić

`data_hash.sort_by { |item| item['completed'] }` ponieważ nie możemy posortować true false

zwróciło by to nam błąd

`** ArgumentError Exception: comparison of FalseClass with true failed`

ale zamiast tego możemy spróbować zrobić tak:

`@items = data_hash.sort_by! { |item| item['completed']? 1 : 0 }`

w ten sposób będziemy mieli nie ukończone elementy na górze, a ukończone na dole

to tyle na dzisiaj

dziękuje za oglądanie

Miłego kodowania!
[Kod aplikacji](https://github.com/rubypopolsku/sinatra-todo)
