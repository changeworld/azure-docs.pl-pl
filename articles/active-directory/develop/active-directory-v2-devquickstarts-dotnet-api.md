---
title: Dodaj logowania do sieci web .NET MVC interfejsu API przy użyciu punktu końcowego v2.0 usługi Azure AD | Dokumentacja firmy Microsoft
description: Jak utworzyć interfejs Api sieci Web .NET MVC, który akceptuje tokeny od obu Account firmy Microsoft i konta firmowego lub szkolnego.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: aa73e918cbd49fee850e402859708ba0c4185a19
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="secure-an-mvc-web-api"></a>Zabezpiecz interfejs API web MVC
Z usługą Azure Active Directory punktu końcowego v2.0, można chronić przy użyciu interfejsu API sieci Web [OAuth 2.0](active-directory-v2-protocols.md) dostęp do tokenów, umożliwiając użytkownikom z obu osobistego konta Microsoft i służbowego konta do bezpiecznego dostępu do interfejsu API sieci Web.

> [!NOTE]
> Nie wszystkie usługi Azure Active Directory scenariuszy i funkcji obsługiwanych przez punktu końcowego v2.0. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

W sieci web ASP.NET interfejsów API można to zrobić za pomocą oprogramowania pośredniczącego OWIN firmy Microsoft włączone w programie .NET Framework 4.5. W tym miejscu użyjemy OWIN do tworzenia interfejs API sieci Web MVC "Lista zadań do wykonania", który pozwala na tworzenie i odczytywanie zadań z listy zadań do wykonania przez użytkownika. Interfejs API sieci web zostanie przeprowadzona Weryfikacja, czy żądania przychodzące zawiera prawidłowy dostęp do tokenu i Odrzuć wszelkie żądania nie przeszedł pomyślnie weryfikacji w trasie chronionych. W tym przykładzie został utworzony przy użyciu programu Visual Studio 2015.

## <a name="download"></a>Do pobrania
Kod używany w tym samouczku jest przechowywany [ w serwisie GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Aby z niego skorzystać, można [pobrać szkielet aplikacji w formie .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) lub sklonować szkielet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Szkielet aplikacji obejmuje całego kodu umożliwiającego prosty interfejs API, ale brakuje wszystkie elementy związane z tożsamości. Jeśli nie chcesz z niego skorzystać, zamiast tego można sklonować lub [Pobieranie ukończone próbki](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Rejestracja aplikacji
Utwórz nową aplikację na [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub wykonaj następujące [szczegółowe kroki](active-directory-v2-app-registration.md). Upewnij się, że:

* Skopiuj **identyfikator aplikacji** przypisany do aplikacji, będzie on potrzebny wkrótce.

To rozwiązanie visual studio zawiera także "TodoListClient", która jest prosta aplikacja WPF. TodoListClient służy do pokazują, jak użytkownika logowania i jak klient może wysyłać żądania do interfejsu API sieci Web. W takim przypadku zarówno TodoListClient i TodoListService są reprezentowane przez tej samej aplikacji. Aby skonfigurować TodoListClient, należy również:

* Dodaj **Mobile** platformy aplikacji.

## <a name="install-owin"></a>Instalowanie interfejsu OWIN
Teraz, gdy został zarejestrowany aplikację, musisz skonfigurować aplikację do komunikacji z punktem końcowym v2.0 do weryfikacji przychodzących żądań i tokenów.

* Aby rozpocząć, otwórz rozwiązanie i dodawanie pakietów NuGet oprogramowanie pośredniczące OWIN do projektu TodoListService przy użyciu konsoli Menedżera pakietów.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Konfigurowanie uwierzytelniania OAuth
* Dodawanie klasy początkowej OWIN do projektu TodoListService o nazwie `Startup.cs`. Kliknij prawym przyciskiem projekt myszy--> **Dodaj** --> **nowy element** --> Wyszukaj "OWIN". Oprogramowanie pośredniczące OWIN wywoła metodę `Configuration(…)` podczas uruchamiania aplikacji.
* Deklaracja klasy, aby zmienić `public partial class Startup` — już zaimplementowano część tej klasy dla Ciebie w innym pliku. W `Configuration(…)` metody, utworzyć ConfgureAuth(...) wywołana w celu ustawienia uwierzytelniania dla aplikacji sieci web.

```csharp
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Otwórz plik `App_Start\Startup.Auth.cs` i wdrożenie `ConfigureAuth(…)` metodę, która zostanie skonfigurowany w interfejsie API sieci Web akceptuje tokeny od punktu końcowego v2.0.

```csharp
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app. Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint. Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Teraz można używać `[Authorize]` atrybutów ochrony kontrolerów i akcji z uwierzytelniania elementu nośnego OAuth 2.0. Dekoracji `Controllers\TodoListController.cs` klasy z tagiem autoryzacji. Spowoduje to wymuszenie użytkownika do logowania przed uzyskaniem dostępu do tej strony.

```csharp
[Authorize]
public class TodoListController : ApiController
{
```

* Gdy Autoryzowany rozmówca pomyślnie wywołuje jeden z `TodoListController` interfejsów API, działania mogą wymagać dostępu do informacji o elemencie wywołującym. OWIN zapewnia dostęp do oświadczeń wewnątrz tokenu elementu nośnego za pośrednictwem `ClaimsPrincipal` obiektu. 

```csharp
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call. In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Na koniec Otwórz `web.config` plików w folderze głównym projektu TodoListService, a następnie wprowadź wartości konfiguracji w `<appSettings>` sekcji.
  * Twoje `ida:Audience` jest **identyfikator aplikacji** aplikacji, którą wprowadzono w portalu.

## <a name="configure-the-client-app"></a>Konfigurowanie aplikacji klienta
Zanim zobaczysz usługi listy Todo w akcji, należy skonfigurować klienta listy Todo, więc mogą uzyskać tokeny z punktem końcowym v2.0 ani nawiązywania połączeń z usługą.

* W projekcie TodoListClient Otwórz `App.config` , a następnie wprowadź wartości konfiguracji w `<appSettings>` sekcji.
  * Twoje `ida:ClientId` identyfikator aplikacji został skopiowany z portalu.

Ponadto wyczyścić, skompilować i uruchomić każdy projekt!  Masz teraz interfejs API sieci Web .NET MVC, który akceptuje tokeny od oba osobistego konta Microsoft i konta firmowego lub szkolnego. Zaloguj się do TodoListClient i wywoływania składnika web api, aby dodać zadania do listy zadań do wykonania przez użytkownika.

Próbka ukończone (bez wartości konfiguracji) [jest dostarczane jako zip w tym miejscu](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), lub można ją sklonować z serwisu GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Kolejne kroki
Możesz teraz przejść do dodatkowe tematy. Można spróbować:

[Wywoływanie interfejsu API sieci Web z aplikacji sieci Web >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Aby uzyskać dodatkowe zasoby Zobacz:

* [Przewodnik dewelopera v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Tag StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Pobierz aktualizacje zabezpieczeń naszych produktów
Firma Microsoft zachęca do przekazywania powiadomień o występujących incydentach zabezpieczeń poprzez wizytę na [tej stronie](https://technet.microsoft.com/security/dd252948) i subskrybowanie Doradczych alertów zabezpieczeń.
