---
title: Chroniony internetowy interfejs API — konfiguracja kodu aplikacji
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i skonfigurować kod aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb562caf2dfc83841762748f37d1a7ee325de10b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73882622"
---
# <a name="protected-web-api-code-configuration"></a>Chroniony internetowy interfejs API: Konfiguracja kodu

Aby skonfigurować kod chronionego internetowego interfejsu API, należy zrozumieć, co definiuje interfejsy API jako chronione, jak skonfigurować token okaziciela oraz sprawdzić poprawność tokenu.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Co definiuje ASP.NET/ASP.NET podstawowe interfejsy API jako chronione?

Podobnie jak w przypadku aplikacji sieci Web, interfejsy API sieci Web ASP.NET/ASP.NET Core są chronione, ponieważ ich akcje kontrolera są poprzedzone atrybutem `[Authorize]`. Tak więc akcje kontrolera można wywołać tylko wtedy, gdy interfejs API jest wywoływany przy użyciu tożsamości, która jest autoryzowana.

Weź pod uwagę następujące pytania:

- Jak interfejs API sieci Web wie tożsamość aplikacji, która ją wywołuje? (Tylko aplikacja może wywołać interfejs API sieci Web).
- Jeśli aplikacja wywołała internetowy interfejs API w imieniu użytkownika, co to jest tożsamość użytkownika?

## <a name="bearer-token"></a>Token okaziciela

Informacje o tożsamości aplikacji oraz o użytkowniku (chyba że aplikacja sieci Web akceptuje wywołania Service to Service z aplikacji demona), jest przechowywany w tokenie okaziciela ustawionym w nagłówku, gdy aplikacja jest wywoływana.

Oto przykład C# kodu, który pokazuje klienta wywołującego interfejs API po uzyskaniu tokenu z biblioteką uwierzytelniania firmy Microsoft dla platformy .net (MSAL.NET):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Aplikacja kliencka zażądała tokenu okaziciela *dla internetowego interfejsu API*. Internetowy interfejs API jest jedyną aplikacją, która powinna zweryfikować token i wyświetlić zawarte w nim oświadczenia. Aplikacje klienckie nigdy nie powinny podejmować próby sprawdzenia oświadczeń w tokenach. (W przyszłości internetowy interfejs API może wymagać zaszyfrowania tokenu. Ten wymóg uniemożliwia dostęp dla aplikacji klienckich, które mogą wyświetlać tokeny dostępu.

## <a name="jwtbearer-configuration"></a>Konfiguracja JwtBearer

W tej sekcji opisano sposób konfigurowania tokenu okaziciela.

### <a name="config-file"></a>Plik konfiguracji

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Inicjowanie kodu

Gdy aplikacja jest wywoływana w ramach akcji kontrolera, która zawiera atrybut `[Authorize]`, ASP.NET/ASP.NET rdzeń przegląda token okaziciela w nagłówku autoryzacji żądania wywołania i wyodrębnia token dostępu. Token jest następnie przekazywany do oprogramowania pośredniczącego JwtBearer, które wywołuje rozszerzenia Microsoft IdentityModel dla platformy .NET.

W ASP.NET Core to oprogramowanie pośredniczące jest inicjowane w pliku Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Oprogramowanie pośredniczące jest dodawane do internetowego interfejsu API przez tę instrukcję:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Obecnie szablony ASP.NET Core tworzą interfejsy API sieci Web w usłudze Azure Active Directory (Azure AD), które logują użytkowników w organizacji lub dowolnej organizacji, a nie z kontami osobistymi. Można jednak łatwo zmienić je tak, aby korzystały z punktu końcowego platformy tożsamości firmy Microsoft przez dodanie tego kodu do pliku Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Ten fragment kodu jest wyodrębniany z wielostopniowego samouczka interfejsu API sieci Web w ASP.NET Core [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Metoda `AddProtectedWebApi`, która wykonuje wiele więcej, jest wywoływana z Startup.cs

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

Oprogramowanie pośredniczące JwtBearer, takie jak OpenID Connect Połącz oprogramowanie pośredniczące w aplikacjach sieci Web, jest kierowane przez `TokenValidationParameters`, aby sprawdzić poprawność tokenu. Token jest odszyfrowywany (w razie konieczności), oświadczenia są wyodrębniane i podpis zostanie zweryfikowany. Oprogramowanie pośredniczące sprawdza token, sprawdzając, czy są to dane:

- Jest on przeznaczony dla internetowego interfejsu API.
- Został wystawiony dla aplikacji, która może wywołać internetowy interfejs API (Sub).
- Zostało wystawione przez zaufaną usługę tokenu zabezpieczającego (STS) (wystawca).
- Jego okres istnienia jest w zakresie (wygasa).
- Nie zostało naruszone (Sygnatura).

Mogą być również specjalne walidacje. Można na przykład sprawdzić, czy klucze podpisywania (osadzone w tokenie) są zaufane i czy token nie jest odtwarzany. Na koniec niektóre protokoły wymagają określonych walidacji.

### <a name="validators"></a>Modułów sprawdzania

Kroki walidacji są przechwytywane w modułach walidacji, które znajdują się w programie [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Open Source Library, w jednym pliku źródłowym: [Microsoft. IdentityModel. tokeny/walidators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

W tej tabeli opisano moduły sprawdzania poprawności:

| Walidacj | Opis |
|---------|---------|
| `ValidateAudience` | Zapewnia, że token jest przeznaczony dla aplikacji, która sprawdza poprawność tokenu (dla mnie). |
| `ValidateIssuer` | Zapewnia, że token został wystawiony przez zaufaną usługę STS (od zaufanej osoby). |
| `ValidateIssuerSigningKey` | Gwarantuje, że aplikacja walidacji tokenu ufa klucz, który został użyty do podpisania tokenu. (Przypadek specjalny, gdzie klucz jest osadzony w tokenie. Zwykle nie jest to wymagane.) |
| `ValidateLifetime` | Zapewnia, że token jest nadal (lub już) prawidłowy. Moduł sprawdzania poprawności sprawdza, czy okres istnienia tokenu (`notbefore` i `expires` oświadczeń) jest w zakresie. |
| `ValidateSignature` | Zapewnia, że token nie został naruszony. |
| `ValidateTokenReplay` | Zapewnia, że token nie jest odtwarzany. (Specjalny przypadek dla niektórych jednorazowej używanych protokołów). |

Wszystkie moduły walidacji są skojarzone z właściwościami klasy `TokenValidationParameters`, które są inicjowane z poziomu konfiguracji ASP.NET/ASP.NET Core. W większości przypadków nie trzeba zmieniać parametrów. Istnieje jeden wyjątek dla aplikacji, które nie są pojedynczymi dzierżawcami. (Oznacza to, że aplikacje sieci Web, które akceptują użytkowników z dowolnej organizacji lub osobistych kont Microsoft). W takim przypadku wystawca musi być zweryfikowany.

## <a name="token-validation-in-azure-functions"></a>Sprawdzanie poprawności tokenu w Azure Functions

Możliwe jest również zweryfikowanie tokenów dostępu przychodzącego w usłudze Azure Functions. Przykłady sprawdzania poprawności tokenów w usłudze Azure Functions można znaleźć w usłudze [dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)i [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Weryfikowanie zakresów i ról aplikacji w kodzie](scenario-protected-web-api-verification-scope-app-roles.md)
