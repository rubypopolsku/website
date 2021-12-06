---
layout: post
title:  "Nowa aplikacja w Ruby on Rails"
date:   2021-01-24 19:38:56 +0100
categories: ruby
---

{{< youtube 58-LoPYjGeU >}}

W tym odcinku chciałbym pokazać wam jak łatwo i szybko można stworzyć aplikacje w ruby on rails

Nasz Projekt to będzie podstawowy blog który pozwoli nam stworzyć, edytować i usuwać posty. Więc zaczynajmy

`rails new blog`

Wchodzimy do katalogu z aplikacja

`cd blog`

Otwórzmy kod naszej aplikacji w edytorze

Ruby on Rails domyślnie używa sqlite, i tak też tu zostanie, ale w większości przypadków wypadało by to zmienić na inną bazę, np postgres

Musimy utworzyć naszą bazę danych wiec wróćmy do terminala

`rails db:create`

Baza danych została utworzona.

Teraz możemy uruchomić nasz blog `rails server`

w przeglądarce możemy uruchomić [localhost:3000](http://localhost:3000), i powinien nam się pojawić ekran powitalny.

Następnym krokiem jest utworzenie postów.

Ruby on rails przy użyciu generatorów pozwala nam utworzyć praktycznie cały kod za nas, zarówno widoki html, jak i kontroler, model czy migracje bazy danych.

Wróćmy do terminala

`rails generate scaffold Post title:string body:text`

Post to nazwa naszego modelu, jednocześnie utworzy nam tabele, o nazwie posts

Następnie podajemy atrybuty naszego modelu, tytuł który będzie stringiem i body które będzie tekstem

Generator uaktualni też nasz router

Przyjrzyjmy się zmianą w plikach

Jak widzicie powstał kontroler ze wszystkimi interesującymi nas akcjami

Jest też model

A w przypadku widoków każda akcja z kontrolera ma przypisany do siebie widok

Teraz musimy odpalić migracje

`rails db:migrate`

tabela posts została utworzona

Odpalmy ponownie nasz serwer `rails server`

Jeżeli wejdziemy na [http://localhost:3000/posts](http://localhost:3000/posts) otworzy nam się widok naszych postów

Utwórzmy nowy post

I to by było na tyle. Kolejny odcinek już wkrótce.

Miłego kodowania.

[Kod aplikacji](https://github.com/rubypopolsku/nowa-aplikacja-w-ruby-on-rails)
