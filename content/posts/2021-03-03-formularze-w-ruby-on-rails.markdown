---
layout: post
title:  "Formularze w Ruby on Rails"
date:   2021-03-03 19:38:56 +0100
categories: ruby
---
{{< youtube  yPRRTd-EtXc >}}

Cześć

w dzisiejszym odcinku chciałbym pokazać wam jak zbudować prosty formularz do zapisywania danych w ruby on rails.
<!--more-->

Na potrzeby tego wideo nie będę tych danych nigdzie wyświetlać potrzebujemy je tylko zapisać.

W kontrolerze, nazwijmy go `ContactMessagesController` potrzebujemy mieć metodę `new` oraz `create` . New to miejsce, gdzie router prowadzi do naszego formularza, create będzie zapisywać nasze dane.

Jeżeli tak jak ja dopiero utworzyliście ten controler, nie zapomnijcie o routerze.

`resources :contact_messages, only: %i(new create)`

w ten sposób dostępne będą tylko dwa adresy

teraz potrzebujemy utworzyć widok dla naszego formularza

w katalogu `views` musimy utworzyć `contact_messages` a w nim plik `new.html.erb` erb to html rozszerzony o kod w języku ruby (po za erb popularnymi formatami jest jeszcze haml i slim, bez wątpienia przyspieszają one pisanie widoków, jeden i drugi format ma swoich zwolenników, polecam poszukać)

jak wynika z nazwy to będzie formularz dla jakiś kontaktowych wiadomości

najprostsze co możemy zrobić to coś takiego

```ruby
<h1>Zostaw wiadomość</h1>

<%= form_with model: @contact_message do |form| %>
	<div>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
</div>

  <div>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
</div>

  <div>
    <%= form.submit %></div>
<% end %>
```

nasz formularz będzie miał tytuł i treść

wróćmy teraz do naszego controllera, musimy zmienić pustą metodę new na:

```ruby
def new
	@contact_message = ContactMessage.new
end
```

oraz create

```ruby
def create
	@contact_message = ContactMessage.new(message_params)
  redirect_to new_contact_message_path
end
```

z racji tego, że nie mamy metody show, po utworzeniu wiadomości i tak będzie przenosić do formularza

potrzebujemy jeszcze utworzyć `message_params`

```ruby
private

    def message_params
      params.require(:contact_message).permit(:title, :body)
    end
```

jeżeli włączymy serwer i przejdziemy na [localhost:3000/contact_messages/new](http://localhost:3000/contact_messages/new) to zobaczymy nasz formularz, problem jest tylko taki, że nie mamy jeszcze modelu o nazwie `ContactMessage`

zanim go utworzę chciałbym jeszcze kilka rzeczy zmienić w naszym formualrzu

```ruby

<h1>Zostaw wiadomość</h1>

<%= form_with model: @contact_message do |form| %>
	<div>
    <%= form.label :name %><br>
    <%= form.text_field :name %>
	</div>
	<div>
    <%= form.label :email %><br>
    <%= form.email_field :email %>
	</div>
	<div>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
</div>

  <div>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
</div>

<div>
	<%= form.check_box :terms %>
	<%= form.label :terms, "Akceptuje regulamin, zgadzam się na przetwarzanie danych" %>
</div>

  <div>
    <%= form.submit %></div>
<% end %>
```

po pierwsze przydało by się dodać jakieś imię osoby kontatkowej

po drugie jeżeli chcemy jej odpisać przydał by się jej email, form ma helper `email_field` który waliduje czy wpisywana wartość to email. A na koniec jeszcze dodajmy checkbox, że nasz kontaktujący zgadza się na przetwarzanie danych. Bo bez tego to w sumie nie powinniśmy tych danych zapisywać.

teraz w kontrolerze musimy zmienić nasze parametry

```ruby
private

    def message_params
      params.require(:contact_message).permit(:name, :email, :title, :body)
    end
```

w metodzie create możemy jeszcze zmienić tak:

```ruby
def create
	unless params[:contact_message][:terms] == "1"
      flash[:notice] = 'Musisz wyraźić zgodę na przetwarzanie danych'
    else
      @contact_message = ContactMessage.new(message_params)

      if @contact_message.save
        flash[:notice] = 'Wiadomość zapisana'
      else
        flash[:notice] = 'Coś poszło nie tak'
      end
    end

    redirect_to new_contact_message_path
end
```

w ten sposób powiadomimy usera o tym co się dzieje

a na górze naszego layoutu musimy dodać

```ruby
<% flash.each do |name, msg| %>
      <%= content_tag :div, msg, class: name %>
    <% end %>
```

teraz już wszystko będzie ok, nie pozostaje nam nic innego jak utworzenie modelu

możemy się posłużyć generatorem

`rails g model ContactMessage name:string email:string title:string body:text`

możecie zauważyć, że nie mamy pola terms, bo nie jest nam potrzebne nie jest to informacja która zapisujemy w bazie danych

Pamiętajcie o migracji `rails db:migrate`

by nie było problemów z naszymi danymi w modelu warto dodać walidacje

```ruby
validates :name, presence: true
validates :email, presence: true
validates :title, presence: true
validates :body, presence: true
```

teraz możemy wrócić do naszego formularza [localhost:3000/contact_messages/new](http://localhost:3000/contact_messages/new) i go zapisać

zobaczmy czy warunek z terms działa

zapiszmy dane

i na koniec sprawdźmy czy rzeczywiście tam są

`rails c`

`ContactMessage.last`

To by było na tyle, w następnym odcinku pokaże wam jak po za zapisaniem tego w bazie danych wysłać ten formularz na maila

Miłego kodowania!

[Kod aplikacji](https://github.com/rubypopolsku/formularze)
