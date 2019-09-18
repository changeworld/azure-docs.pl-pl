---
title: Samouczek — udzielanie dostępu do ASP.NET Core internetowego interfejsu API z poziomu aplikacji jednostronicowej — Azure Active Directory B2C
description: Dowiedz się, jak za pomocą programu Active Directory B2C chronić interfejs API sieci Web platformy .NET Core i wywoływać interfejs API z jednej strony aplikacji node. js.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6d354ab25125b0df90ac3d6852d7eafe5d5aba46
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064688"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API ASP.NET Core chronionych Azure Active Directory B2C (Azure AD B2C) z aplikacji jednostronicowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki i spełnij wymagania wstępne w [Samouczku: Włącz uwierzytelnianie w aplikacji jednostronicowej przy użyciu Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Program Visual Studio 2019 lub nowszy lub Visual Studio Code
* .NET Core 2,2 lub nowszy
* Node.js

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
1. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
1. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:5000`.
1. W polu **Identyfikator URI aplikacji**wprowadź identyfikator punktu końcowego interfejsu API dla podanego identyfikatora URI. Dla samouczka wprowadź `api`, aby pełny identyfikator URI był podobny do `https://contosotenant.onmicrosoft.com/api`.
1. Kliknij przycisk **Utwórz**.
1. Wybierz aplikację *webapi1* , aby otworzyć jej stronę właściwości.
1. Zapisz **Identyfikator aplikacji** widoczny na stronie właściwości. Ten identyfikator jest potrzebny w późniejszym kroku podczas konfigurowania aplikacji sieci Web.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu i zapisu dla internetowego interfejsu API.

1. Wybierz pozycję **aplikacje**, a następnie wybierz pozycję *webapi1* , aby otworzyć jej stronę właściwości, jeśli nie została jeszcze otwarta.
1. Wybierz pozycję **Opublikowane zakresy**.
1. W obszarze **zakres**wprowadź `Hello.Read`tekst i wpisz `Read access to hello`polecenie.
1. W obszarze **zakres**wprowadź `Hello.Write`tekst i wpisz `Write access to hello`polecenie.
1. Wybierz pozycję **Zapisz**.
1. Zapisz **pełną wartość** zakresu dla zakresu, `Hello.Read` który ma być używany w późniejszym kroku podczas konfigurowania aplikacji jednostronicowej. Pełny zakres wartości jest podobny do `https://yourtenant.onmicrosoft.com/api/Hello.Read`.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywołać chroniony internetowy interfejs API z innej aplikacji, należy przyznać tej aplikacji uprawnienia do internetowego interfejsu API.

W samouczku wymagań wstępnych została utworzona aplikacja sieci Web o nazwie *webapp1*. W tym samouczku skonfigurujesz aplikację do wywoływania internetowego interfejsu API utworzonego w poprzedniej sekcji *webapi1*.

1. Przejdź do swojej dzierżawy B2C w Azure Portal
1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
1. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy **Hello.Read** i **Hello.Write**, które zostały wcześniej zdefiniowane.
1. Kliknij przycisk **OK**.

Jednostronicowa aplikacja sieci Web jest zarejestrowana w celu wywoływania chronionego internetowego interfejsu API. Użytkownik jest uwierzytelniany przy użyciu Azure AD B2C do korzystania z aplikacji jednostronicowej. Aplikacja jednostronicowa uzyskuje autoryzację od Azure AD B2C, aby uzyskać dostęp do chronionego internetowego interfejsu API.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz kod internetowego interfejsu API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową platformy .NET Core pobieraną z usługi GitHub.

Pobierz archiwum zip lub Sklonuj przykładowy projekt internetowego interfejsu API z usługi GitHub. [ \*](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz plik *B2C-WebAPI/* * appSettings. JSON** * w programie Visual Studio lub Visual Studio Code.
1. `AzureAdB2C` Zmodyfikuj blok, aby odzwierciedlał nazwę dzierżawy, identyfikator aplikacji internetowego interfejsu API, nazwę zasad rejestracji/logowania oraz zdefiniowane wcześniej zakresy. Blok powinien wyglądać podobnie do poniższego przykładu (z odpowiednimi `Tenant` wartościami `ClientId` i):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "Hello.Read",
      "ScopeWrite": "Hello.Write"
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

1. Ponadto w ramach `ConfigureServices()` metody `jwtOptions.Authority` ustaw wartość na następujący identyfikator URI wystawcy tokenu.

    Zamień `<your-tenant-name>` na nazwę dzierżawy B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. `Configure()` W metodzie Skonfiguruj mechanizm CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Tylko Visual Studio) W obszarze **Właściwości** w Eksplorator rozwiązań otwórz plik *profilu launchsettings. JSON* , `iisExpress` a następnie Znajdź blok.
1. (Tylko Visual Studio) Zaktualizuj wartość przy użyciu numeru portu określonego podczas rejestrowania aplikacji webapi1 we wcześniejszym kroku. `applicationURL` Przykład:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa (SPA) z [poprzedniego samouczka](active-directory-b2c-tutorials-spa.md) w serii używa Azure AD B2C do rejestracji i logowania użytkowników, a następnie wywołuje ASP.NET Core internetowy interfejs API chroniony przez dzierżawę demonstracyjną *frabrikamb2c* .

Ta sekcja umożliwia zaktualizowanie aplikacji jednostronicowej w celu wywołania ASP.NET Core internetowego interfejsu API chronionego przez dzierżawę *usługi* Azure AD B2C i uruchamiania na komputerze lokalnym.

Aby zmienić ustawienia w SPA:

1. Otwórz plik *index. html* w projekcie [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] , który został pobrany lub sklonowany w poprzednim samouczku.
1. Skonfiguruj przykład za pomocą identyfikatora URI dla utworzonego wcześniej zakresu *Hello. Read* i adresu URL internetowego interfejsu API.
    1. W definicji Zastąp wartość pełnym identyfikatorem URI dla zakresu ( **pełna wartość zakresu** została zapisana wcześniej). `b2cScopes` `appConfig`
    1. `webApi` Zmień wartość`applicationURL` na wartość określoną w poprzedniej sekcji.

    Definicja powinna wyglądać podobnie do następującego bloku kodu (z nazwą dzierżawy w `<your-tenant-name>`miejscu): `appConfig`

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/Hello.Read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Uruchamianie SPA i internetowego interfejsu API

Na koniec należy uruchomić zarówno ASP.NET Core Web API, jak i aplikację jednostronicową Node. js na komputerze lokalnym. Następnie zaloguj się do aplikacji jednostronicowej i naciśnij przycisk, aby zainicjować żądanie do chronionego interfejsu API.

Mimo że obie aplikacje działają lokalnie w tym samouczku, używają Azure AD B2C do bezpiecznego rejestrowania/logowania i udzielania dostępu do chronionego internetowego interfejsu API.

### <a name="run-the-aspnet-core-web-api"></a>Uruchamianie interfejsu API sieci Web ASP.NET Core

W programie Visual Studio naciśnij klawisz **F5** , aby skompilować i debugować rozwiązanie *rozwiązanie B2C-WebApi. sln* . Po uruchomieniu projektu Strona sieci Web zostanie wyświetlona w domyślnej przeglądarce informującej, że internetowy interfejs API jest dostępny dla żądań.

Jeśli wolisz używać `dotnet` interfejsu wiersza polecenia zamiast programu Visual Studio:

1. Otwórz okno konsoli i przejdź do katalogu zawierającego  *\*plik. csproj* . Na przykład:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Kompiluj i uruchamiaj internetowy interfejs API, `dotnet run`wykonując.

    Gdy interfejs API jest uruchomiony i działa, powinny zostać wyświetlone dane wyjściowe podobne do następujących (samouczek umożliwia bezpieczne ignorowanie `NETSDK1059` ostrzeżeń):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Uruchamianie aplikacji jednostronicowej

1. Otwórz okno konsoli i przejdź do katalogu zawierającego przykład środowiska Node. js. Na przykład:

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

1. Przejdź do `http://localhost:6420` przeglądarki, aby wyświetlić aplikację.
1. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [poprzednim samouczku](active-directory-b2c-tutorials-spa.md). Po pomyślnym zalogowaniu powinien zostać `User 'Your Username' logged-in` wyświetlony komunikat.
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
> [Typy aplikacji, które mogą być używane w Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
