---
title: Migrowanie interfejsów API opartych na ujmowanie w celu b2clogin.com
titleSuffix: Azure AD B2C
description: Dowiedz się, jak włączyć internetowy interfejs API platformy .NET do obsługi tokenów wystawionych przez wielu wystawców tokenów podczas migracji aplikacji w celu b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184098"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migrowanie internetowego interfejsu API opartego na usłudze OWIN do b2clogin.com

W tym artykule opisano technikę włączania obsługi wielu wystawców tokenów w internetowych interfejsach API implementujących [otwarty interfejs sieci Web dla platformy .NET (OWIN).](http://owin.org/) Obsługa wielu punktów końcowych tokenów jest przydatna podczas migracji interfejsów API usługi Azure Active Directory B2C (Azure AD B2C) i ich aplikacji z *login.microsoftonline.com* do *b2clogin.com.*

Dodając obsługę w interfejsie API do akceptowania tokenów wystawionych przez b2clogin.com i login.microsoftonline.com, można migrować aplikacje sieci web w sposób etapowy przed usunięciem pomocy technicznej dla tokenów wydanych przez login.microsoftonline.com z interfejsu API.

W poniższych sekcjach przedstawiono przykład włączania wielu wystawców w internetowym interfejsie API, który używa składników oprogramowania pośredniczącego [Microsoft OWIN][katana] (Katana). Chociaż przykłady kodu są specyficzne dla oprogramowania pośredniczącego OWIN firmy Microsoft, ogólna technika powinna mieć zastosowanie do innych bibliotek OWIN.

> [!NOTE]
> Ten artykuł jest przeznaczony dla klientów usługi Azure AD B2C z `login.microsoftonline.com` aktualnie wdrożonymi interfejsami `b2clogin.com` API i aplikacjami, które się odwołują i którzy chcą przeprowadzić migrację do zalecanego punktu końcowego. Jeśli konfigurujesz nową aplikację, użyj [b2clogin.com](b2clogin.md) zgodnie z zaleceniami.

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem czynności określonych w tym artykule potrzebne są następujące zasoby usługi Azure AD B2C:

* [Przepływy użytkowników](tutorial-create-user-flows.md) lub [niestandardowe zasady](custom-policy-get-started.md) utworzone w dzierżawie

## <a name="get-token-issuer-endpoints"></a>Pobierz punkty końcowe wystawcy tokenów

Najpierw należy uzyskać identyfikatory identyfikatorów URI punktu końcowego wystawcy tokenu dla każdego wystawcy, który chcesz obsługiwać w interfejsie API. Aby uzyskać *b2clogin.com* i *login.microsoftonline.com* punktów końcowych obsługiwanych przez dzierżawę usługi Azure AD B2C, należy wykonać poniższą procedurę w witrynie Azure portal.

Zacznij od wybrania jednego z istniejących przepływów użytkownika:

1. Przejdź do dzierżawy usługi Azure AD B2C w [witrynie Azure portal](https://portal.azure.com)
1. W obszarze **Zasady**wybierz **pozycję Przepływy użytkownika (zasady)**
1. Wybierz istniejącą zasadę, na przykład *B2C_1_signupsignin1*, a następnie wybierz pozycję **Uruchom przepływ użytkownika**
1. W obszarze **Nagłówek Przepływ użytkownika w** górnej części strony wybierz hiperłącze, aby przejść do punktu końcowego odnajdywania OpenID Connect dla tego przepływu użytkownika.

    ![Dobrze znane hiperłącze identyfikatora URI na stronie Uruchom teraz w witrynie Azure portal](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na stronie, która zostanie otwarta `issuer` w przeglądarce, zapisz wartość, na przykład:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Użyj listy rozwijanej **Wybierz domenę,** aby wybrać inną domenę, a `issuer` następnie wykonaj dwa poprzednie kroki ponownie i zarejestruj jej wartość.

Teraz powinny być rejestrowane dwa identyfikatory URI, które są podobne do:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Zasady niestandardowe

Jeśli masz zasady niestandardowe zamiast przepływów użytkowników, można użyć podobnego procesu, aby uzyskać identyfikatory URI wystawcy.

1. Przejdź do dzierżawy usługi Azure AD B2C
1. Wybierz **strukturę środowiska tożsamości**
1. Wybierz jedną z zasad jednostki uzależniającej, na przykład *B2C_1A_signup_signin*
1. Użyj listy rozwijanej **Wybierz domenę,** aby wybrać domenę, na przykład *yourtenant.b2clogin.com*
1. Zaznaczanie hiperłącza wyświetlanego w **punkcie końcowym odnajdywania OpenID Connect**
1. Rejestrowanie `issuer` wartości
1. Wykonaj kroki 4-6 dla innej domeny, na przykład *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Pobieranie przykładowego kodu

Teraz, gdy masz zarówno identyfikatory identyfikatorów URI punktu końcowego tokenu, należy zaktualizować kod, aby określić, że oba punkty końcowe są prawidłowe wystawców. Aby przejść przez przykład, pobierz lub sklonuj przykładową aplikację, a następnie zaktualizuj przykład do obsługi obu punktów końcowych jako prawidłowych wystawców.

Pobierz archiwum: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Włączanie wielu wystawców w interfejsie API sieci Web

W tej sekcji zaktualizuj kod, aby określić, że oba punkty końcowe wystawcy tokenu są prawidłowe.

1. Otwórz rozwiązanie **B2C-WebAPI-DotNet.sln** w programie Visual Studio
1. W projekcie **TaskService** otwórz plik *App_Start Usługi Startup.Auth.cs\\\\*** w edytorze
1. Dodaj następującą `using` dyrektywę do górnej części pliku:

    `using System.Collections.Generic;`
1. Dodaj [`ValidIssuers`][validissuers] właściwość [`TokenValidationParameters`][tokenvalidationparameters] do definicji i określ zarówno identyfikatory URI zarejestrowane w poprzedniej sekcji:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`jest dostarczany przez MSAL.NET i jest zużywany przez oprogramowanie pośredniczące OWIN w następnej sekcji kodu w *Startup.Auth.cs*. Z wielu prawidłowych wystawców określonych potoku aplikacji OWIN jest świadomy, że oba punkty końcowe tokenu są prawidłowe wystawców.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Jak wspomniano wcześniej, inne biblioteki OWIN zazwyczaj zapewniają podobną możliwość obsługi wielu wystawców. Chociaż podając przykłady dla każdej biblioteki jest poza zakresem tego artykułu, można użyć podobnej techniki dla większości bibliotek.

## <a name="switch-endpoints-in-web-app"></a>Przełączanie punktów końcowych w aplikacji sieci Web

Z obu identyfikatorów URI teraz obsługiwane przez interfejs API sieci web, teraz trzeba zaktualizować aplikację sieci web, tak aby pobierał tokeny z punktu końcowego b2clogin.com.

Na przykład można skonfigurować przykładową aplikację sieci web do używania nowego punktu końcowego, modyfikując `ida:AadInstance` wartość w pliku *TaskWebApp\\**Web.config*** projektu **TaskWebApp.**

Zmień `ida:AadInstance` wartość w *web.config* taskwebapp tak, `{your-b2c-tenant-name}.b2clogin.com` aby odwoływać się zamiast `login.microsoftonline.com`.

Przed:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Po (zastąp `{your-b2c-tenant}` nazwą dzierżawy B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Gdy ciągi punktu końcowego są konstruowane podczas wykonywania aplikacji sieci web, b2clogin.com oparte na punktach końcowych są używane, gdy żąda tokenów.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono metodę konfigurowania internetowego interfejsu API implementującego oprogramowanie pośredniczące OWIN firmy Microsoft (Katana) w celu akceptowania tokenów z wielu punktów końcowych wystawcy. Jak można zauważyć, istnieje kilka innych ciągów w *plikach Web.Config* projektów zarówno TaskService i TaskWebApp projektów, które musiałyby zostać zmienione, jeśli chcesz tworzyć i uruchamiać te projekty przed własną dzierżawą. Możesz odpowiednio zmodyfikować projekty, jeśli chcesz je zobaczyć w działaniu, jednak pełne przejście do tego jest poza zakresem tego artykułu.

Aby uzyskać więcej informacji na temat różnych typów tokenów zabezpieczających emitowanych przez usługę Azure AD B2C, zobacz [Omówienie tokenów w usłudze Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
