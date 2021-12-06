---
layout: post
title:  "Maile w Ruby on Rails"
date:   2021-03-09 19:38:56 +0100
categories: ruby
---
{{< youtube  LRDhYsMAa_4 >}}

Cześć

w dzisiejszym odcinku chciałbym pokazać wam jak wysyłać maile. W poprzednim filmie pokazałem wam jak przygotować formularz. Więc dzisiaj chciałbym pokazać wam jak rozszerzyć jego funkcjonalność
<!--more-->

jeżeli nie oglądaliście poprzedniego filmu to oczywiście zachęcam, ale możecie sobie pobrać kod z tamtego odcinka z gita. W ten sposób będzie mieli ten sam kod który ja mam teraz.

[https://github.com/rubypopolsku/formularze](https://github.com/rubypopolsku/formularze)

`git clone [git@github.com](mailto:git@github.com):rubypopolsku/formularze.git`

Ruby on rails bardzo ułatwia nam tworzenie maili ponieważ posiada Action Mailer. A więc ładnie opakowana magie railsów do wysyłania maili

gdy mamy już nasz kod możemy użyć generatora

`rails generate mailer ContactMailer`

zajrzyjmy sobie do pliku `app/mailers/application_mailer.rb`

jest to domyślny plik wszystkich mail

```ruby
class ApplicationMailer < ActionMailer::Base
  default from: "from@example.com"
  layout 'mailer'
end
```

mamy w nim domyślny email i layout

nie wszystkie maile musimy wysyłać z tego samego adresu więc można to ustawiać per mailer, ale przeważnie jak raz to tutaj ustawimy to tak to już sobie żyje

przejdźmy więc do naszego mailera

```ruby
class ContactMailer < ApplicationMailer
end
```

domyślnie jest to pusta klasa, nie ma w nim nic

utwórzmy więc w nim nową metodę np contact_message

```ruby
def contact_message
end
```

w ten sposób mamy nowe metodę która absolutnie nie wie co ma robić

```ruby
def contact_message(email)
	mail(to: params[:email], subject: 'Wiadomość testowa')
end
```

teraz jeżeli gdziekolwiek w kodzie odpalimy

```ruby
ContactMailer.with(email: "foo@bar.dev").contact_message.deliver_now
```

wyśle email na email który podamy jako parametr. TYu

tyle, że owszem email wyśle ale nie mamy jeszcze jego treści. Po za tym miał nam wysyłać zawartość formularza.

Przejdźmy na chwilę do naszego kontrolera. Jak widzicie wszystkie informacje jakich potrzebujemy zapisywane są w `ContactMessage` a mail powinien być wysyłany po zapisie

dodajmy więc odpowiedni kod

```ruby
def create
    unless params[:contact_message][:terms] == "1"
      flash[:notice] = 'Musisz wyraźić zgodę na przetwarzanie danych'
    else
      @contact_message = ContactMessage.new(message_params)

      if @contact_message.save
				ContactMailer.contact_message(@contact_message).deliver_now
        flash[:notice] = 'Wiadomość zapisana'
      else
        flash[:notice] = 'Coś poszło nie tak'
      end
    end

    redirect_to new_contact_message_path
  end
```

po zapisaniu w bazie (tutaj już decyzja należy do was czy w ogóle jest sens taki formularz zapisywać w bazie) wysyła email

przejdźmy więc do naszego mailera który nie wie jeszcze nic o `@contact_message`

```ruby
def contact_message(message)
	@message = message
	mail(to: @message.email, subject: @message.title)
end
```

by móc korzystać z danych zapisanych w modelu ContactMessage musimy dodać znak małpy przed zmienna message wtedy widok będzie miał do tego dostęp.

No właśnie. Mamy już logikę wysyłania maila, ale nie mamy maila który będzie wysyłany.

Nasz generator utworzył nam nowy katalog w widokach ale musimy utworzyć sam widok

`contact_message.html.erb`

```ruby
<!DOCTYPE html>
<html>
  <head>
    <meta content='text/html; charset=UTF-8' http-equiv='Content-Type' />
  </head>
  <body>
		<p><%= @message.name %> o emailu <%= @message.email %> wysłał Ci wiadomość</p>
    <p><b><%= @message.title %></b></p>
		<p><%= @message.body %></p>
  </body>
</html>
```

dobrą praktyką jest dodanie takiego samego widoku w wersji tekstowej, dla klientów którzy nie używają htmla w mailach

stwórzmy więc `message.text.erb`

```ruby
<%= @message.name %> o emailu <%= @message.email %> wysłał Ci wiadomość
===============================================

<%= @message.title %>

<%= @message.body %>
```

### konfiguracja maili

Teoretycznie to wszystko, gdyby nie jedno małe ale. Musimy jeszcze dodać jakiś serwer pocztowy który te maile będzie wysyłać

jeżeli używacie gmaila to w `application.rb` możecie dodać konfiguracje

```ruby
config.action_mailer.delivery_method = :smtp
config.action_mailer.smtp_settings = {
  address:              'smtp.gmail.com',
  port:                 587,
  domain:               'example.com',
  user_name:            '<username>',
  password:             '<password>',
  authentication:       'plain',
  enable_starttls_auto: true
}
```

ALE!

taka konfiguracja będzie działać zarówno dla produkcji jak i naszego lokalnego środowiska a chyba nie chcemy wysłać maili podczas testów.

a sam gmail też nie jest najlepszym rozwiązaniem.

Przenieśmy więc ten konfig do naszego konfigu produkcyjnego

przenieśmy to do  `config/environments/production.rb`

teraz dodajmy gem `gem "letter_opener", :group => :development`

i w `config/environments/development.rb`

ustawmy

```
config.action_mailer.delivery_method = :letter_opener
config.action_mailer.perform_deliveries = true
```

dzięki temu wszystkie maile będą uruchamiane u nas lokalnie

a maile z produkcji będą wysyłane przez konto gmaila, które jak wspomniałem nie jest dobrym pomysłem

jeżeli używacie heroku to możecie użyć np mailguna który do 400 maili dziennie jest za darmo

[https://elements.heroku.com/addons/mailgun](https://elements.heroku.com/addons/mailgun)

sam konfig będzie wyglądał prawie tak samo

```ruby
ActionMailer::Base.smtp_settings = {
  :port           => ENV['MAILGUN_SMTP_PORT'],
  :address        => ENV['MAILGUN_SMTP_SERVER'],
  :user_name      => ENV['MAILGUN_SMTP_LOGIN'],
  :password       => ENV['MAILGUN_SMTP_PASSWORD'],
  :domain         => 'yourapp.heroku.com',
  :authentication => :plain,
}
ActionMailer::Base.delivery_method = :smtp
```

a jak dodacie wtyczkę wszystkie envy ustawią wam się samodzielnie

Na dzisiaj to już tyle

Dziękuje za oglądanie.
Miłego kodowania!

[Kod aplikacji](https://github.com/rubypopolsku/maile)
