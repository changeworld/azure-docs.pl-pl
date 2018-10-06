---
title: Usługa Azure AD v2.0 Szybki Start aplikacji sieci web platformy ASP.NET Core | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaimplementować logowania firmy Microsoft w aplikacji internetowej ASP.NET Core przy użyciu protokołu OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ab3d0b74e8305d67af862020197c69b15221086
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830229"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Dodawanie logowania firmy Microsoft do aplikacji sieci web platformy ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ten przewodnik Szybki Start zawiera przykładowy kod, który demonstruje, jak zalogować się aplikacji internetowej ASP.NET Core w osobiste (hotmail.com, live.com, inne) i pracy kont służbowych z dowolnego wystąpienia usługi Azure Active Directory.

![Jak działa przykładowa aplikacja wygenerowana w tym przewodniku Szybki start](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Rejestrowanie aplikacji i pobieranie aplikacji Szybki start
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Rejestrowanie i konfigurowanie aplikacji i przykładowego kodu
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
> 
> 1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Wprowadź nazwę aplikacji, upewnij się, że pole wyboru **Guided Setup** (Konfiguracja z przewodnikiem) nie jest zaznaczone, a następnie kliknij przycisk **Create** (Utwórz).
> 1. Kliknij pozycję `Add Platform`, a następnie wybierz pozycję `Web`.
> 1. Upewnij się, że pole wyboru **Zezwalaj na niejawny przepływ** jest *zaznaczone*.
> 1. W polu **Identyfikatory URL przekierowań** wprowadź ciąg `http://localhost:3110/`.
> 1. Przewiń w dół do końca strony, a następnie kliknij pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu z tego samouczka działał, należy jako adres URL odpowiedzi dodać adres `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Already configured](media/quickstart-v2-aspnet-core-webapp/green-check.png) (Już skonfigurowano) Twoja aplikacja została skonfigurowana za pomocą tego atrybutu

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Pobieranie usługi projektu platformy ASP.NET Core

- [Pobieranie projektu ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3. Konfigurowanie projektu

1. Wyodrębnij plik zip do folderu lokalnego bliżej do folderu głównego — na przykład **C:\Azure-Samples**
1. Jeśli używasz programu Visual Studio 2017, otwórz projekt w programie Visual Studio (opcjonalne)
1. Edytuj **appsettings.json** i zastąp wartość `ClientId` identyfikatorem aplikacji z poziomu aplikacji, które właśnie zostało zarejestrowane:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Jeśli aplikacja jest *aplikacji z jedną dzierżawą* (przeznaczonych dla kont w bieżącym katalogu) w swojej **appsettings.json** plików, Znajdź wartość `TenantId` i Zastąp `common`za pomocą usługi **identyfikator dzierżawy** lub **nazwa dzierżawy** (na przykład contoso.microsoft.com). Nazwę dzierżawy możesz uzyskać na stronie **Przegląd**.

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera omówienie kodu wymaganego do logowania użytkowników. Może to być przydatne zrozumieć, jak działa kod, główne argumenty, a także jeśli chcesz dodać logowanie do istniejącej aplikacji platformy ASP.NET Core.

### <a name="startup-class"></a>Klasa początkowa

*Microsoft.AspNetCore.Authentication* oprogramowania pośredniczącego używa klasy startowy, który jest wykonywany, gdy inicjuje proces hostingu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

Metoda `AddAuthentication` służy do konfigurowania usługi do dodania uwierzytelniania na podstawie plików Cookie — używany w scenariuszach przeglądarki, a także równa wyzwanie OpenIdConnect. 

Wiersz zawierający `.AddAzureAd` dodaje uwierzytelniania usługi Azure Active Directory do aplikacji.

Poza tym, że plik **AzureAdAuthenticationBuilderExtensions.cs** dodaje metody rozszerzenia do potoku uwierzytelniania usługi Azure AD. Ta metoda rozszerzenia skonfiguruj atrybuty wymagane dla usługi Azure AD Authentication. `Configure` Method in Class metoda `IConfigureNamedOptions` interfejs zawiera następujące czynności:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Lokalizacja  |  |
> |---------|---------|
> |ClientId     |Identyfikator aplikacji z aplikacji zarejestrowany w witrynie Azure portal|
> |Urząd | Użytkownik fo punktu końcowego usługi STS do uwierzytelniania. Zazwyczaj jest to adres https://login.microsoftonline.com/{tenant}/v2.0 dla chmury publicznej, gdzie parametr {tenant} jest nazwą dzierżawy, identyfikatorem dzierżawy lub ma wartość *common* na potrzeby odwołania do wspólnego punktu końcowego (używany dla aplikacji z wieloma dzierżawami)|
> |UseTokenLifetime |Wskazuje, że plik cookie uwierzytelniania powinien odpowiadać okresowi istnienia tokenu uwierzytelniania|
> |RequireHttpsMetadata     |Wymaganie protokołu HTTPS na adres metadanych lub urzędu. Zaleca się zmienić tę wartość na `True`|
> |Element TokenValidationParameters.     | Lista parametrów na potrzeby weryfikacji tokenu. W tym przypadku `ValidateIssuer` ustawiono `false` do wskazania, że może akceptować logowania z dowolnym osobiste lub konta służbowego lub szkolnego|

### <a name="initiate-an-authentication-challenge"></a>Inicjowanie żądania uwierzytelnienia

Można wymusić użytkownikowi zalogowanie, wysyłając żądanie uwierzytelnienia w kontrolerze, podobnie jak w **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Żądanie uwierzytelnienia przy użyciu powyższej metody jest opcjonalne i commonsly używany, gdy chcesz, aby widok był dostępny do uwierzytelnionego i nieuwierzytelnionych użytkowników. Kontrolery można chronić przy użyciu metody opisanej w następnej sekcji.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrona kontrolera lub metody kontrolera

Można chronić kontroler lub kontrolera methodss za pomocą `[Authorize]` atrybutu. Ten atrybut ogranicza dostęp do kontrolera lub metody, zezwalając tylko uwierzytelnieni użytkownicy — co oznacza, że można uruchomić dostępu kontrolera, jeśli użytkownik nie jest uwierzytelniony do uwierzytelnienia.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z repozytorium GitHub dla tej platformy ASP.NET Core Szybki Start: więcej informacji — w tym instrukcje dotyczące sposobu Dodawanie uwierzytelniania do zupełnie nowej aplikacji sieci Web platformy ASP.NET Core:

> [!div class="nextstepaction"]
> [Przykładowy kod aplikacji sieci Web platformy ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]