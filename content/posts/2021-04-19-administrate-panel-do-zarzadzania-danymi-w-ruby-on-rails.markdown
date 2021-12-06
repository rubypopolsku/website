---
layout: post
title:  "Administrate panel do zarządzania danymi w Ruby on Rails"
date:   2021-04-19 08:31:56 +0100
categories: ruby
---
{{< youtube  Fk_S6hTI0Vw >}}

Czasami, szczególnie gdy nasza aplikacja to tylko API dla jakiegoś frontendu potrzebujemy móc tymi danymi zarządzać. Np dodać nową kategorie. W takim celu nie ma sensu wymyślać koła na nowo, a już szczególnie gdy przygotowujemy tylko MVP. Możemy użyć gotowego panelu administracyjnego.

<!--more-->

Jest kilka gotowych rozwiązań dla naszego problemu np [https://activeadmin.info/](https://activeadmin.info/)  albo rails admin ale w tym nagraniu pokaże wam Administrate. By nie robić aplikacji od zera, posłużę się tu kodem z jednego z wcześniejszych odcinków.

na początek dodajmy nowy gem do naszego Gemfila

```ruby
...
gem "administrate"
```

odpalmy `bundle install`

teraz musimy uruchomić generator `rails generate administrate:install`

doda on nam nową ścieżkę w routerze oraz nowy kontrolery w `app/controllers/admin/`

zależnie od tego co mieliśmy do tej pory

przyjrzyjmy się temu co możemy znaleźć

po pierwsze

w `admin/applicaction_controller.rb` powinniśmy dodać jakieś zabezpieczenie, możemy użyć istniejących uzytkowników, możemy przygotować jakąś osobą autentykacje, np devise tylko dla adminów, może nowy model Admin zamiast user

a możemy po prostu zrobić basic auth

```ruby
module Admin
  class ApplicationController < Administrate::ApplicationController
		http_basic_authenticate_with(
			name: "user",
			password: "password",
		)

    # Override this value to specify the number of elements to display at a time
    # on index pages. Defaults to 20.
    # def records_per_page
    #   params[:per_page] || 20
    # end
  end
end
```

jeżeli mamy jednego admina, lub zmiany nie muszą być powiązane z konkretnym userem, powinno wystarczyć

jeżeli zajrzycie na [http://localhost:3000/admin/posts](http://localhost:3000/admin/posts) to możecie zobaczyć, że wyświetla nam się np autor posta, czy liczba komentarzy ale już nie tytuł. Moim zdaniem tytuł jest bardziej przydatną informacją. Na szczęście można łatwo to zmienić.

w `app/dashboards/post_dashboard.rb` mamy collection atributes i to tam możemy ustalić co ma się wyświetlać. Możemy do arraya dodać tytuł

```ruby
COLLECTION_ATTRIBUTES = %i[
    comments
    category
    user
    title
    id
  ].freeze
```

po odświeżeniu strony, tytuł będzie widoczny

Jeżeli dodacie nowy model do aplikacji i chcecie w łatwy sposób dodać go do panelu administracyjnego to wystarczy odpalić

`rails generate administrate:dashboard Nazwamodelu`

w ten sposób utworzymy nowy dashboard

Z racji, że administrate generuje nam normalne kontrolery, możemy też wygenerować widoki. Więc w łatwy sposób możemy wykorzystać go do bardzo custowych akcji. Np import produktów z excela. Ja nie mam zbyt skomplikowanej aplikacji więc dodam jakąś prostą akcje. Skasowanie wszystkich komentarzy. Bo dlaczego nie?

`rails generate administrate:views`

wygenerujemy sobie wszystkie widoki

teraz przejdźmy do kontrolera z komentarzami, kontrolera administratora

zróbmy sobie nową metode

```ruby
def destroy_all
  Comment.destroy_all
end
```

edytujmy router by nasza akcja mogła działać

```ruby
resources :comments do
	get :destroy_all, on: :collection
end
```

pozostaje nam jeszcze widok

wystarczy, że gdzieś w naszym widoku umieścimy

`<%= link_to "Usuń wszystko", controller: "comments", action: "destroy_all", class: "button" %>`

możemy przejść na [localhost:3000/admin/comments](http://localhost:3000/admin/comments) by zobaczyć efekt naszej pracy

Nie będę ukrywać, Administrate nie jest rozwiązaniem na każdy panel. Czasami potrzeba czegoś naprawdę customowego. Ale dla sporej ilości zastosowań, powinien wystarczyć

to tyle na dzisiaj

Miłego kodowania!
[Kod aplikacji](https://github.com/rubypopolsku/Administrate)
