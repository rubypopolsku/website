Zanim przejdziemy do właściwego kursu. Upewnijmy się czy wszystko działa jak należy. Jeżeli budowaliście już coś z wykorzystaniem Ruby on Rails, prawdopodobnie po prostu możecie ten post pominąć.

# Edytor
Nasz kod musimy edytować w jakimś edytorze. Jeżeli nie macie jeszcze swojego faworyta polecam:

- Visual Studio Code
- Sublime Text
- Ruby Mine
- Vim

Ja osobiście używam tego pierwszego. Ale każdy z nich ma swoje wady i zalety. A wybór zależy od własnych preferencji

# Ruby
Jeżeli nie masz jeszcze zainstalowanego języka ruby zachęcam do zapoznania się z jednym z moich filmów pokazuje proces instalacji na popularnych platformach:
- windows https://www.youtube.com/watch?v=sH8ttwTMqyM
- mac https://www.youtube.com/watch?v=MZKTHOcX6tY
- linux raspian https://www.youtube.com/watch?v=7tK69garuRs

Nie zależnie od platformy z jakiej korzystasz zapraszam na stronę RBENV

# Rails
Korzystając z oficjalnej instrukcji wiemy czego potrzebujemy:
- Ruby (o nim było wcześniej)
- Baza danych (najlepiej od razu instalować PostgreSQL)
- Node.js
- Yarn

Mając już wszystko zainstalowane wystarczy uruchomić komendę
`gem install rails`

# Przykładowa aplikacja
Gdy już macie wszystko zainstalowane nic nie stoi na przeszkodzie by stworzyć przykładową aplikację
`rails new sample-app -d postgresql`

# Uruchamianie lokalnie
Jeżeli udało wam się utworzyć aplikacje bezproblemowo sprawdźmy czy działa lokalnie

```rails server```

gdy nie mamy błędów w przeglądarce możemy odpalić http://localhost:3000 by zobaczyć naszą aplikację

# Pierwszy widok
Nasza aplikacja powinna zawierać jakiekolwiek dane. Możemy posłużyć się jakimiś generatorami railsowymi o których opowiadam w jednym z filmów. Albo utworzyć stronę statyczną. O którym również było nagranie.

# Git
jeżeli jeszcze nie znacie gita to musicie się z nim poznać
https://git-scm.com/book/pl/v2
oczywiście zapoznanie się z tym wszystkim, szczególnie na początku może być przerażające ale co robi `git add` czym jest `commit`, `branch`, `merge` czy `rebase` powinniście wiedzieć.
Gdy tworzyliśmy nową aplikację to automatycznie utworzyliśmy nowe repozytorium gita. Musicie pamiętać tylko by zapisać swoje commity.

`git add -A`

`git commit -m "Wiadomość waszego commita"`

# Uruchamianie w internecie
Skoro tworzymy aplikacje internetowe to chcemy by były one dostępne w internecie. Z pomocą przyjdzie nam serwis heroku. Który w prosty i tani sposób pozwoli nam umieścić naszą aplikację w sieci. Heroku ma świetną dokumentację. Z która krok po kroku możecie zrobić praktycznie wszystko. W moich nagraniach na youtubie również pokazuje jak korzystać z heroku.
Jeżeli wszystkie kroki poszły wam bezproblemowo, jesteście gotowi na następne wyzwania! Zapraszam do fimów na youtube.
