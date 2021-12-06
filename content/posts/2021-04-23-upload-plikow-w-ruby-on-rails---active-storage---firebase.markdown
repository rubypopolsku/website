---
layout: post
title:  "Upload plików w Ruby on Rails - Active Storage - Firebase"
date:   2021-04-23 08:31:56 +0100
categories: ruby
---
{{< youtube  SU1rH5_Zi-A >}}

Upload plików to teoretycznie prosta sprawa, wystarczy input w htmlu.
Ale musimy te pliki gdzieś zapisać. W ruby on rails na pomoc przychodzi Active Storage. Dzięki niemu możemy dołączać pliki do naszych obiektów active recorda. Pliki te możemy przetrzymywać w chmurze np amazon s3 czy azure storage od microsoftu. Korzystając z Active Storage. Po przesłaniu pliku graficznego możemy konwertować je do innych formatów. Ale też robić dużo innych czynności nie tylko związanych z obrazkami.

<!--more-->

jeżeli chcecie używać active storage najpierw trzeba dodać go do naszej aplikacji

`rails active_storage:install`

utworzy to między innymi wcześniej wspomniane tabele

następnie trzeb uruchomić migrację

`rails db:migrate`

w pliku `config/storage.yml`

możemy ustawić różnych providerów do trzymania danych, nie musimy się ograczniać do jednego. Możemy dane trzymać zarówno na dysku jak i np na s3. Możemy też wybierać providera  zależnie od tego gdzie nasza aplikacja jest uruchomiona, np na produkcji używać s3 a lokalnie czy przy testach dysku.

na chwile obecną wystarczy nam dysk

```ruby
test:
  service: Disk
  root: <%= Rails.root.join("tmp/storage") %>

local:
  service: Disk
  root: <%= Rails.root.join("storage") %>
```

do providerów jeszcze wrócę

Jeżeli zastanawiacie się po co trzymać pliki po za serwerem już spieszę z odpowiedzią.

- po pierwsze na heroku które najczęsciej tu pokazuje pliki kasują się z każdym deploymentem. Więc o ile nie są to pliki tymczasowe musicie trzymać je po za heroku
- po drugie - skalowanie. Jeżeli chcecie mieć odpaloną maszynę na kilku urządzeniach, pliki muszą być w jednym miejscu
- po trzecie - cena
to chyba w tym momencie bardzo istotne, usługi do trzymania plików są bardzo tanie. Jeżeli weźmiemy jakiegoś vpsa np digitalocen to owszem wraz z wzrostem ceny rośnie też dostępne miejsce, ale często rośnie nam zapotrzebowanie na miejsce, nie na resztę zasobów. Po co płacić za 16gb ram jeżeli wykorzystujemy tylko 2gb? Po za tym na przykładzie digitalocean, za 80$ na miesiąc mamy tylko 320GB . Dla porównania na tym samym digitalocean dostępna jest usługa spaces. Gdzie 250gb kosztuje tylko 5$.
Dodatkowo takie usługi płatne są tylko za miejsce które wykorzystujecie. No i transfer.

To chyba najważniejsze. Ostatecznie jest taniej, szybciej i bezpieczniej.
Ale jeżeli używacie innej usługi niż heroku (np jakiegoś vpsa) oraz nie planujecie trzymać wielu plików, nic nie stoi na przeszkodzie by trzymać te pliki lokalnie czyli na dysku

Nie zależnie co wybierzecie jako miejsce trzymania plików reszta konfiguracji wygląda podobnie.

Pora zacząć trzymać jakieś pliki. Wykorzystam tu kod z jednego z wcześniejszych filmów.

do modelu `Post` dodajmy `has_one_attached :cover_image`

doda to załącznik do modelu, i relacje do wcześniej utworzonej tabeli. Ponieważ nie ważne ile modeli  z załącznikami będziemy mieć. Wszystkie informacje o nich będą trzymane właśnie we wcześniej utworzonej tabeli.

w widoku formularza od naszego posta dodajmy

```ruby
	<div class="field">
    <%= form.label "Obrazek" %>
    <%= form.file_field :cover_image %>
  </div>
```

musimy jeszcze w kontrolerze zaaakceptować nowy parametr

```ruby
def post_params
  params.require(:post).permit(:title, :body, :category_id, :cover_image)
end
```

skoro mamy już obrazek to jeszcze trzeba go wyświetlić

przejdźmy do widoku naszego posta

dodajmy `<%= image_tag @post.cover_image %>`

railsy mają helper `image_tag` który odpowiada za wyświetlanie obrazków

to tyle jeżeli chodzi o obrazki

ale teraz gdzieś musimy je trzymać. Lokalnie nie ma problemu - dysk. Ale co z heroku.

Ja osobiście polecam firebase, jest usługa należąca teraz do googla. Trochę jest to taki uproszczony google cloud. 5gb przestrzeni macie do wykorzystania. Z Punktu widzenia konfiguracji to też google cloud

zajrzyjmy do naszego `storage.yml`

i dodajmy nowego providera - firebase

```ruby
firebase:
  service: GCS
  credentials: <%= Rails.root.join("firebase.json") %>
  project: "sampleproject"
  bucket: "sampleproject.appspot.com"
```

`project` to nazwa naszego projektu a `bucket` to project + [appspot.com](http://appspot.com)

a plik z credentials za chwilę pokaże wam skąd pobrać

a do gemfila dodajmy

```ruby
...
gem "google-cloud-storage", "~> 1.11", require: false
```

teraz potrzebujemy jeszcze projektu na firebase

- utwórzmy nowy projekt
- przejdźmy do zakładki storage i dokończmy setup
najważniejsze to ustawić gdzie pliki będą trzymane (nie można tego później zmienić)
- przejdźmy do zakładki settings a następnie service accounts
- wygenerujmy nowy klucz i umieśćmy zawartośc pliku w naszym repozytorium

pamiętajcie by plik firebase.json dodać do `.gitignore` w przeciwnym wypadku wasze dane zapiszą się w repozytirum i mogą przypadkowo wyciec. Lepiej tego uniknąć.

w pliku `/enviroments/productions.rb` dodajmy `config.active_storage.service = :`firebase by nasza aplikacja na produkcji korzystała z firebasea

jeżeli to wszytsko ma być trzymane na heroku to plik będzie problemem ale możemy lekko zmienić konfig

```ruby
firebase:
  service: GCS
  credentials:
    type: <%= ENV["gsc_type"] %>
    project_id: <%= ENV["gsc_project_id"] %>
    private_key_id: <%= ENV["private_key_id"] %>
    private_key: <%= ENV["gsc_private_key"] %>
    client_email: <%= ENV["gsc_client_email"] %>
    client_id: <%= ENV["gsc_client_id"] %>
    auth_uri: "https://accounts.google.com/o/oauth2/auth"
    token_uri: "https://accounts.google.com/o/oauth2/token"
    auth_provider_x509_cert_url: "https://www.googleapis.com/oauth2/v1/certs"
    client_x509_cert_url: <%= ENV["gsc_client_x509_cert_url"] %>
  project: <%= ENV["gsc_project"] %>
  bucket: <%= ENV["gsc_bucket"] %>
```

i każdy klucz dodawać ręcznie

na koniec zostanie nam wrzucenie naszej aplikacji na heroku

i dodanie wszystkich potrzebnych kluczy

na dzisiaj to już wszystko
oczywiście active storage to narzędzie wielu możliwości i pewnie w wielu filmach będę do niego wracać

dziękuje za ogładanie

Miłego kodowania!
