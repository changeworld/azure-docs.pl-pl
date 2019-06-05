---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej — Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy .NET Core i wywoływania go z aplikacji jednostronicowej.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 609e7bc4104a445c5d77109c9470fb487b2f1336
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507743"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API platformy ASP.NET Core chroniony przez usługę Azure Active Directory (Azure AD) B2C z aplikacji jednostronicowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki i spełnij wymagania wstępne w [Samouczku: włączanie uwierzytelniania aplikacji jednostronicowej przy użyciu kont w usłudze Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Dodawanie aplikacji internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
6. Dla pozycji **Uwzględnij aplikację internetową/internetowy interfejs API** i **Zezwalaj na niejawny przepływ** wybierz wartość **Tak**.
7. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:5000`.
8. Dla pozycji **Identyfikator URI identyfikatora aplikacji** wprowadź identyfikator używany na potrzeby internetowego interfejsu API. Zostanie wygenerowany pełny identyfikator URI łącznie z domeną. Na przykład `https://contosotenant.onmicrosoft.com/api`.
9. Kliknij pozycję **Utwórz**.
10. Na stronie właściwości zapisz identyfikator aplikacji, który będzie używany podczas konfigurowania aplikacji internetowej.

## <a name="configure-scopes"></a>Konfigurowanie zakresów

Zakresy umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu dla internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapi1*.
2. Wybierz pozycję **Opublikowane zakresy**.
3. Dla pozycji **zakres** wprowadź `Hello.Read`, a dla opisu wprowadź `Read access to hello`.
4. Dla pozycji **zakres** wprowadź `Hello.Write`, a dla opisu wprowadź `Write access to hello`.
5. Kliknij pozycję **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

## <a name="grant-permissions"></a>Udzielenie uprawnień

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W ramach samouczka wymagań wstępnych w usłudze Azure AD B2C została utworzona aplikacja internetowa o nazwie *webapp1*. Tej aplikacji użyjesz do wywołania internetowego interfejsu API.

1. Wybierz pozycję **Aplikacje**, a następnie wybierz pozycję *webapp1*.
2. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
3. Na liście rozwijanej **Wybierz interfejs API** wybierz pozycję *webapi1*.
4. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy **Hello.Read** i **Hello.Write**, które zostały wcześniej zdefiniowane.
5. Kliknij przycisk **OK**.

Twoja aplikacja o nazwie **Moja przykładowa aplikacja jednostronicowa** została zarejestrowana do wywołania chronionego interfejsu **Hello Core API**. Użytkownik uwierzytelnia się w usłudze Azure AD B2C, aby korzystać z aplikacji jednostronicowej. Aplikacja jednostronicowa uzyskuje autoryzację z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="configure-the-sample"></a>Konfigurowanie przykładu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów konfigurujesz kod internetowego interfejsu API w celu skorzystania ze swojej dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową platformy .NET Core, którą możesz pobrać z witryny GitHub. [Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurowanie internetowego interfejsu API

1. Otwórz rozwiązanie **B2C-WebAPI.sln** w programie Visual Studio.
2. Otwórz plik **appsettings.json**. Zaktualizuj następujące wartości w celu skonfigurowania internetowego interfejsu API do używania dzierżawy:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Włączanie mechanizmu CORS

Aby umożliwić aplikacji jednostronicowej wywoływanie internetowego interfejsu API platformy ASP.NET Core, musisz włączyć mechanizm [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. W pliku **Startup.cs** dodaj mechanizm CORS do metody `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. W pliku **Startup.cs** skonfiguruj mechanizm CORS w metodzie `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Otwórz plik **launchSettings.json** w obszarze **Właściwości**, zlokalizuj ustawienie **iisSettings** *applicationURL* i ustaw numer portu na jeden z numerów zarejestrowanych dla adresu URL odpowiedzi interfejsu API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa używa usługi Azure AD B2C do tworzenia nowych kont i logowania użytkowników oraz wywołuje chroniony internetowy interfejs API platformy ASP.NET Core. Zaktualizuj aplikację jednostronicową tak, aby wywoływała internetowy interfejs API platformy .NET Core.

Aby zmienić ustawienia aplikacji:

1. Otwórz plik `index.html`.
2. Skonfiguruj przykład obejmujący informacje na temat rejestracji dzierżawy usługi Azure AD B2C. W poniższym kodzie dodaj nazwę swojej dzierżawy do pozycji **b2cScopes** i zmień wartość **webApi** na zapisaną wcześniej wartość ustawienia *applicationURL*:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Uruchamianie aplikacji SPA i internetowego interfejsu API

Musisz uruchomić zarówno aplikację jednostronicową platformy Node.js, jak i internetowy interfejs API platformy .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Uruchamianie internetowego interfejsu API platformy ASP.NET Core 

Naciśnij klawisz **F5**, aby debugować rozwiązanie **B2C-WebAPI.sln** w programie Visual Studio.

Po uruchomieniu projektu strona internetowa jest wyświetlana w przeglądarce domyślnej, co oznacza, że interfejs API jest dostępny i może obsługiwać żądania.

### <a name="run-the-single-page-app"></a>Uruchamianie aplikacji jednostronicowej

1. Uruchom wiersz polecenia platformy Node.js.
2. Przejdź do katalogu zawierającego przykład platformy Node.js. Na przykład `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Uruchom następujące polecenia:
    ```
    npm install && npm update
    node server.js
    ```

    W oknie konsoli jest wyświetlany numer portu, w którym jest hostowana aplikacja.
    
    ```
    Listening on port 6420...
    ```

4. W przeglądarce przejdź do adresu `http://localhost:6420`, aby wyświetlić aplikację.
5. Zaloguj się przy użyciu adresu e-mail i hasła użytego w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji jednostronicowej (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Kliknij pozycję **Wywołaj interfejs API**.

Po utworzeniu nowego konta lub zalogowaniu się przy użyciu konta użytkownika przykład wywołuje chronionej internetowy interfejs API i zwraca wynik.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie aplikacji internetowego interfejsu API
> * Konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień do internetowego interfejsu API
> * Konfigurowanie przykładu korzystania z aplikacji

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie dostawcy tożsamości do aplikacji w usłudze Azure Active Directory B2C](tutorial-add-identity-providers.md)
