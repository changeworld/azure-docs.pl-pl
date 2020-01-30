---
title: 'Samouczek: udzielanie dostępu do ASP.NET Core internetowego interfejsu API z aplikacji jednostronicowej'
titleSuffix: Azure AD B2C
description: W tym samouczku dowiesz się, jak używać Active Directory B2C do ochrony interfejsu API sieci Web platformy .NET Core i wywoływać interfejs API z jednej strony aplikacji node. js.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 80b2165b0ec652358a3eb8ac9d55b64f525e4690
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850020"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Samouczek: udzielanie dostępu do ASP.NET Core internetowego interfejsu API z aplikacji jednostronicowej przy użyciu Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API ASP.NET Core chronionych Azure Active Directory B2C (Azure AD B2C) z aplikacji jednostronicowej.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki i wymagania wstępne w [samouczku: Włączanie uwierzytelniania w aplikacji jednostronicowej przy użyciu Azure Active Directory B2C](tutorial-single-page-app.md).
* Program Visual Studio 2019 lub nowszy lub Visual Studio Code
* .NET Core 2,2 lub nowszy
* Node.js

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zapisz wartość w obszarze **zakresy** dla zakresu `demo.read`, który ma być używany w późniejszym kroku podczas konfigurowania aplikacji jednostronicowej. Pełna wartość zakresu jest podobna do `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z innej aplikacji, należy przyznać tej aplikacji uprawnienia do internetowego interfejsu API.

W samouczku wymagań wstępnych została utworzona aplikacja sieci Web o nazwie *webapp1*. W tym samouczku skonfigurujesz aplikację do wywoływania internetowego interfejsu API utworzonego w poprzedniej sekcji *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Jednostronicowa aplikacja sieci Web jest zarejestrowana w celu wywoływania chronionego internetowego interfejsu API. Użytkownik jest uwierzytelniany przy użyciu Azure AD B2C do korzystania z aplikacji jednostronicowej. Aplikacja jednostronicowa uzyskuje autoryzację od Azure AD B2C, aby uzyskać dostęp do chronionego internetowego interfejsu API.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz kod internetowego interfejsu API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową platformy .NET Core pobieraną z usługi GitHub.

[Pobierz archiwum \*. zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) lub Sklonuj przykładowy projekt internetowego interfejsu API z usługi GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik <em>B2C-WebAPI/**appSettings. JSON**</em>  w programie Visual Studio lub Visual Studio Code.
1. Zmodyfikuj blok `AzureAdB2C` w taki sposób, aby odzwierciedlał nazwę dzierżawy, identyfikator aplikacji internetowego interfejsu API, nazwę zasad rejestracji/logowania oraz zdefiniowane wcześniej zakresy. Blok powinien wyglądać podobnie do poniższego przykładu (z odpowiednimi `Tenant` i `ClientId` wartościami):

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

Aby umożliwić aplikacji jednostronicowej wywoływanie ASP.NET Core internetowego interfejsu API, należy włączyć funkcję [CORS](https://docs.microsoft.com/aspnet/core/security/cors) w interfejsie API sieci Web.

1. W pliku *Startup.cs* dodaj mechanizm CORS do metody `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Ponadto w metodzie `ConfigureServices()` ustaw wartość `jwtOptions.Authority` na następujący identyfikator URI wystawcy tokenu.

    Zastąp `<your-tenant-name>` nazwą dzierżawy B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. W metodzie `Configure()` Skonfiguruj mechanizm CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Tylko Visual Studio) W obszarze **Właściwości** w Eksplorator rozwiązań otwórz plik *profilu launchsettings. JSON* , a następnie Znajdź blok `iisExpress`.
1. (Tylko Visual Studio) Zaktualizuj wartość `applicationURL` numerem portu określonym podczas rejestrowania aplikacji *webapi1* we wcześniejszym kroku. Przykład:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa (SPA) z [poprzedniego samouczka](tutorial-single-page-app.md) w serii używa Azure AD B2C do rejestracji i logowania użytkowników, a następnie wywołuje ASP.NET Core internetowy interfejs API chroniony przez dzierżawę demonstracyjną *frabrikamb2c* .

Ta sekcja umożliwia zaktualizowanie aplikacji jednostronicowej w celu wywołania ASP.NET Core internetowego interfejsu API chronionego przez dzierżawę *usługi* Azure AD B2C i uruchamiania na komputerze lokalnym.

Aby zmienić ustawienia w SPA:

1. Otwórz plik *index. html* w projekcie [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] , który został pobrany lub sklonowany w poprzednim samouczku.
1. Skonfiguruj przykład za pomocą identyfikatora URI dla *demonstracji.* wcześniej utworzony zakres odczytu i adres URL internetowego interfejsu API.
    1. W definicji `appConfig` Zastąp wartość `b2cScopes` wartością pełny identyfikator URI zakresu (wartość **zakresu** zarejestrowana wcześniej).
    1. Zmień wartość `webApi` na identyfikator URI przekierowania, który został dodany podczas rejestrowania aplikacji internetowego interfejsu API we wcześniejszym kroku.

    Definicja `appConfig` powinna wyglądać podobnie do następującego bloku kodu (z nazwą dzierżawy w miejscu `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Uruchamianie SPA i internetowego interfejsu API

Na koniec należy uruchomić zarówno ASP.NET Core Web API, jak i aplikację jednostronicową Node. js na komputerze lokalnym. Następnie zaloguj się do aplikacji jednostronicowej i naciśnij przycisk, aby zainicjować żądanie do chronionego interfejsu API.

Mimo że obie aplikacje działają lokalnie w tym samouczku, używają Azure AD B2C do bezpiecznego rejestrowania/logowania i udzielania dostępu do chronionego internetowego interfejsu API.

### <a name="run-the-aspnet-core-web-api"></a>Uruchamianie interfejsu API sieci Web ASP.NET Core

W programie Visual Studio naciśnij klawisz **F5** , aby skompilować i debugować rozwiązanie *rozwiązanie B2C-WebApi. sln* . Po uruchomieniu projektu Strona sieci Web zostanie wyświetlona w domyślnej przeglądarce informującej, że internetowy interfejs API jest dostępny dla żądań.

Jeśli wolisz używać interfejsu wiersza polecenia `dotnet` zamiast programu Visual Studio:

1. Otwórz okno konsoli i przejdź do katalogu zawierającego plik *\*. csproj* . Przykład:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Kompiluj i uruchamiaj internetowy interfejs API, wykonując `dotnet run`.

    Gdy interfejs API jest uruchomiony i działa, powinny zostać wyświetlone dane wyjściowe podobne do następujących (samouczek umożliwia bezpieczne ignorowanie ostrzeżeń `NETSDK1059`):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Uruchamianie aplikacji jednostronicowej

1. Otwórz okno konsoli i przejdź do katalogu zawierającego przykład środowiska Node. js. Przykład:

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

1. Przejdź do `http://localhost:6420` w przeglądarce, aby wyświetlić aplikację.
1. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [poprzednim samouczku](tutorial-single-page-app.md). Po pomyślnym zalogowaniu powinna zostać wyświetlona wiadomość `User 'Your Username' logged-in`.
1. Wybierz przycisk **Wywołaj internetowy interfejs API** . SPA uzyskuje autoryzację od Azure AD B2C, następnie uzyskuje dostęp do chronionego internetowego interfejsu API w celu wyświetlenia zawartości strony indeksu:

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

Teraz, gdy zostało wystawione żądanie SPA zasobu z chronionego internetowego interfejsu API, uzyskaj dokładniejsze informacje o tym, jak te typy aplikacji współdziałają ze sobą i z Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikacji, które mogą być używane w Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
