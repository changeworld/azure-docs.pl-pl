---
title: Samouczek — udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C | Microsoft Docs
description: Samouczek dotyczący używania usługi Active Directory B2C do chronienia internetowego interfejsu API platformy .NET Core i wywoływania go z aplikacji jednostronicowej.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: efe975fa4f89a262faef82df3cc79820d393b60e
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605763"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Samouczek: udzielanie dostępu do internetowego interfejsu API platformy ASP.NET Core z aplikacji jednostronicowej przy użyciu usługi Azure Active Directory B2C

W tym samouczku pokazano, jak wywoływać zasób internetowego interfejsu API platformy ASP.NET Core chroniony przez usługę Azure Active Directory (Azure AD) B2C z aplikacji jednostronicowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie internetowego interfejsu API w dzierżawie usługi Azure AD B2C
> * Definiowanie i konfigurowanie zakresów dla internetowego interfejsu API
> * Udzielanie uprawnień aplikacji do internetowego interfejsu API
> * Aktualizowanie przykładowego kodu w celu chronienia internetowego interfejsu API za pomocą usługi Azure AD B2C

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj czynności opisane w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji jednostronicowej](active-directory-b2c-tutorials-spa.md).
* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/downloads/) z pakietem roboczym **Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych**.
* Zestaw [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) lub nowszy
* Zainstalować środowisko [Node.js](https://nodejs.org/en/download/).

## <a name="register-web-api"></a>Rejestrowanie internetowego interfejsu API

Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na [żądania chronionych zasobów](../active-directory/develop/developer-glossary.md#resource-server) wysyłane przez [aplikacje klienckie](../active-directory/develop/developer-glossary.md#client-application) przedstawiające [token dostępu](../active-directory/develop/developer-glossary.md#access-token) z usługi Azure Active Directory. Rejestracja powoduje ustanowienie [obiektu aplikacji i jednostki usługi](../active-directory/develop/developer-glossary.md#application-object) w dzierżawie. 

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**. Teraz powinna być używana dzierżawa utworzona w poprzednim samouczku.

2. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.

    Aby zarejestrować przykładowy internetowy interfejs API w dzierżawie, użyj następujących ustawień.
    
    ![Dodawanie nowego interfejsu API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nazwa** | Interfejs Hello Core API | Wprowadź wartość **Nazwa** opisującą internetowy interfejs API dla deweloperów. |
    | **Uwzględnij aplikację internetową/internetowy interfejs API** | Yes | Wybierz pozycję **Tak** dla internetowego interfejsu API. |
    | **Zezwalaj na niejawny przepływ** | Yes | Wybierz pozycję **Tak**, ponieważ interfejs API używa [logowania OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adres URL odpowiedzi** | `http://localhost:5000` | Adresy URL odpowiedzi to punkty końcowe, do których usługa Azure AD B2C zwraca wszelkie tokeny żądane przez interfejs API. W tym samouczku przykładowy internetowy interfejs API jest uruchamiany lokalnie (localhost) i nasłuchuje na porcie 5000 (po skonfigurowaniu do nowszej wersji w tym samouczku). |
    | **Identyfikator URI identyfikatora aplikacji** | HelloCoreAPI | Identyfikator URI unikatowo identyfikuje interfejs API w dzierżawie. Dzięki temu można zarejestrować wiele interfejsów API w dzierżawie. [Zakresy](../active-directory/develop/developer-glossary.md#scopes) umożliwiają zarządzanie dostępem do chronionego zasobu interfejsu API i są definiowane dla identyfikatora URI aplikacji. |
    | **Natywny klient** | Nie | Ponieważ jest to internetowy interfejs API, a nie klient natywny, wybierz pozycję Nie. |
    
3. Kliknij pozycję **Utwórz**, aby zarejestrować interfejs API.

Zarejestrowane interfejsy API są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

![Właściwości internetowego interfejsu API](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Zwróć uwagę na wartość **Identyfikator klienta aplikacji**. Ten identyfikator unikatowo identyfikuje interfejs API i jest potrzebny podczas późniejszego konfigurowania interfejsu API w samouczku.

Zarejestrowanie internetowego interfejsu API w usłudze Azure AD B2C definiuje relację zaufania. Interfejs API zarejestrowany w usłudze B2C może ufać tokenom dostępu B2C otrzymywanym z innych aplikacji.

## <a name="define-and-configure-scopes"></a>Definiowanie i konfigurowanie zakresów

[Zakresy](../active-directory/develop/developer-glossary.md#scopes) umożliwiają zarządzanie dostępem do chronionych zasobów. Zakresy są używane przez internetowy interfejs API w celu implementowania kontroli dostępu opartej na zakresach. Na przykład niektórzy użytkownicy mogą mieć dostęp do odczytu i zapisu, a inni użytkownicy mogą mieć uprawnienia tylko do odczytu. W tym samouczku zdefiniujesz uprawnienia do odczytu dla internetowego interfejsu API.

### <a name="define-scopes-for-the-web-api"></a>Definiowanie zakresów dla internetowego interfejsu API

Zarejestrowane interfejsy API są wyświetlane na liście aplikacji dla dzierżawy usługi Azure AD B2C. Wybierz swój internetowy interfejs API z listy. Zostanie wyświetlone okienko właściwości interfejsu API.

Kliknij pozycję **Opublikowane zakresy (wersja zapoznawcza)**.

Aby skonfigurować zakresy dla interfejsu API, dodaj następujące wpisy. 

![zakresy definiowane w internetowym interfejsie API](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Ustawienie      | Sugerowana wartość  | Opis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Zakres** | demo.read | Dostęp tylko do odczytu do pokazowego interfejsu API |

Kliknij pozycję **Zapisz**.

Opublikowane zakresy umożliwiają udzielenie aplikacji klienckiej uprawnień do internetowego interfejsu API.

### <a name="grant-app-permissions-to-web-api"></a>Udzielanie uprawnień aplikacji do internetowego interfejsu API

Aby wywoływać chroniony internetowy interfejs API z aplikacji, należy udzielić aplikacji uprawnień do tego interfejsu. W tym samouczku użyj aplikacji jednostronicowej utworzonej w [samouczku dotyczącym uwierzytelniania użytkowników za pomocą usługi Azure Active Directory B2C w aplikacji jednostronicowej (JavaScript)](active-directory-b2c-tutorials-spa.md).

1. W witrynie Azure Portal wybierz pozycję **Azure AD B2C** na liście usług i kliknij pozycję **Aplikacje**, aby wyświetlić listę zarejestrowanych aplikacji.

2. Wybierz pozycję **Moja przykładowa aplikacja jednostronicowa** na liście aplikacji i kliknij pozycję **Dostęp do interfejsu API (wersja zapoznawcza)**, a następnie pozycję **Dodaj**.

3. Na liście rozwijanej **Wybierz interfejs API** wybierz zarejestrowany internetowy interfejs API o nazwie **Hello Core API**.

4. Na liście rozwijanej **Wybierz zakresy** wybierz zakresy zdefiniowane podczas rejestracji internetowego interfejsu API.

    ![wybieranie zakresów dla aplikacji](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Kliknij przycisk **OK**.

Twoja aplikacja o nazwie **Moja przykładowa aplikacja jednostronicowa** została zarejestrowana do wywołania chronionego interfejsu **Hello Core API**. Użytkownik [uwierzytelnia się](../active-directory/develop/developer-glossary.md#authentication) w usłudze Azure AD B2C, aby korzystać z aplikacji jednostronicowej. Aplikacja jednostronicowa uzyskuje [autoryzację](../active-directory/develop/developer-glossary.md#authorization-grant) z usługi Azure AD B2C w celu uzyskiwania dostępu do chronionego internetowego interfejsu API.

## <a name="update-code"></a>Aktualizowanie kodu

Po zarejestrowaniu internetowego interfejsu API i zdefiniowaniu zakresów należy skonfigurować kod internetowego interfejsu API w celu korzystania z dzierżawy usługi Azure AD B2C. W tym samouczku skonfigurujesz przykładową aplikację internetową platformy .NET Core, którą można pobrać z witryny GitHub. 

[Pobierz plik zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) lub sklonuj przykładową aplikację internetową z usługi GitHub.

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
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
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

### <a name="configure-the-single-page-app"></a>Konfigurowanie aplikacji jednostronicowej

Aplikacja jednostronicowa używa usługi Azure AD B2C do tworzenia nowych kont i logowania użytkowników oraz wywołuje chroniony internetowy interfejs API platformy ASP.NET Core. Musisz zaktualizować wywołanie aplikacji jednostronicowej dla internetowego interfejsu platformy .NET Core.
Aby zmienić ustawienia aplikacji:

1. Otwórz plik `index.html` w przykładowej aplikacji jednostronicowej platformy Node.js.
2. Skonfiguruj przykład obejmujący informacje na temat rejestracji dzierżawy usługi Azure AD B2C. W poniższym kodzie dodaj nazwę swojej dzierżawy do pozycji **b2cScopes** i zmień wartość **webApi** na zapisaną wcześniej wartość ustawienia *applicationURL*:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Uruchamianie aplikacji SPA i internetowego interfejsu API

Musisz uruchomić aplikację jednostronicową platformy Node.js oraz internetowy interfejs API platformy .NET Core.

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
6. Kliknij przycisk **Wywołaj interfejs API**.

Po utworzeniu nowego konta lub zalogowaniu się przy użyciu konta użytkownika przykład wywołuje chronionej internetowy interfejs API i zwraca wynik.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne samouczki usługi Azure AD B2C, możesz użyć swojej dzierżawy usługi Azure AD B2C. Możesz [usunąć dzierżawę usługi Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant), gdy nie będzie już potrzebna.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono proces ochrony internetowego interfejsu API przez rejestrowanie i definiowanie zakresów w usłudze Azure AD B2C. Dowiedz się więcej, zapoznając się z dostępnymi przykładami kodu usługi Azure AD B2C.

> [!div class="nextstepaction"]
> [Przykłady kodu usługi Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
