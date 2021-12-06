---
layout: post
title:  "Walidacje modelu w Ruby on Rails"
date:   2021-02-04 19:38:56 +0100
categories: ruby
---
{{< youtube ZgXWRZQ-J6w >}}

Cześć
w dzisiejszym odcinku chciałbym przedstawić wam temat walidacji. A konkretnie pokazać wam jak dodać walidacje do modelu. Walidacja ma na celu sprawdzenie stanu obiektu zanim trafi on do bazy. Ruby on Rails używa domyślnie Active Record Validations. Po za nimi istnieje kilka innych rozwiązań na walidacje, możemy też oczywiście zrobić coś własnego.
<!--more-->

```ruby
def foo(arg)
	if arg.is_a?(String)
		return arg
	else
		raise StandardError.new "Is not a string"
	end
end
```

Bądźmy szczerzy, nikt nie będzie pisał czegoś takiego dla każdej metody w kodzie

Jak już wspomniałem, railsy używają Active Record Validations i właśnie temu rozwiązaniu chcę poświęcić dzisiejszy odcinek.

# Dlaczego używać walidacji?

Walidacje służą do weryfikacji czy w bazie danych są zapisywane tylko prawidłowe dane. Przykładowo podczas rejestracji istotnym jest by użytkownik zawsze podał email i hasło. Albo post zawierał tytuł i treść. Walidacje na poziomie modelu to dobry sposób na zapewnienie, że w bazie danych są zapisywane tylko prawidłowe dane. Są niezależne od bazy danych, oraz są wygodne w testowaniu i utrzymaniu.

popatrzmy na przykład

jeżeli zechcemy dodać nowy post, ale nie uzupełnimy żadnych danych, post zapisze nam się z pustymi polami.

Jeżeli dodamy walidacje np

```ruby
class Post
	validates :title, presence: true
	validates :body, presence: true
end
```

to teraz nie da się zapisać posta bez uzupełniania tych pól

# Rodzaje walidacji

Active Record oferuje bardzo dużo gotowych typów walidacji. Możemy dodać wiele typów walidacji i będą one działać wspólnie. Za każdym razem, gdy walidacja się nie powiedzie, do tablicy z błędami obiektu dodawany jest błąd.

Tak jak wcześniejszy przykład z postem

```ruby
post = Post.create
post.errors
```

zwróci nam

```ruby
#<ActiveModel::Errors:0x00007ff04f3d2718 @base=#<Post id: nil, title: nil, body: nil, created_at: nil, updated_at: nil, slug: nil>, @errors=[#<ActiveModel::Error attribute=title, type=blank, options={}>, #<ActiveModel::Error attribute=body, type=blank, options={}>]>
```

Przyjrzyjmy się niektórym z rodzajów walidacji

### validates_associated

Tego rodzaju walidacji powinniśmy używać gdy nasz model jest powiązany z innym modelem. Np w przypadku posta, może on wymagać posiadania kategorii. Bez niej nie powinno dojść do zapisu

```ruby
class Post
	has_many :categories
	validates_associated :categories
end
```



### confirmation

Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól powinno zawierać taką samą treść jak inne. Np email, lub hasło podczas rejestracji wtedy nazwa takiego pola powinna się nazywać `*_confirmation`

Moim zdaniem tego typu walidacja powinna odbywać się na etapie formularza więc przejdę dalej

```ruby
class Person < ApplicationRecord
	validates :email, confirmation: true
end
```

### exclusion

Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól nie powinno zawierać jakieś treści. Np.  Tworząc subdomeny nie chcemy by ktoś podał `www` lub innych używanych przez nas treści wtedy możemy dodać tego typu walidacje.

```ruby
class Account < ApplicationRecord
  validates :subdomain, exclusion: { in: %w(www us ca jp),
    message: "%{value} is reserved." }
end
```

### inclusion

Przeciwieństwo poprzedniego przykładu. Jednocześnie częściej używany. Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól musi zawierać jakieś treści. Np rozmiary produktów, płeć, województwo, coś co występuje w kilku wersjach ale nie ma sensu trzymać tego w osobnych tabelach.

```ruby
class Coffee < ApplicationRecord
  validates :size, inclusion: { in: %w(small medium large),
    message: "%{value} is not a valid size" }
end
```

### format

Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól jak sama nazwa wskazuje powinno mieć jakiś format. Np nie zawierać cyfr, zaczynać się z wielkiej litery. Itp.
Walidacja do tego celu używa wyrażeń regularnych, często nazywanych regexem

```ruby
class Product < ApplicationRecord
  validates :legacy_code, format: { with: /\A[a-zA-Z]+\z/,
    message: "only allows letters" }
end
```

### length

Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól powinno mieć jakiś limit znaków. Np minimalna treść posta, czy komentarza. W drugą stronę przeważnie ogranicza nas limit bazy danych. Ale gdybyśmy chcieli mieć limit jak na twiterze oczywiście możemy  to zrobić .
Limit może być minimalny, maksymalny, można połączyć oba czyli od do, ale też można podać jakąś konkretną wartość

```ruby
class Person < ApplicationRecord
  validates :name, length: { minimum: 2 }
  validates :bio, length: { maximum: 500 }
  validates :password, length: { in: 6..20 }
  validates :registration_number, length: { is: 6 }
end
```

### numericality

Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól powinno zawierać same liczby. Taką walidacje można rozszerzyć o dodatkowe warunki np `only_integer` będzie sprawdzać czy pole zawiera tylko liczby całkowite. Ale tych warunków jest dużo więcej

```ruby
class Player < ApplicationRecord
  validates :points, numericality: true
  validates :games_played, numericality: { only_integer: true }
  validates :games_played, numericality: { greater_than: 1 }
  validates :games_played, numericality: { greater_than_or_equal_to: 1 }
  validates :games_played, numericality: { equal_to: 1 }
  validates :games_played, numericality: { less_than: 2 }
  validates :games_played, numericality: { less_than_or_equal_to: 2 }
  validates :games_played, numericality: { other_than: 0 }
  validates :games_played, numericality: { odd: true }
	validates :games_played, numericality: { even: true }
end
```

### presence

Najpopularniejsza chyba walidacja. Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól  jest wymagane. Tak jak w przykładzie, nie powinniśmy móc utworzyć obiektu bez nazwy, loginu i emaila

```ruby
class Person < ApplicationRecord
  validates :name, :login, :email, presence: true
end
```

### uniqueness

Tego rodzaju walidacji powinniśmy używać gdy jedno z naszych pól powinno być unikalne. Np emaile użytkowników. Ale też można łączyć to w grupy

```ruby
class Account < ApplicationRecord
  validates :email, uniqueness: true
end
```

np nazwa musi być unikalna ale tylko w danym roku więc `name: foo, year: 2000`

oraz `name: foo, year:2001` powinno przejść walidacje

```
class Holiday < ApplicationRecord
  validates :name, uniqueness: { scope: :year,
    message: "should happen once per year" }
end
```

### validates_with

w tym przypadku możemy użyć oddzielnej klasy do walidacji. Musicie pamiętać, by zwracać tam error bo domyślnie go nie ma. Jeżeli tej samej walidacji chcecie użyć w kilku modelach takie rozwiązanie ma sens, w innym wypadku można zrobić osobną metodę wewnątrz modelu

```ruby
class GoodnessValidator < ActiveModel::Validator
  def validate(record)
    if record.first_name == "Evil"
      record.errors.add :base, "This person is evil"
    end
  end
end

class Person < ApplicationRecord
  validates_with GoodnessValidator
end
```

## Dodatkowe opcje walidacji

### allow_nil

pomija walidacje, jeżeli pole jest nilem

```ruby
class Coffee < ApplicationRecord
  validates :size, inclusion: { in: %w(small medium large),
    message: "%{value} is not a valid size" }, allow_nil: true
end
```

### allow_blank

używa helpera `.blank?` w porównaniu do nila pozwala na zapisanie pustego stringa

```ruby
class Topic < ApplicationRecord
  validates :title, length: { is: 5 }, allow_blank: true
end
```

### message

Wszystkie wcześniej wymienione walidacje mają domyślnie swoje zdefiniowane wiadomości w przypadku errora. jeżeli dodamy parametr `message` możemy stworzyć własną wiadomość

```ruby
class Person < ApplicationRecord
  validates :name, presence: { message: "must be given please" }

  validates :age, numericality: { message: "%{value} seems wrong" }

  validates :username,
    uniqueness: {
      message: ->(object, data) do
        "Hey #{object.name}, #{data[:value]} is already taken."
      end
    }
end
```

### on

bardzo fajny parametr, możemy ustawić osobne walidacje podczas tworzenia a osobne podczas aktualizacji obiektu

podczas tworzenia sprawdza, email czy jest unikalny ale podczas aktualizacji pozwoli na duplikaty, ale podczas tworzenia można podać dowolny wiek, jednak przy aktualizacji musi być liczbą. Walidacja pola name,  będzie się zawsze odbywać

```ruby
class Person < ApplicationRecord
  validates :email, uniqueness: true, on: :create

  validates :age, numericality: true, on: :update

  validates :name, presence: true
end
```

## if/unless

możemy dodać `if` do naszej walidacji i odpalać ją tylko gdy jakiś warunek jest spełniony. Tutaj dla przykładu sprawdza numer karty tylko gdy wybrana płatność to płatność kartą

```ruby
class Order < ApplicationRecord
  validates :card_number, presence: true, if: :paid_with_card?

  def paid_with_card?
    payment_type == "card"
  end
end
```

takie warunki można też grupować

np user admin musi mieć inne warunki niż zwykły user

```ruby
class User < ApplicationRecord
  with_options if: :is_admin? do |admin|
		admin.validates :password, length: { minimum: 10 }
    admin.validates :email, presence: true
	end
end
```

## własna metoda

wcześniej pokazywałem walidacje przy użyciu osobnej klasy. Ale można to też zrobić metodą wewnątrz modelu

```ruby
class Invoice < ApplicationRecord
  validate :active_customer

  def active_customer
    errors.add(:customer_id, "is not active") unless customer.active?
  end
end
```

# Pominięcie walidacji

czasami zdarza się, że musimy zignorować walidjacje. Z jednej strony skoro z jakiegoś powodu ją dodaliśmy to najprawdopodobniej nie powinniśmy tego robić. No ale różne są przypadki.

w takiej sytuacji podczas zapisywania wystarczy dodać argument `validate: false`

np.

```ruby
post = Post.new(body: "bar")
post.save(validate: false)
```

w tym przypadku zapisze nam post bez tytułu

# Errory

W idealnym świecie walidacje zawsze przechodzą i w ogóle nie powinniśmy się przejmować errorami. Ale potem przychodzą użytkownicy i pojawiają się błędy których nawet się nie spodziewaliśmy. O takich opowiem innym razem. Gdy już nasza walidacja zwróci error, musimy ją jakoś obsłużyć.

Jeżeli railsów używamy jako API to w zwrotce do klienta po prostu zwracamy błędy, oraz odpowiedni kod. Jeżeli widoki są po stronie railsów to trzeba te errory wyświetlic.

w przypadku API może to wyglądać tak

```ruby
def create
	post = Post.create(title: prams[:title], body: params[:body])
	if post.success
	  render json: { status: "Ok" }
  else
		render json: { status: "Error", message: post.errors }, status: :bad_request
  end
end
```

w przypadku tradycyjnych widoków musimy dodać obsługę błędów. Oczywiście możemy to dowolnie ostylować

```ruby
<% if @article.errors.any? %>
  <div id="error_explanation">
    <h2><%= pluralize(@article.errors.count, "error") %> prohibited this article from being saved:</h2>

    <ul>
      <% @article.errors.each do |error| %>
        <li><%= error.full_message %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

To by było na tyle. Mam nadzieje, że temat walidacji jest teraz dla was bardziej zrozumiały. Tak jak wspomniałem po za active recordem można inaczej zrobić walidacje, np przy pomocy [dry-validation](https://dry-rb.org/gems/dry-validation/1.6/) który przy bardziej skomplikowanych aplikacjach będzie lepszym rozwiązaniem. Ale w sporej ilości przypadków to co oferuje active record jest wystarczające. Gdybyście mieli jakieś pytania zachęcam do zostawienia komentarza. Dziękuje za oglądanie i życzę miłego kodowania.
