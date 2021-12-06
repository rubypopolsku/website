---
layout: post
title:  "Transakcje w Ruby on Rails"
date:   2021-03-01 19:38:56 +0100
categories: ruby
---
{{< youtube  VkeHuYM6lJ8 >}}

Cześć

w dzisiejszym odcinku chciałbym opowiedzieć wam o transakcjach.  Transakcja nie jest to termin dotyczący ruby on rails bezpośrednio tylko bazy danych. Ale z racji tego, że nasza praca to głównie praca z bazą danych jest to temat bardzo istotny
<!--more-->

Transakcja to zbiór operacji na bazie danych, które stanowią w istocie pewną całość i jako takie powinny być wykonane wszystkie lub żadna z nich. Najczęściej jako przykład pokazywany jest przelew bankowy. To jest świetny przykład. Muszą tu zostać dokonane 2 operacje – zabranie pieniędzy z jednego konta oraz dopisanie ich do drugiego. W przypadku niepowodzenia żadna z tych operacji nie powinna być zatwierdzona, gdyż zajście tylko jednej z nich powodowałoby nieprawidłowości w bazie danych (pojawienie się lub zniknięcie pieniędzy).

W przypadku baz SQL wszystkie popularne bazy(o ile nie wszystkie) wspierają transakcje. W ruby on rails możemy wspomóc się  active recordem do wykonania transakcji.

# Active Record Transactions

Jako przykład użyjemy metody transfer_money

a wewnątrz niej aktualizujemy obiekt john i obiekt ted. John zwiększa stan konta, ted zmniejsza

```ruby
def transfer_money
  ActiveRecord::Base.transaction do
    john.update!(money: john.money + 100)
    ted.update!(money: ted.money - 100)
  end
end
```

jeżeli któraś z operacji się nie powiedzie stan konta w obu przypadkach wróci do poprzedniego stanu. Bardzo ważny jest wykrzyknik w metodzie update. Oznacza on, że w momencie gdy operacja się nie powiedzie railsy zwrócą błąd. Gdyby wykrzyknika nie było to transakcja nie wróciła by do poprzedniego stanu bo nie natrafiła by na błąd.

Inny przykład

```ruby
ActiveRecord::Base.transaction do
  order = order_create!(order_params)
	make_payment!(order, payment_params)
end
```

Wyobraźcie sobie sytuacje, gdy mamy sklep internetowy, albo jakiś magazyn. Składamy zamówienie, tak więc w bazie zapisują się tym informacje, liczba dostępnych produktów maleje, ale płatność nie działa. W takim przypadku stan magazynu musi wrócić do poprzedniego stanu, skoro nie było płatności a produkty wracają do obiegu.

Ale nie powinniśmy też tego nadużywać

jeżeli do poprzedniego przykładu dodali byśmy powiadomienia

np.

```ruby
ActiveRecord::Base.transaction do
  order = order_create!(order_params)
	make_payment!(order, payment_params)
	send_notification(order)
end
```

w przypadku notyfikacji bardzo często korzystamy z zewnętrznych serwisów. Emaile, smsy, whatsapp, albo jeszcze inne kanały komunikacji. Gdyby okazało się, że zewnętrzne API nie działa i zwraca błąd wtedy wszystko musi wrócić do poprzedniego stanu i nasz sklep nie mógł by sprzedawać tylko dlatego, że nie wysyła smsów.

Oczywiście wszystko zależy od modelu biznesowego być może jest to porządane zachowanie. Ale na chwilę obecną umówmy się, że nie.

W takim przypadku ten kod powinien wyglądać tak:

```ruby
ActiveRecord::Base.transaction do
  order = order_create!(order_params)
	make_payment!(order, payment_params)
end

send_notification(order) if order
```

send_notification powinno być po za transakcja. Jeżeli zamówienie się powidło wtedy dopiero wysyłamy powiadomienia, a same powiadomienia nie mają wpływu na naszą transakcje.

Należy pamiętać, że transakcja wykona rollback, czyli wróci do poprzedniego stanu tylko jeżeli metoda zwróci błąd. Jeżeli zwróci `nil` to dla transakcji wszystko jest ok

```ruby
def sample_method(foo)
	return if foo < 1

	do_something
end

def sample_method!(foo)
	raise "error" if foo < 1

	do_something
end
```

w przypadku pierwszej metody jeżeli foo jest mniejsze niż 1 kod się po prostu nie wykona, i dla transakcji taki warunek jest ok. W drugim przypadku jeżeli foo jest mniejsze niż 1 zwróci błąd. W takim przypadku przewie transakcji wróci do poprzedniego stanu.

To tyle w temacie transakcji. Mam nadzieje, że chociaż trochę wam go przybliżyłem.

Dziękuję za oglądanie.

Miłego kodowania.
