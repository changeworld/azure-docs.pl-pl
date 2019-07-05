---
title: Platforma tożsamości usługi Microsoft Szybki Start aplikacji sieci web platformy ASP.NET Core | Azure
description: Dowiedz się, jak zaimplementować logowanie firmy Microsoft w aplikacji internetowej ASP.NET Core za pomocą protokołu OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d13786dc731627ba2000ab6069ea06ed3183ba
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565461"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Szybki start: dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

W tym przewodniku Szybki start dowiesz się, w jaki sposób aplikacja internetowa ASP.NET Core może przeprowadzać logowanie kont osobistych (hotmail.com, outlook.com itp.) oraz kont służbowych z dowolnego wystąpienia usługi Azure Active Directory (Azure AD).

![Pokazuje, jak działa przykładowej aplikacji wygenerowane przez ten przewodnik Szybki Start](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwie opcje uruchamiania aplikacji Szybki start:
> * [Ekspresowo] [Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ręcznie] [Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opcja 1: zarejestrowanie i automatyczne skonfigurowanie aplikacji, a następnie pobranie przykładowego kodu
>
> 1. Przejdź do [witryna Azure portal — rejestracje aplikacji](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Wprowadź nazwę aplikacji i wybierz pozycję **Zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami, aby jednym kliknięciem pobrać i automatycznie skonfigurować nową aplikację.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2: zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Rejestrowanie aplikacji
> Aby zarejestrować aplikację i ręcznie dodać informacje na temat rejestracji aplikacji do rozwiązania, wykonaj następujące czynności:
>
> 1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
> 1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
> 1. Przejdź do platforma tożsamości firmy Microsoft dla deweloperów [rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) strony.
> 1. Wybierz **nowej rejestracji**.
> 1. Po wyświetleniu strony **Rejestrowanie aplikacji** podaj informacje dotyczące rejestracji aplikacji:
>    - W sekcji **Nazwa** podaj znaczącą nazwę aplikacji, która będzie wyświetlana użytkownikom aplikacji, na przykład `AspNetCore-Quickstart`.
>    - W **identyfikator URI przekierowania**, Dodaj `https://localhost:44321/`i wybierz **zarejestrować**.
> 1. Wybierz menu **Uwierzytelnianie**, a następnie dodaj następujące informacje:
>    - W **identyfikatory URI przekierowań**, Dodaj `https://localhost:44321/signin-oidc`i wybierz **Zapisz**.
>    - W sekcji **Ustawienia zaawansowane** ustaw pole **Adres URL wylogowywania** na wartość `https://localhost:44321/signout-oidc`.
>    - W obszarze **Niejawne przyznanie** zaznacz pole wyboru **Tokeny Identyfikatorów**.
>    - Wybierz pozycję **Zapisz**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurowanie aplikacji w witrynie Azure Portal
> Aby przykład kodu w tym przewodniku Szybki Start działał, należy dodać adresy URL odpowiedzi jako `https://localhost:44321/` i `https://localhost:44321/signin-oidc`, dodać adres URL wylogowywania jako `https://localhost:44321/signout-oidc`, a następnie zażądać, aby tokeny Identyfikatorów były wystawiane przez punkt końcowy autoryzacji.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź tę zmianę automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-your-aspnet-core-project"></a>Krok 2: Pobieranie projektu ASP.NET Core

- [Pobierz rozwiązanie Visual Studio 2019 r.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Krok 3: Konfigurowanie projektu programu Visual Studio

1. Wyodrębnij plik zip do folderu lokalnego w ramach folderu głównego, na przykład **C:\Azure-Samples**.
1. Jeśli używasz programu Visual Studio 2019 r, otwórz rozwiązanie w programie Visual Studio (opcjonalne).
1. Edytuj plik **appsettings.json**. Znajdź `ClientId` i zaktualizuj wartość `ClientId` z **identyfikator aplikacji (klienta)** wartość aplikacja została zarejestrowana. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ten przewodnik szybkiego startu obsługuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Gdzie:
> - `Enter_the_Application_Id_here` to **identyfikator aplikacji (klienta)** dla aplikacji zarejestrowanej w witrynie Azure Portal. **Identyfikator aplikacji (klienta)** możesz znaleźć na stronie aplikacji **Przegląd** aplikacji.
> - `Enter_the_Tenant_Info_Here` to jedna z następujących opcji:
>   - Jeśli aplikacja obsługuje tryb **Konta tylko w tym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości **Identyfikator dzierżawy** lub **Nazwa dzierżawy** (na przykład contoso.microsoft.com)
>   - Jeśli aplikacja obsługuje tryb**Konta w dowolnym katalogu organizacyjnym**, zastąp tę wartość za pomocą wartości `organizations`
>   - Jeśli aplikacja obsługuje tryb **Wszyscy użytkownicy kont Microsoft**, zastąp tę wartość za pomocą wartości `common`
>
> > [!TIP]
> > Aby znaleźć wartości **Identyfikator aplikacji (klienta)** , **Identyfikator katalogu (dzierżawy)** i **Obsługiwane typy kont**, przejdź do strony **Przegląd** w witrynie Azure Portal.

## <a name="more-information"></a>Więcej informacji

Ta sekcja zawiera przegląd kodu jest wymagane do logowania użytkowników. W tym omówieniu może być grupowaniu można sprawdzić, jak działa kod, głównym argumentów, a także jeśli chcesz dodać logowanie do istniejącej aplikacji platformy ASP.NET Core.

### <a name="startup-class"></a>Klasa początkowa

Oprogramowanie pośredniczące *Microsoft.AspNetCore.Authentication* używa klasy początkowej, która jest wykonywana podczas inicjowania procesu hostingu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

Metoda `AddAuthentication` służy do konfigurowania usługi do dodania na podstawie plików cookie uwierzytelniania, który jest używany w scenariuszach przeglądarki i ustawić żądania uwierzytelniania OpenID Connect. 

Wiersz zawierający `.AddAzureAd` dodaje uwierzytelnianie platformy Microsoft tożsamości do aplikacji. Następnie jest on skonfigurowany do logowania za pomocą programu Microsoft platformy tożsamości z punktu końcowego.

> |Lokalizacja  |  |
> |---------|---------|
> | ClientId  | Identyfikator aplikacji (klient) z aplikacji zarejestrowanej w witrynie Azure Portal. |
> | Urząd | Punkt końcowy usługi STS na potrzeby uwierzytelnienia użytkownika. Zazwyczaj jest to adres <https://login.microsoftonline.com/{tenant}/v2.0> dla chmury publicznej, gdzie parametr {tenant} jest nazwą dzierżawy, identyfikatorem dzierżawy lub ma wartość *common* na potrzeby odwołania do wspólnego punktu końcowego (używany dla aplikacji z wieloma dzierżawami) |
> | TokenValidationParameters | Lista parametrów na potrzeby weryfikacji tokenu. W tym przypadku parametr `ValidateIssuer` ustawiono na wartość `false`, aby wskazać, że może akceptować logowania z dowolnych kont osobistych i służbowych. |


> [!NOTE]
> Ustawienie `ValidateIssuer = false` jest uproszczenia w tym przewodniku Szybki Start. W rzeczywistych aplikacjach należy do weryfikowania wystawcy.
> Zobacz przykłady, aby dowiedzieć się, jak to zrobić.

### <a name="protect-a-controller-or-a-controllers-method"></a>Ochrona kontrolera lub metody kontrolera

Kontroler lub jego metody można chronić za pomocą atrybutu `[Authorize]`. Ten atrybut ogranicza dostęp do kontrolera lub metody, zezwalając tylko uwierzytelnionych użytkowników, co oznacza, można uruchomić dostępu kontrolera, jeśli użytkownik nie jest uwierzytelniony do uwierzytelnienia.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z repozytorium GitHub na potrzeby tego samouczka platformy ASP.NET Core, aby uzyskać więcej informacji, w tym instrukcje dotyczące sposobu dodawania uwierzytelniania do zupełnie nowej aplikacji sieci Web platformy ASP.NET Core, jak wywołać program Microsoft Graph i inne APIs Microsoft sposób wywołania z własnymi interfejsami API, jak dodać autoryzacja, jak mają logować użytkowników w innych chmurach krajowej lub przy użyciu tożsamości społecznościowych i nie tylko:

> [!div class="nextstepaction"]
> [Samouczek dotyczący aplikacji sieci Web Core ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
