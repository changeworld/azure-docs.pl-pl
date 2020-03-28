---
title: 'Samouczek: Udzielanie dostępu do ASP.NET core internetowego interfejsu API z aplikacji jednostronicowej'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak chronić internetowy interfejs API usługi .NET Core za pomocą usługi Active Directory i wywoływać go z jednostronicowej aplikacji Node.js.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f6f9ff7bb0d504ecc163f6ce1f87477b1ea9c2d1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186150"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Samouczek: Udzielanie dostępu do internetowego interfejsu API ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywołać zasób interfejsu API sieci Web ASP.NET usługi Azure Active Directory B2C (Azure AD B2C) z aplikacji jednostronicowej.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki i wymagania wstępne w [samouczku: Włącz uwierzytelnianie w aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C](tutorial-single-page-app.md).
* Visual Studio 2019 lub nowsze lub Kod programu Visual Studio
* .NET Core 2.2 lub nowszy
* Node.js

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku można zdefiniować uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zapisz wartość w obszarze `demo.read` **ZAKRESY** dla zakresu do użycia w późniejszym kroku podczas konfigurowania aplikacji jednostronicowej. Pełna wartość zakresu jest `https://contosob2c.onmicrosoft.com/api/demo.read`podobna do .

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z innej aplikacji, należy udzielić tej aplikacji uprawnień do internetowego interfejsu API.

W samouczku warunek wstępny utworzono aplikację sieci web o nazwie *webapp1*. W tym samouczku skonfigurujesz tę aplikację tak, aby wywoływała internetowy interfejs API utworzony w poprzedniej sekcji *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Aplikacja sieci Web jednostronicowej jest rejestrowana w celu wywołania chronionego interfejsu API sieci Web. Użytkownik uwierzytelnia się za pomocą usługi Azure AD B2C do korzystania z aplikacji jednostronicowej. Aplikacja jednostronicowa uzyskuje udzielić autoryzacji z usługi Azure AD B2C, aby uzyskać dostęp do chronionego interfejsu API sieci web.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz kod internetowego interfejsu API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację sieci web .NET Core pobraną z usługi GitHub.

[Pobierz \*archiwum zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) lub sklonuj przykładowy projekt interfejsu API sieci web z usługi GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik <em>B2C-WebApi/**appsettings.json** </em> w programie Visual Studio lub visual studio code.
1. Zmodyfikuj `AzureAdB2C` blok, aby odzwierciedlić nazwę dzierżawy, identyfikator aplikacji interfejsu API sieci web, nazwę zasad rejestracji/logowania oraz zakresy zdefiniowane wcześniej. Blok powinien wyglądać podobnie do następującego `Tenant` `ClientId` przykładu (z odpowiednimi i wartościami):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Włączanie mechanizmu CORS

Aby zezwolić aplikacji jednostronicowej na wywoływanie ASP.NET core internetowego interfejsu API, należy włączyć [cors](https://docs.microsoft.com/aspnet/core/security/cors) w interfejsie API sieci web.

1. W pliku *Startup.cs* dodaj mechanizm CORS do metody `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Również w `ConfigureServices()` ramach metody, ustawić `jwtOptions.Authority` wartość do następującego identyfikatora URI wystawcy tokenu.

    Zamień `<your-tenant-name>` na nazwę dzierżawy B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. W `Configure()` metodzie należy skonfigurować cors.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Tylko program Visual Studio) W obszarze **Właściwości** w Eksploratorze rozwiązań otwórz plik *launchSettings.json,* a następnie znajdź `iisExpress` blok.
1. (Tylko program Visual Studio) Zaktualizuj `applicationURL` wartość o numer portu określony podczas rejestrowania aplikacji *webapi1* we wcześniejszym kroku. Przykład:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa (SPA) z [poprzedniego samouczka](tutorial-single-page-app.md) z tej serii używa usługi Azure AD B2C do rejestracji i logowania użytkownika i wywołuje ASP.NET core internetowy interfejs API chroniony przez *frabrikamb2c* demo dzierżawy.

W tej sekcji zaktualizuj aplikację jednostronicową, aby wywołać ASP.NET core internetowy interfejs API chroniony *przez* dzierżawę usługi Azure AD B2C i które są uruchamiane na komputerze lokalnym.

Aby zmienić ustawienia w SPA:

1. Otwórz plik *index.html* w projekcie [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] pobranym lub sklonowanym w poprzednim samouczku.
1. Skonfiguruj przykład z identyfikatorem URI dla zakresu *demo.read* utworzonego wcześniej i adresu URL internetowego interfejsu API.
    1. W `appConfig` definicji zastąp `b2cScopes` wartość pełnym identyfikatorem URI dla zakresu (wartość **SCOPE,** którą wcześniej zarejestrowałeś).
    1. Zmień `webApi` wartość na identyfikator URI przekierowania, który został dodany podczas rejestracji aplikacji interfejsu API sieci web we wcześniejszym kroku.

    Definicja `appConfig` powinna wyglądać podobnie do następującego bloku kodu (z nazwą dzierżawy w `<your-tenant-name>`miejscu):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Uruchamianie interfejsu API SPA i sieci Web

Na koniec można uruchomić zarówno ASP.NET Core interfejsu API sieci web i node.js aplikacji jednostronicowej na komputerze lokalnym. Następnie zaloguj się do aplikacji jednostronicowej i naciśnij przycisk, aby zainicjować żądanie do chronionego interfejsu API.

Mimo że obie aplikacje są uruchamiane lokalnie w tym samouczku, używają usługi Azure AD B2C do bezpiecznego rejestrowania/logowania i udzielania dostępu do chronionego interfejsu API sieci web.

### <a name="run-the-aspnet-core-web-api"></a>Uruchamianie interfejsu API sieci web ASP.NET Core

W programie Visual Studio naciśnij **klawisz F5,** aby utworzyć i debugować rozwiązanie *B2C-WebAPI.sln.* Po uruchomieniu projektu w domyślnej przeglądarce jest wyświetlana strona internetowa z ogłoszeniem, że internetowy interfejs API jest dostępny dla żądań.

Jeśli wolisz używać `dotnet` interfejsu wiersza polecenia zamiast programu Visual Studio:

1. Otwórz okno konsoli i zmień katalog zawierający plik * \*csproj.* Przykład:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Skompiluj i uruchom `dotnet run`internetowy interfejs API, wykonując program .

    Gdy interfejs API jest uruchomiony, powinien zostać wyświetlony dane wyjściowe podobne do `NETSDK1059` następujących (w samouczku można bezpiecznie zignorować wszelkie ostrzeżenia):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Uruchamianie aplikacji jednostronicowej

1. Otwórz okno konsoli i zmień katalog zawierający przykład Node.js. Przykład:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Uruchom następujące polecenia:

    ```console
    npm install && npm update
    node server.js
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym jest hostowana aplikacja.

    ```console
    Listening on port 6420...
    ```

1. Przejdź `http://localhost:6420` do przeglądarki, aby wyświetlić aplikację.
1. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [poprzednim samouczku](tutorial-single-page-app.md). Po pomyślnym zalogowaniu `User 'Your Username' logged-in` powinien zostać wyświetlony komunikat.
1. Wybierz przycisk **Wywołaj interfejs API sieci Web.** Spa uzyskuje udzielić autoryzacji z usługi Azure AD B2C, a następnie uzyskuje dostęp do chronionego interfejsu API sieci web, aby wyświetlić zawartość jego strony indeksu:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

Teraz, gdy widzisz SPA żądania zasobu z chronionego interfejsu API sieci web, uzyskać głębsze zrozumienie, jak te typy aplikacji współdziałają ze sobą i z usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikacji, które mogą być używane w usłudze Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
