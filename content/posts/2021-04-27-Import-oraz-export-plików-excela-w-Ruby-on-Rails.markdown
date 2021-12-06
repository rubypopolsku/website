---
layout: post
title:  "Import oraz export plików excela w Ruby on Rails"
date:   2021-04-27 08:31:56 +0100
categories: ruby
---
{{< youtube  qX2n9Vva-1s >}}

Gdy zaczynacie swoją przygodę z programowaniem wydaje wam się, że na wszystko można zrobić aplikacje, w końcu to tylko kolejna warstwa nad bazą danych. W pewnym momencie dochodzi się jednak do wniosku, że bez sensu jest pisać nową aplikacje skoro to samo można zrobić w excelu.

Excel to potężne narzędzie i raczej prędzej niż później będzie potrzebować importu lub exportu danych do tego programu. Biznes wymaga excela. Albo chociaż pliku CSV.

<!--more-->

Na potrzeby tego video  utworzę nową aplikacje

`rails new excel-example -d postgresql`

gdy mamy już aplikacje skorzystam z generatora scaffold i utworzę model Item by nasza aplikacja nie była pusta

`rails generate scaffold Item name:string price:float`

pamiętajmy odpalić migracje

`rails db:migrate` chociaż z racji, że to nowa aplikacja to jeszcze trzeba by bazę danych utworzyć `rails db:create`

na początek może export danych
utwórzmy jakieś by było co exportować

`rails console`

```ruby
10.times do |i|
	Item.create(name: "item-#{i}", price: rand(1...100))
end
```

włączmy sobie naszą aplikacje by zobaczyć jak to wygląda

na samym dole jak widzicie mamy przycisk `new item` dodajmy sobie do niego przycisk export

w widoku dodajmy

`<%= link_to 'Export Items', export_items_path %>`

jeżeli odświeżycie stronę powinniście zobaczyć błąd ponieważ nie ma takiego routa

musimy go dodać

zajrzyjmy do `routes.rb`

i dodajmy

```ruby
Rails.application.routes.draw do
  resources :items do
    get :export, on: :collection
  end
end
```

adres jest już poprawny. Z tym, że nic nam się nie wydarzy

przejdźmy więc do kontrolera `items_controller` i utwórzmy nową metodę export

```ruby
def export
end
```

teraz musimy stworzyć właściwy export

dodajmy gemy odpowiedzialne za generowanie plików

```ruby
gem 'caxlsx'
gem 'caxlsx_rails'
```

i pamiętajmy o `bundle install`

w metodzie `export` w naszym kontrolerze zmieńmy

```ruby
def export
    @items = Item.all
    render xlsx: "create", template: "items/export"
end
```

musimy jeszcze utworzyć widok

`export.axlsx`

a w nim

```ruby
wb = xlsx_package.workbook
wb.add_worksheet(name: "Items") do |sheet|
  @items.each do |item|
    sheet.add_row [item.id, item.name, item.price]
  end
end
```

jeżeli chcecie mieć jeszcze nazwy tych pól to plik musi wyglądać tak

```ruby
wb = xlsx_package.workbook
wb.add_worksheet(name: "Items") do |sheet|
	sheet.add_row ['Id', 'Name', 'Price']
  @items.each do |item|
    sheet.add_row [item.id, item.name, item.price]
  end
end
```

wtedy przed pętlą z każdym przedmiotem dodamy tytuły pól

przy tak prostym exporcie pewnie wystarczył by export do pliku CSV, a jeżeli chcecie robić bardziej skomplikowane rzeczy odsyłam do dokumentacji gema.

zrestartujmy serwer i zobaczmy jak to działa

na http://localhost:300/items

jeżeli wszystko jest ok pora na import pliku.

Na cele tego filmu załóżmy, że importować będziemy ten sam plik, numery ID są dla nas unikalne więc ID z pliku uznajemy, za ID elementu, resztę danych będziemy nadpisywać

wizualnie niestety ja nie umiem zrobić linka który by otwierał wybór plików więc dodamy prosty formularz. Multipart używane jest właśnie przy uploadzie plików Dodam jeszcze nad nim nową linie (tag HR) by było to lepiej widoczne

```ruby

<hr>
<%= form_tag import_items_path, multipart: true do %>
  <%= file_field_tag :file %>
  <%= submit_tag "Import" %>
<% end %>
```

w `routes.rb`

należy dodać

```ruby
resources :items do
    get :export, on: :collection
    post :import, on: :collection
  end
```

teraz zajmijmy się naszą metodą import

Do importu pliku potrzebujemy innego gema

```ruby
...
gem "roo", "~> 2.8.0"
```

```ruby
def import
	file = params[:file].tempfile
   spreadsheet = Roo::Excelx.new(file).to_a
```

do tego dodajmy pętle dla każdego wpisu i przypiszmy czym jest każde pole

```ruby
spreadsheet.each do |item_array|
	id = item_array[0]
  name = item_array[1]
  price = item_array[2]
end
```

znajdźmy nasz item `item = Item.find_or_create_by(id: id)` w ten sposób znajdzie lub utworzy pole o wybranym id. Pamiętajcie plik excela to nasze źródło prawdy

teraz zaktualizujmy nasze dane

`item.update(name: name, price: price)`

na koniec dodajmy jeszcze redirect `redirect_to items_path` by odświeżyć nasz widok

ostatecznie metoda wygląda tak

```ruby
def import
    file = params[:file].tempfile
    spreadsheet = Roo::Excelx.new(file).to_a
    spreadsheet.each do |item_array|
      id = item_array[0]
      name = item_array[1]
      price = item_array[2]
      item = Item.find_or_create_by(id: id)

      item.update(name: name, price: price)
    end
    redirect_to items_path

  end
```

Miejcie na uwadze, że to są proste operacje które wykonają się bardzo szybko, w przypadku bardziej skomplikowanych, bardziej czasochłonnych rzeczy powinniśmy pomyśleć o tym by nasze akcje działy się w background jobie

możemy wyedytować nasz plik i zobaczyć co się stanie gdy go wrzucimy

Na dzisiaj to już wszystko

Życzę miłego kodowania

[kod aplikacji](https://github.com/rubypopolsku/excel-example)
[caxlsx](https://github.com/caxlsx/caxlsx)
[caxlsx_rails](https://github.com/caxlsx/caxlsx_rails)
[roo](https://github.com/roo-rb/roo)
