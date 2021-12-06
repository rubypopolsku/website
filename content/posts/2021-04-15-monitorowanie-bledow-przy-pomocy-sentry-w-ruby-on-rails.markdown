---
layout: post
title:  "Monitorowanie błędów przy pomocy sentry w Ruby on Rails"
date:   2021-04-15 08:31:56 +0100
categories: ruby
---
{{< youtube  hHUz9WnKo60 >}}

Mimo naszych największych starań nie jesteśmy w stanie sprawdzić czy przewidzieć wszystkiego. To, że coś działa lokalnie nie znaczy, że będzie działać na produkcji. Za chwilę przyjdzie użytkownik który będzie używał tego ficzera w sposób zupełnie odmienny od tego który planowaliśmy. Upload plików excela? spoko ale user wrzuca dokument worda. Po co? Pewnie tam też ma tabelkę.

Dlatego bardzo ważnym jest monitorowanie tych błędów. Tak by o nich wiedzieć.

<!--more-->

załóżmy, że nasz formularz ma dropdown, z dwoma opcjami do wyboru

```json
params[:foo]
when 1
  jakas_akcja
when 2
  jakas_inna_akcja
end
```

czy taki kod jest poprawny? Oczywiście. Czy wydarzy się coś złego? Pewnie nie. Ale co jak foo będzie 3? Jeżeli dalsza część kodu nie nawiązuje do tego, pewnie nic, może następna metoda nie będzie zawierać jakiś danych ale zamiast tego lepiej dodać błąd

```ruby
params[:foo]
when 1
  jakas_akcja
when 2
  jakas_inna_akcja
else
	raise "zły parametr"
end
```

po pierwsze teraz użytkownik będzie wiedział, że coś poszło nie tak. Ale po za użytkownikiem to my mamy wiedzieć, że coś jest źle.

Najprostsze, ale nie najlepsze to wysłanie tego na maila

możemy do tego użyć gema `exception_notification`

podstawowy konfig wygląda tak

```ruby
Rails.application.config.middleware.use ExceptionNotification::Rack,
  email: {
    email_prefix: '[PREFIX] ',
    sender_address: %{"notifier" <notifier@example.com>},
    exception_recipients: %w{exceptions@example.com}
  }
```

a o ile skonfigurowaliście już action mailera to nic więcej nie musicie robić. Jak nie, to nagrałem odcinek o tym.

Jak możecie się domyślić wysyłanie maili o błędach nie jest najlepszym pomysłem. Wyobraźcie sobie, że macie kilka lub kilkanaście tysięcy użytkowników i jest błąd w jakimś popularnym miejscu waszej aplikacji. Kilka tysięcy maili dostaniecie i to tylko o jednym błędzie.

zamiast tego lepiej użyć zewnętrznego serwisu do obsługi błędów.

W projektach przy których pracowałem najczęściej wybierany jest bugsnag albo sentry

W tym filmie pokaże wam jak używać sentry. Ale konfiguracja bugsnaga jest równie prosta

na początek musimy dodać dwa gemy do naszego gemfila

```ruby
gem "sentry-ruby"
gem "sentry-rails"
```

teraz trzeba utworzyć nowy plik `config/initializers/sentry.rb`

a w nim

```ruby
Sentry.init do |config|
  config.dsn = 'https://<key>@sentry.io/<project>'
  config.breadcrumbs_logger = [:active_support_logger]

# To activate performance monitoring, set one of these options.# We recommend adjusting the value in production:
  config.traces_sample_rate = 0.5
# or
  config.traces_sampler = lambda do |context|
    true
  end
end
```

pamiętajcie by zastąpić `config.dsn` swoim kluczem z sentry

skąd go wziąć?

jeżeli nie macie jeszcze konta na sentry to należy owo konto utworzyć

w zakładce "Projects" należy utworzyć nowy projekt

wybieracie jakiś język wasz interesuje, jak macie być powiadomieni o błędach no i nazwę projektu należy wpisać

po utworzeniu projektu zobaczycie jeszcze instrukcje taką samą jak moja.

Od teraz możecie zbierać błędy. Mogło wam też wpaść w oko, że sentry po za błędami monitoruje też wydajność aplikacji. Ale o tym nagram inny odcinek.

Chcecie sprawdzić czy sentry działa?
dodajmy jakiś wyjątek

np w metodzie index na samym początek dodajmy

```ruby
def index
	raise "test"
	...
end
```

jak wejdziemy nasz widok, zobaczymy błąd, a po chwili ten sam błąd pojawi się na sentry

możemy zobaczyć co to za błąd, gdzie wystąpił i trochę danych o środowisku na którym się wydarzył

jeżeli nie chcecie dostawać powiadomień o jakimś błędzie, możecie go zignorować, ale chyba nie powinniście tego robić

jak już naprawicie (albo wydaje wam się, że naprawiliście) błąd możecie kliknąć resolve

błąd zniknie z waszej listy, ale dostaniemy powiadomienie o tym, że pojawił się na nowo

a co do powiadomień

to po za mailem - tym razem już tylko o pojedynczym przypadku, to ile razy error miał miejsce możecie zobaczyć na sentry. Możecie ustawić powiadomienia na wielu popularnych platformach. Slack, Teams i dużo dużo więcej

to tyle na dzisiaj

mam nadzieje, że od teraz będziecie monitorować błędy w swoich aplikacjach

Miłego kodowania!
