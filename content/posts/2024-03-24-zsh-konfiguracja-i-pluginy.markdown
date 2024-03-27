---
layout: post
title:  "ZSH, konfiguracja i pluginy"
date:   2024-03-24 08:31:56 +0100
categories: ruby
---

Zaczynając od podstaw, warto zastanowić się, dlaczego warto zainteresować się ZSH i oh-my-zsh oraz jakie korzyści to niesie. ZSH, czyli Z Shell, to zaawansowany interpreter poleceń dla systemów Unix i Unixopodobnych, który oferuje szereg zaawansowanych funkcji, w tym autouzupełnianie poleceń, zaawansowane zarządzanie plikami i katalogami, oraz konfigurowalne skróty klawiszowe. Wraz z pojawieniem się macOS Catalina, Apple zdecydowało się na wprowadzenie ZSH jako domyślnego shell'a, zastępując dotychczasowego basha. Jednak wybór ZSH to nie tylko decyzja podyktowana zmianami systemowymi, lecz również świadoma decyzja korzystająca z potencjału tej rozbudowanej powłoki systemowej.

Jednym z głównych powodów, dla których warto zainteresować się ZSH, jest jego elastyczność i możliwość konfiguracji. Dzięki plikowi .zshrc użytkownicy mogą dostosować swoje środowisko pracy w terminalu, pisząc proste skrypty, które kontrolują różne aspekty zachowania powłoki. Jednakże, dla tych, którzy poszukują szybkiego startu i szerokiej gamy gotowych rozwiązań, oh-my-zsh staje się kluczowym narzędziem.

Oh-My-Zsh to framework do zarządzania konfiguracją ZSH, który oferuje zbiór skryptów, motywów, pluginów i ustawień konfiguracyjnych. Dzięki niemu korzystanie z ZSH staje się jeszcze wygodniejsze i efektywniejsze. Umożliwia łatwą instalację dodatkowych funkcji, takich jak autouzupełnianie poleceń, skróty klawiszowe, kolorowanie składni czy tematy wyglądu terminala. Ponadto, oh-my-zsh zapewnia prosty sposób zarządzania konfiguracją, co pozwala użytkownikom szybko dostosować swoje środowisko pracy do własnych potrzeb i preferencji.

Korzystając z ZSH i oh-my-zsh, użytkownicy mogą tworzyć bardzo personalizowane środowisko pracy w terminalu, które nie tylko zwiększa wydajność, ale także sprawia przyjemność z użytkowania. Dodatkowo, istnieje szeroki wybór pluginów, takich jak zsh-syntax-highlighting, które dodają dodatkowe funkcje i ulepszenia, jeszcze bardziej zwiększając potencjał ZSH. Dlatego warto zainteresować się tymi narzędziami, aby w pełni wykorzystać możliwości, jakie oferuje praca w terminalu.

Przyznam się, że tak jak raz kiedyś to zainstalowałem, to przenosiłem swoją konfiguracje z komputera na komputer, nie wiele sprawdzając co się zmieniło. Dlatego postanowiłem od nowa zainteresować się swoją konfiguracją.

## Instalacja

Sama instalacja jest bardzo prosta, potrzebujemy pobrać i uruchomić skrypt instalacyjny

```ruby
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

```

no i właściwie to tyle

domyślny plik .zshrc wygląda mniej więcej tak

```ruby
export ZSH=$HOME/.oh-my-zsh

ZSH_THEME="robbyrussell"

plugins=(git)

source $ZSH/oh-my-zsh.sh

```

pierwsza linia to ścieżka gdzie zainstalowane jest oh-my-zsh. Ostatnia zaś ładuje wszystkie skrypty z nim związane. Druga linia (`ZSH_THEME`) to motyw. `plugins` to lista pluginów które mamy dodane. Opisze je później.

jeżeli coś w nim zmienimy i chcemy zobaczyć nasze zmiany należy wykonać polecenie . ~/.zshrc

ja dodałem sobie na to alias `reload!`

```ruby
export ZSH=$HOME/.oh-my-zsh

ZSH_THEME="robbyrussell"

plugins=(git)

source $ZSH/oh-my-zsh.sh

#reload zsh
alias reload!='. ~/.zshrc'
```

łatwiejsze do zapamiętania

jeżeli chodzi o wybór motywu to większość dostępnych możecie zobaczyć tutaj https://github.com/ohmyzsh/ohmyzsh/wiki/Themes - jest w czym wybierać. Oczywiście nic nie stoi na przeszkodzie by napisać coś własnego.

Nie będę sugerować motywu, ale z przydatnych rzeczy które warto mieć to wersja rubiego, branch na jakim się znajdujemy i czy jest coś do zacommitowania na gicie (to chyba podstawa każdego motywu). 

## Wtyczki

Pluginy to największa magia oh-my-zsh jest ich bardzo dużo https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins ale dzięki nim praca w shelu jest o wiele przyjemniejsza.

W domyślnym konfigu jest dodany plugin `git` 

https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git jest to zbiór aliasów by szybciej wykonywać polecenia związane z gitem. Np `gst` zamiast `git status`

W pracy z rubym napewno przyda się `bundler` https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/bundler alias `be` zamiast `bundle exec` naprawdę ułatwia codzienną prace. 

A jak przy rubym jesteśmy to `ruby` też jest przydatne https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/ruby

Warto przejrzeć listę pluginów, każdy znajdzie coś dla siebie. Chociaż nie ukrywam, niektóre to taka sztuka dla sztuki. 

## Dodatkowe paczki

Ja znam i używam jedną - **zsh-syntax-highlighting** podświetla składnie. Bardzo przydatne narzędzie. Najprościej by je opisać, jeżeli coś jest poprawną komendą to zmienia się tego kolor.

Instalacja, zależnie od systemu opisana jest tutaj https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md  na macu to będzie `brew install zsh-syntax-highlighting`  a później `echo "source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc`

## Własne skrypty

nic nie stoi na przeszkodzie by napisać coś własnego. Ja np używam takiej funkcji

```ruby
function ip {
  echo "Airport:  $(ifconfig en0 | grep netmask | awk '{print $2}')"
  echo "External: $(curl --silent checkip.dyndns.org | awk '{print $6}' | cut -f 1 -d "<")"
}
```

zwraca mi ona adresy IP, czasami mi się przydaje i jest zdecydowanie szybciej zrobić to w terminalu niż szukać po ustawieniach systemu.

ZSH jest zaawansowaną powłoką systemową, która oferuje liczne funkcje ułatwiające pracę w terminalu, takie jak zaawansowane zarządzanie plikami, autouzupełnianie poleceń i konfigurowalne skróty klawiszowe. Oh-My-Zsh to framework, który rozszerza możliwości ZSH poprzez dodatkowe skrypty, motywy, pluginy i ustawienia konfiguracyjne, co czyni pracę w terminalu jeszcze bardziej wygodną i efektywną. Dzięki Oh-My-Zsh użytkownicy mogą szybko dostosować swoje środowisko pracy do własnych preferencji. Dodatkowo, istnieje wiele pluginów oraz paczek, takich jak zsh-syntax-highlighting, które dodają dodatkowe funkcje i ulepszenia. Korzystając z tych narzędzi, użytkownicy mogą stworzyć bardzo personalizowane i wydajne środowisko pracy w terminalu.