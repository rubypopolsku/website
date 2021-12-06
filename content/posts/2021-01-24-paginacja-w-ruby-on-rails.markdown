---
layout: post
title:  "Paginacja w Ruby on Rails"
date:   2021-01-24 19:39:56 +0100
categories: ruby
---
{{< youtube GLGwGXJQcvM >}}

W tym odcinku chciałbym pokazać wam jak dodać paginacje do naszego modelu.

Oto przykładowa lista postów.

Naszym celem jest ograniczenie ilości postów do 5 na stronę i przełączanie się między stronami.

W Ruby on Rails dostępnych jest kilka gemów do paginacji. Najpopularniejsze z nich to [Kaminari](https://github.com/kaminari/kaminari) i [will paginate](https://github.com/mislav/will_paginate). Oba te gemy są regularnie aktualizowane. A wybór między nimi nie jest aż tak istotny. Oba robią to co mają robić. Ja użyje will paginate.

Do naszego gemfila musimy dodać gem

```bash
# paginate
gem 'will_paginate', '~> 3.1.0'
```

a w konsoli odpalić `bundle install`

teraz w modelu `Post` możemy dodać `self.per_page = 5`

co ograniczy nas do 5 postów na strone

Przejdźmy jeszcze do naszego kontrolera. W `posts_controller.rb` w akcji `index` gdzie wyciągamy z bazy nasze posty. Musimy dodać, a właściwie zamienić `Post.all` na `Post.paginate(page: params[:page])` w ten sposób jako parametr będziemy wysyłać stronę jaka nas interesuje.

Przejdźmy do naszej strony.

Jak widzicie postów jest tylko 5, ale nie ma możliwości przejścia dalej. Można oczywiście w adresie w przeglądarce wpisać [http://localhost:3000/posts?page=2](http://localhost:3000/posts?page=2) ale nie jest to wygodne rozwiązanie.

W widoku `posts/index.html.erb`

musimy dodać `<%= will_paginate @posts %>`

Jeżeli odświeżymy naszą stronę to możecie zobaczyć, że paginacja działa

To by było na tyle. Kolejny odcinek już wkrótce.

Miłego kodowania.

[Kod aplikacji](https://github.com/rubypopolsku/paginacja)
