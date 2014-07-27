<!--- Copyright (C) 2009-2013 Typesafe Inc. <http://www.typesafe.com> -->
# Dosya Yükleme

## Dosya yüklemeyi form içinde `multipart/form-data` kullanarak yapmak

Web uygulamalarında dosya yüklemek için standart yol, standart form verisi ile dosya eklerini karıştırmaya izin veren özel `multipart/form-data` şifrelenmiş form kullanmaktır. Not: form için HTTP yöntemi POST olmalıdır (GET değil).

HTML form yazarak başlayın:

```
@form(action = routes.Application.upload, 'enctype -> "multipart/form-data") {

    <input type="file" name="picture">

    <p>
        <input type="submit">
    </p>

}
```
`upload` aksiyonunu tanımlayalım:

```
public static Result upload() {
  MultipartFormData body = request().body().asMultipartFormData();
  FilePart picture = body.getFile("picture");
  if (picture != null) {
    String fileName = picture.getFilename();
    String contentType = picture.getContentType();
    File file = picture.getFile();
    return ok("File uploaded");
  } else {
    flash("error", "Missing file");
    return redirect(routes.Application.index());
  }
}
```

## Doğrudan dosya yükleme

Dosyaları sunucuya göndermenin diğer yolu, formdan Ajax kullanarak dosyaları asenkron yüklemektir. Bu durumda, istek gövdesi `multipart/form-data` olarak şifrelenmeyecektir fakat yalın dosya içeriğini içerecektir.

```
public static Result upload() {
  File file = request().body().asRaw().asFile();
  return ok("File uploaded");
}
```

> **Sonraki:** [[SQL veritabanına erişim | JavaDatabase]]
