---
layout: post
title:  "Worker - Background Job - Active Job - Ruby on Rails"
date:   2021-03-09 19:38:56 +0100
categories: ruby
---
{{< youtube  3kY2SE_Xfj8 >}}

Hej

W dzisiejszym odcinku chciałbym poruszyć niezwykle istotny temat jakim są background joby. W poprzednim odcinku pokazywałem wam jak wysyłać maile, ale w tamtym przypadku musimy czekać na zakończenie wysyłania by dostać odpowiedź z serwera. Dodatkowo do maila może być generowany jakiś duży załącznik więc lepiej by takie rzeczy działy się w tle.

W tytule jest jeszcze jedno hasło, active job. Kolejny raz Railsy mają już gotowe rozwiązanie na popularny problem. Active Job nie tylko odpowiada za pracę w tlę ale też pracę kolejek. Można też ustawić wykonanie workera o danym czasie. Np powiadomienia mailowe o północy.

Ale tak jak w przypadku ActiveRecorda gdzie ten jest warstwą abstrakcji na bazie danych tak tutaj pod spodem musi działać jakiś inny gem np sidekiq Delayed Job albo Resque.
<!--more-->
# Tworzenie jobów

jeżeli na pierwszy przykład weźmiemy maila z poprzedniego odcinka to wystarczy zmienić

```ruby
	ContactMailer.contact_message(@contact_message).deliver_now

```

na

```ruby
	ContactMailer.contact_message(@contact_message).deliver_later

```

wtedy z automatu mail trafi na kolejkę. A akcja naszego kontrolera  wykona się niezależnie od tego czy mail się wyślę czy nie.

zróbmy jeszcze jeden przykład. Tym razem utwórzmy joba

do tego celu użyjemy gneratora z railsów

`rails generate job messages_cleanup`

utworzy to nam plik który wygląda tak

```ruby
class MessagesCleanupJob < ApplicationJob
  queue_as :default

  def perform(*guests)
    # Do something later
  end
end
```

zmieńmy go trochę by rzeczywiście coś zrobił

```ruby
class MessagesCleanupJob < ApplicationJob
  queue_as :default

  def perform(message)
    message.delete
  end
end
```

w ten sposób jako argument przyjmuje message, który jest nasza wiadomością z kontrolera, następnie usuwa wiadomość. Zastanawiacie się może po co usuwać wiadomość.

przejdźmy więc do kontrolera

po wysłaniu maila mogli byśmy od razu odpalić taką komende

```ruby
MessagesCleanupJob.perform_later(@contact_message)
```

wtedy od razu czyszczenie trafi na kolejkę, w przypadku tak małej aplikacji jak nasza wykona się to natychmiastowo

ale możemy to zmienić na

```ruby
MessagesCleanupJob.set(wait_until: Date.tomorrow.noon).perform_later(@contact_message)
```

w ten sposób dopiero jutro usunie się nasza wiadomość.
W realnym świecie te zasady pewnie nie mają sensu, ale to jest tylko przykład.

# konfiguracja

najprostszym gemem będzie zapewne `Delayed::Job` używa on tej samej bazy danych co reszta aplikacji, więc po za nową tabelą nie potrzebujemy za wiele robić

dodajmy więc ten gem do naszego Gemfila

`gem 'delayed_job_active_record'`

i odpalmy `bundle install`

następnie należy odpalić generator `rails generate delayed_job:active_record`

i migracje `rails db:migrate`

w appliacation.rb musimy ustawić by delayed job był odpowiedzialny za naszą kolejkę `config.active_job.queue_adapter = :delayed_job`

na koniec musimy pamiętać by po za `rails server` odpalić jeszcze `rake jobs:work`

jeżeli będziemy chcieli to zakończyć możemy nacisnąć `ctrl+c`

# Heroku

tak jak w innych moich filmach by pokazać jak coś działa na serwerze posłużę się heroku. korzystanie z background jobów na heroku jest bardzo proste. Niestety server i worker nie mogą korzystać z tej samej instancji. Na szczęście oba mogą korzystać z darmowej. Problem zaczyna się gdy przechodzimy na płatne, bo heroku wymusza by oba miały tą samą konfiguracje, ale można je osobno skalować.

Potrzebujemy utworzyć nowy plik `Procfile`

a w nim dodać komende to odpalania servera

`web: bundle exec rails server -p $PORT`

ale też naszego workera

`worker: bundle exec rake jobs:work`

po wrzuceniu zmian na heroku wszystko już będzie działać

# Sidekiq

wartym wspomnienia jest jeszcze gem sidekiq który jest chyba najlepszym rozwiązaniem do zarządzania kolejkami. Z racji, że korzysta on z osobnej bazy danych. Redisa. To poświęcę mu osobne nagranie

Na dzisiaj to już wszystko. Dziękuje za oglądanie
Życzę miłego kodowania!

[Kod aplikacji](https://github.com/rubypopolsku/workery)
