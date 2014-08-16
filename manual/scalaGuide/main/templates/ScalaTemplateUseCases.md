<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Scala şablonları yaygın kullanım senaryoları

Şablonlar, aslında basit birer fonksiyon olduklarından, istediğiniz şekilde birleştirilebilirler. Aşağıda bazı yaygın kullanım senaryosu örnekleri bulunuyor.

## Yerleşim

Ana yerleşim şablonu olarak kullanacağımız bir `views/main.scala.html` şablonu tanımlayalım:

```html
@(title: String)(content: Html)
<!DOCTYPE html>
<html>
  <head>
    <title>@title</title>
  </head>
  <body>
    <section class="content">@content</section>
  </body>
</html>

```

Gördüğünüz gibi bu şablon iki parametre alıyor: bir başlık ve HTML içerik bloğu. Artık bu şablonu başka bir `views/Application/index.scala.html` şablonundan kullanabiliriz:

```html
@main(title = "Home") {

  <h1>Home page</h1>

}
```

> **Not:** Bazen `@main(title = "Home")` gibi isimlendirilmiş parametreler kullandığımız halde bazen `@main("Home")` şeklinde kullanıyoruz. Sizin durumunuza en uygun olanı seçmekte özgürsünüz.

Bazı durumlarda sidebar ya da breadcrumb için diğer bir sayfaya-özel içerik bloğuna ihtiyacınız olabilir. Bunu fazladan bir parametre ile sağlayabilirsiniz:

```html
@(title: String)(sidebar: Html)(content: Html)
<!DOCTYPE html>
<html>
  <head>
    <title>@title</title>
  </head>
  <body>
    <section class="sidebar">@sidebar</section>
    <section class="content">@content</section>
  </body>
</html>
```

Bunu daha önceden yazdığımız ‘index’ şablonundan kullanmak için:

```html
@main("Home") {
  <h1>Sidebar</h1>

} {
  <h1>Home page</h1>

}
```

Ya da sidebar bloğunu ayrıca tanımlayabiliriz:

```html
@sidebar = {
  <h1>Sidebar</h1>
}

@main("Home")(sidebar) {
  <h1>Home page</h1>

}
```


## Tag'ler (yalnızca birer fonksiyonlar, değil mi?)

Bir HTML uyarısı gösteren basit bir `views/tags/notice.scala.html` tag yazalım:

```html
@(level: String = "error")(body: (String) => Html)

@level match {

  case "success" => {
    <p class="success">
      @body("green")
    </p>
  }

  case "warning" => {
    <p class="warning">
      @body("orange")
    </p>
  }

  case "error" => {
    <p class="error">
      @body("red")
    </p>
  }

}
```

Şimdi de bunu başka bir şablondan kullanalım:

```html
@import tags._

@notice("error") { color =>
  Oops, something is <span style="color:@color">wrong</span>
}
```

## Şablon dahil etme

Yine çok özel bir durum yok. Herhangi bir şablonu istediğiniz gibi çağırabilirsiniz (aslında herhangi bir yerden gelen herhangi bir fonksiyonu):

```html
<h1>Home</h1>

<div id="side">
  @common.sideBar()
</div>
```
## moreScripts ve moreStyles eşlenikleri

Eski moreScripts ve moreStyles eşleniklerini (Play! 1.x'te olduğu gibi) tanımlamak için ana şablonda aşağıdaki gibi bir değişken tanımlayabilirsiniz:

```html
@(title: String, scripts: Html = Html(""))(content: Html)

<!DOCTYPE html>

<html>
    <head>
        <title>@title</title>
        <link rel="stylesheet" media="screen" href="@routes.Assets.at("stylesheets/main.css")">
        <link rel="shortcut icon" type="image/png" href="@routes.Assets.at("images/favicon.png")">
        <script src="@routes.Assets.at("javascripts/jquery-1.7.1.min.js")" type="text/javascript"></script>
        @scripts
    </head>
    <body>
        <div class="navbar navbar-fixed-top">
            <div class="navbar-inner">
                <div class="container">
                    <a class="brand" href="#">Movies</a>
                </div>
            </div>
        </div>
        <div class="container">
            @content
        </div>
    </body>
</html>
```

Ve fazladan script isteyen bir şablonda aşağıdaki gibi kullanabilirsiniz:

```html
@scripts = {
    <script type="text/javascript">alert("hello !");</script>
}

@main("Title",scripts){

   Burada HTML içerik yer alıyor ...

}

```

Fazladan script ihtiyacı olmayan bir şablonda ise kullanım aşağıdaki gibidir:

```html
@main("Title"){

   Burada HTML içerik yer alıyor ...

}
```
> **Sonraki:** [[Özel format ekleme | ScalaCustomTemplateFormat]]
