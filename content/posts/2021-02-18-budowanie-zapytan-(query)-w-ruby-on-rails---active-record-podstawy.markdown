---
layout: post
title:  "Budowanie zapytań (query) w Ruby on Rails - Active Record podstawy"
date:   2021-02-18 19:38:56 +0100
categories: ruby
---
{{< youtube  NvuSk--uvFo >}}

Cześć

w dzisiejszym odcinku chciałbym wam opowiedzieć o Active Recordzie. W jednym z poprzednich odcinków opowiadałem już o jednym z elementów Active Recorda - walidacjach. Dzisiaj chciałbym pokazać wam jak budować zapytania.
<!--more-->

# czym jest active record?

Active Record odpowiada za literę M w  skrócie MVC czyli model. Jest to najpopularniejsze lecz nie jedyne narzędzie  ORM w railsach.

ORM to warstwa pomiędzy tabelami w bazie danych a obiektami w naszym kodzie. Korzystając z ORMa, można łatwo przechowywać i pobierać z bazy danych bez bezpośredniego pisania instrukcji SQL. Oczywiście znajomość SQLa jest przydatną umiejętnością. Szczególnie, że nie wszystkie przypadki uda nam się ogarnąć przy pomocy Active Record

Active record reprezentuje modele i ich dane. Oraz relacje między nimi, pomaga nam budować zapytania a ostatecznie dzięki niemu możemy pisać zdecydowanie mniej kodu.

# Nazewnictwo

Active Record przyjmuje kilka zasad jeżeli chodzi o nazewnistwo

Podtawowa, tabele w bazie danych występują w liczbie mnogiej. Np `posts`

ale model będzie się nazywać `Post`

jeżeli mamy bardziej złożone nazwy tabel np `post_categories` to model będzie pisany camel casem czyli `PostCategory`

# Używanie

To tyle teorii, pora zacząć używać

Domyślnie Active Record obsługuje wszystkie akcje związane z CRUDem. CRUD to skrów od Create, Read, Update i Delete i opisuje podstawowe akcje na naszych danych, czyli zapis, odczyt, aktualizacja i usuwanie.

## Tworzenie

chyba najważniejsza akcja, o ile nie korzystamy z jakieś bazy danych tylko do oczytu. To w jakiś sposób naszą bazę musimy zapełnić.

```html
Post.create(name: "Foo", body: "Bar")
```

metoda create tworzy nowy obiekt Post i zwraca nam go

ale możemy też utworzyć nowy obiekt bez zapisywania

np.

```html
post = Post.new
post.name = "Foo"
post.body = "Bar"
```

jeżeli byśmy chcieli zapisać nasz post należy użyć `post.save`

# Odczyt

to chyba najczęściej używany element. O tym jak wyciągać dane można mówić bez końca, wszystko zależy od tego co chcemy osiągnąć oczywiście. Pewnie nagram jeszcze nie jeden film na ten temat, może nawet nie powiązany z active recordem.

Ale pokaże wam kilka podstawowych rzeczy:

```html
Post.all
```

Zwraca wszystkie posty

```html
Post.first
Post.last
```

Zwraca pierwszy i ostatni post

```html
Post.first(3)
```

Zwraca pierwsze 3 (w podobny sposób można wyciągnąć ostatnie) posty

```html
Post.find_by(title: 'Foo')
```

Zwraca post o tytule "foo", jeżeli jest ich kilka zwraca pierwszy

Dlatego też można użyć

```html
Post.where(title: 'Foo')
```

wtedy zwróci wszystkie posty o tym tytule. Można tu tak samo zastosować `first` lub `last`

a na koniec zostawiłem najważniejsze czyli `find`

domyślna metoda która szuka po `id`

`Post.find(123)`

jeżeli nie znajdzie posta zwróci błąd

## Aktualizacja

Chyba nie muszę opowiadać o zastosowaniu aktualizacji, mamy post i chcemy coś zmienić

```html
post = Post.first
post.title = "nowy tytuł"
post.save
```

można też inaczej to zrobić

```html
Post.first.update(title: "nowy tytuł")
```

musicie pamiętać, że aktualizacja zwraca nam `true` jeżeli się powiedzie, więc jeżeli chcecie by metoda zwracała wam obiekt musicie go dodać na końcu np tak

```html
post = Post.first
post.title = "nowy tytuł"
post.save
post
```

## Kasowanie

Gdy już znajdziemy nasz obiekt możemy go skasować

`Post.find(123).destroy`

Możemy też skasować wszystkie dane w tabeli

`Post.destroy_all`

albo np jakiś zbiór danych `Post.where(title: 'Foo').destroy_all`

Oczywiście to co tutaj pokazałem to absolutnie minimum co możemy zrobić. Możemy dodawać joiny, możemy sprawdzać czy obiekty nie zawierają jakiś danych, możliwości jest naprawdę dużo i jak wspomniałem jeszcze wrócę do tego temu

Ale na chwilę obecną to wszystko co chciałem wam pokazać.

Dziękuje za oglądanie

Miłego kodowania!
