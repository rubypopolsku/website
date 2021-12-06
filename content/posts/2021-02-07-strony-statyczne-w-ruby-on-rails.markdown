---
layout: post
title:  "Strony statyczne w Ruby on Rails"
date:   2021-02-07 19:38:56 +0100
categories: ruby
---
{{< youtube WgyvlLxC9dU >}}

W dzisiejszym odcinku chciałbym pokazać wam jak budować statyczne strony w ruby on rails. Strona statyczna to taka, która zawiera w sobie sam html ewentualnie Javascript. Nie wykorzystuje w ogóle języka ruby.
<!--more-->

na potrzeby tego filmu zbuduje sobie nowy projekt w railsach

`rails new strony_statyczne`

Teraz otworzę router

```ruby
get  "/about_me", to: "static_sites#about_me"
```

w ten sposób router będzie prowadził z adresu [http://localhost:3000/about_me](http://localhost:3000/about_me) do odpowiedniego kontrolera, a ten będzie wyświetlał odpowiednia stronę

Na chwile obecną nie mamy kontrolera więc musimy go utworzyć

`/app/controllers/static_sites_controller.rb`

```ruby
class StaticSitesController < ApplicationController
  def about_me
	end
end
```

jak widzicie po za zdefiniowaniem metody `about_me` nie musimy nic więcej dodawać w kontrolerze.

Musimy natomiast dodać widok

`/app/views/static_sites/about_me.html.erb`

i w tym pliku możemy zaprojektować naszą stronę statyczną. A właściwie jej fragment. Ponieważ musicie pamiętać, że strona ta wyświetli się wewnątrz layoutu z `/app/views/layouts/application.html.erb`

```ruby
<h1>O mnie</h1>
<p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean vulputate imperdiet neque, ac porttitor quam scelerisque nec. Aliquam nec odio urna. Nam justo tellus, tempor sit amet luctus pellentesque, rutrum et mi. Aenean fringilla cursus arcu, sit amet pulvinar nunc blandit eu. Fusce et lacinia erat. Aenean nec interdum felis. Etiam viverra massa eget nunc accumsan, sit amet consequat leo finibus. Integer facilisis mauris eget accumsan aliquam. Vestibulum placerat lectus non enim vehicula viverra. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Ut at lectus ut ligula lacinia pellentesque. Quisque a ante eget ligula imperdiet egestas at consequat risus. Integer molestie lectus dolor, eget aliquet enim porttitor imperdiet.

Cras ultricies mauris vulputate lorem volutpat convallis. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia curae; Nullam egestas faucibus commodo. Vestibulum in arcu non nunc efficitur lacinia et sit amet neque. Vivamus consequat ante urna, ut semper quam sollicitudin at. Proin non est iaculis, tincidunt odio sit amet, luctus mi. Cras tincidunt ante quis enim rhoncus, a euismod sapien tincidunt. Vivamus pulvinar rutrum est ac ultrices. Orci varius natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Sed cursus sapien vitae mi elementum, non scelerisque diam laoreet.

Nullam sed nibh pellentesque, mollis massa ac, ornare urna. Phasellus pharetra justo quis elementum interdum. Morbi dictum porttitor ante sit amet viverra. Praesent pharetra libero vitae sapien lobortis semper. Donec eget metus aliquet, commodo augue sit amet, congue magna. Mauris pharetra ligula et tellus euismod condimentum. Etiam nisl velit, tempus non libero consectetur, scelerisque porttitor felis. Maecenas nec feugiat nisi. Phasellus suscipit neque sed fringilla porttitor. In faucibus, nunc id viverra rutrum, quam risus suscipit turpis, id luctus purus erat eget neque. Donec sit amet nunc porttitor, blandit justo vitae, pulvinar metus. Praesent tincidunt elementum nunc sed facilisis.

Aliquam ut rutrum lacus, quis porttitor dolor. Aenean rutrum nulla eu nibh faucibus hendrerit. Nulla vitae sem enim. Sed rutrum vel ante in luctus. Donec pretium mauris quis erat placerat dignissim. Nam congue viverra lobortis. Integer rhoncus at mauris vel finibus.

Morbi faucibus blandit nulla quis sagittis. Suspendisse at tincidunt mauris. Quisque eget ornare leo, id bibendum leo. Etiam vel mi pharetra, maximus nulla quis, fermentum quam. Donec consequat nulla sit amet nunc accumsan, id dictum tellus dapibus. Integer tempus mauris ornare mi mollis hendrerit. Donec non leo in tellus sodales sodales. Donec bibendum sit amet ante vel mattis.
</p>
```

włączmy sobie nasz serwer

`rails server`

i wchodząc na [http://localhost:3000/about_me](http://localhost:3000/about_me) dostępna jest nasza strona

Ale to nie jedyna możliwość na stronę statyczną. Jeżeli mamy plik html z gotową stroną i nie chcemy by był umieszczony wewnątrz naszego layoutu. Możemy taki plik umieścić w katalogu `/public`

np `/public/inna_strona`

```html
<!doctype html>
<html lang="en" class="h-100">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="Mark Otto, Jacob Thornton, and Bootstrap contributors">
    <meta name="generator" content="Hugo 0.79.0">
    <title>Sticky Footer Navbar Template · Bootstrap v5.0</title>

    <link rel="canonical" href="https://getbootstrap.com/docs/5.0/examples/sticky-footer-navbar/">

    <!-- Bootstrap core CSS -->
<link href="https://getbootstrap.com/docs/5.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1" crossorigin="anonymous">

    <!-- Favicons -->
<link rel="apple-touch-icon" href="https://getbootstrap.com/docs/5.0/assets/img/favicons/apple-touch-icon.png" sizes="180x180">
<link rel="icon" href="https://getbootstrap.com/docs/5.0/assets/img/favicons/favicon-32x32.png" sizes="32x32" type="image/png">
<link rel="icon" href="https://getbootstrap.com/docs/5.0/assets/img/favicons/favicon-16x16.png" sizes="16x16" type="image/png">
<link rel="manifest" href="https://getbootstrap.com/docs/5.0/assets/img/favicons/manifest.json">
<link rel="mask-icon" href="https://getbootstrap.com/docs/5.0/assets/img/favicons/safari-pinned-tab.svg" color="#7952b3">
<link rel="icon" href="https://getbootstrap.com/docs/5.0/assets/img/favicons/favicon.ico">
<meta name="theme-color" content="#7952b3">

    <style>
      .bd-placeholder-img {
        font-size: 1.125rem;
        text-anchor: middle;
        -webkit-user-select: none;
        -moz-user-select: none;
        user-select: none;
      }

      @media (min-width: 768px) {
        .bd-placeholder-img-lg {
          font-size: 3.5rem;
        }
      }
    </style>

    <!-- Custom styles for this template -->
    <link href="sticky-footer-navbar.css" rel="stylesheet">
  </head>
  <body class="d-flex flex-column h-100">

<header>
  <!-- Fixed navbar -->
  <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
    <div class="container-fluid">
      <a class="navbar-brand" href="#">Fixed navbar</a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarCollapse">
        <ul class="navbar-nav me-auto mb-2 mb-md-0">
          <li class="nav-item active">
            <a class="nav-link" aria-current="page" href="#">Home</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Link</a>
          </li>
          <li class="nav-item">
            <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
          </li>
        </ul>
        <form class="d-flex">
          <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
          <button class="btn btn-outline-success" type="submit">Search</button>
        </form>
      </div>
    </div>
  </nav>
</header>

<!-- Begin page content -->
<main class="flex-shrink-0">
  <div class="container">
    <h1 class="mt-5">Sticky footer with fixed navbar</h1>
    <p class="lead">Pin a footer to the bottom of the viewport in desktop browsers with this custom HTML and CSS. A fixed navbar has been added with <code class="small">padding-top: 60px;</code> on the <code class="small">main &gt; .container</code>.</p>
    <p>Back to <a href="https://getbootstrap.com/docs/5.0/examples/sticky-footer/">the default sticky footer</a> minus the navbar.</p>
  </div>
</main>

<footer class="footer mt-auto py-3 bg-light">
  <div class="container">
    <span class="text-muted">Place sticky footer content here.</span>
  </div>
</footer>

    <script src="/docs/5.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-ygbV9kiqUc6oa4msXn9868pTtWMgiQaeYH7/t7LECLbyPA2x65Kgf80OJFdroafW" crossorigin="anonymous"></script>

  </body>
</html>
```

to jest przykład z bootstrapa

w takim przypadku nie musimy dodawać nic do routera, nie musimy tworzyć kontrolera, wystarczy umieścić sam plik

To by było na tyle

Nowy odcinek już wkrótce.

Miłego kodowania

[Kod aplikacji](https://github.com/rubypopolsku/strony-statyczne-w-ruby-on-rails)
