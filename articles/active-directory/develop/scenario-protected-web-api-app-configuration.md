---
title: Chroniony internetowy interfejs API — konfiguracji kodu aplikacji | Azure
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i konfigurowanie kodu aplikacji.
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
ms.openlocfilehash: bdd68d9ec5d0dd83df4628f39785ce255482245d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111166"
---
# <a name="protected-web-api---code-configuration"></a>Chroniony internetowy interfejs API — Konfiguracja kodu

Aby pomyślnie skonfigurować kod dla chronionego internetowego interfejsu API, musisz zrozumieć, co sprawia, że interfejsy API, chronione, należy skonfigurować token elementu nośnego i sposób sprawdzania poprawności tokenu.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>Co sprawia, że ASP.NET/ASP.NET podstawowe interfejsy API chronione?

Tak jak w aplikacji sieci web, ASP.NET/ASP.NET podstawowe interfejsy API są "chronione" w sieci web, ponieważ ich akcji kontrolera mają prefiks `[Authorize]` atrybutu. W związku z akcji kontrolera można wywołać tylko interfejs API jest wywoływana przy użyciu tożsamości, który jest autoryzowany.

Należy rozważyć następujące kwestie:

- Jak internetowy interfejs API znać tożsamość aplikacji, która wywołuje on (tylko aplikacja może wywołać interfejs API sieci web)?
- Jeśli aplikacja wywołuje internetowy interfejs API w imieniu użytkownika, co to jest tożsamością użytkownika?

## <a name="bearer-token"></a>Token elementu nośnego

Informacje o tożsamości aplikacji, a także o użytkowniku (chyba że aplikacja internetowa akceptuje wywołania do usługi z poziomu aplikacji demona), są przechowywane w token elementu nośnego, który jest ustawiony w nagłówku podczas wywoływania aplikacji.

Oto C# przykładu kodu, który przedstawia wywołania interfejsu API po uzyskaniu tokenu przy użyciu platformy MSAL.NET klienta.

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
> Zażądano tokenu elementu nośnego przez aplikację kliencką do endpoint platforma tożsamości firmy Microsoft **dla internetowego interfejsu API**. Interfejs API sieci web to tylko aplikacji, która powinna zweryfikowania tokenu i przyjrzyj się oświadczenia, które zawiera. Aplikacje klienckie nigdy nie należy rozważyć oświadczenia w tokeny (internetowy interfejs API może zdecydować, w przyszłości, że zażąda szyfrowane token i spowoduje to przerwanie aplikacja kliencka, której można otworzyć tokeny dostępu).

## <a name="jwtbearer-configuration"></a>Konfiguracja JwtBearer

Tej sekcji opisano, co jest potrzebne do skonfigurowania tokenu elementu nośnego.

### <a name="config-file"></a>Plik konfiguracji

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

### <a name="code-initialization"></a>Kod inicjowania

Gdy aplikacja jest wywoływana w gospodarstwie akcji kontrolera `[Authorize]` atrybutu, ASP.NET/ASP.NET core analizuje tokenu elementu nośnego w nagłówku autoryzacji żądania wywołującego i wyodrębnia tokenu dostępu, który jest następnie przekazywany do JwtBearer oprogramowanie pośredniczące, która wywołuje rozszerzenia modelu tożsamości firmy Microsoft dla platformy .NET.

W programie ASP.NET Core to oprogramowanie pośredniczące jest inicjowana w `Startup.cs` pliku.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Oprogramowanie pośredniczące zostanie dodany do internetowego interfejsu API, według następujących instrukcji:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Obecnie szablony ASP.NET Core tworzyć usługi Azure AD w wersji 1.0 internetowych interfejsów API. Jednak można łatwo zmienić ich do używania punktu końcowego platformy tożsamości firmy Microsoft, dodając następujący kod w `Startup.cs` pliku.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Weryfikacja tokenu

Oprogramowanie pośredniczące JwtBearer, takich jak oprogramowania pośredniczącego OpenID Connect, w aplikacji sieci web, jest wskazany przez `TokenValidationParameters` można zweryfikować tokenu. Token jest odszyfrowywany (jeśli jest to konieczne), oświadczenia są wyodrębniane i podpis jest weryfikowany. Następnie zweryfikowaniu tokenu przez sprawdzenie, czy następujące dane:

- Jest ona przeznaczona dla internetowego interfejsu API (odbiorcy)
- Został wystawiony dla aplikacji, która może wywoływać internetowy interfejs API (pod)
- Został wystawiony przez godne zaufania reguły zabezpieczeń tokenu serwera (STS) (Wystawca)
- Okres istnienia tokenu znajduje się w zakresie (wygaśnięcia)
- Nie została naruszona (podpis)

Można także specjalnych operacji sprawdzania poprawności. Na przykład jest to możliwe zweryfikować, że klucze podpisywania, (gdy są osadzone w tokenie) są zaufane, i czy token nie jest odtwarzany. Ponadto niektóre protokoły wymagają określonych operacji sprawdzania poprawności.

### <a name="validators"></a>Moduły weryfikacji

Kroki weryfikacji są przechwytywane do modułów sprawdzania poprawności, które są w [rozszerzenia modelu tożsamości firmy Microsoft dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteki typu open source, w jednym pliku źródłowym: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Moduły weryfikacji zostały opisane w poniższej tabeli:

| Moduł sprawdzania poprawności | Opis |
|---------|---------|
| `ValidateAudience` | Zapewnia tokenu dla aplikacji, która sprawdza poprawność tokenu (dla mnie). |
| `ValidateIssuer` | Gwarantuje, czy token został wystawiony przez zaufany usługi STS (od osoby, której można zaufać). |
| `ValidateIssuerSigningKey` | Zapewnia aplikacji, sprawdzanie poprawności tokenu relacji zaufania, klucz, który został użyty do podpisywania tokenu (przypadek specjalny, gdy klucz jest osadzone w tokenie, zazwyczaj nie wymagane). |
| `ValidateLifetime` | Zapewnia, że token jest prawidłowy nadal (lub już). Wykonywane przez sprawdzanie okres istnienia tokenu (`notbefore`, `expires` oświadczenia) znajduje się w zakresie. |
| `ValidateSignature` | Zapewnia, że token nie została zmieniona. |
| `ValidateTokenReplay` | Zapewnia token nie jest odtwarzany (specjalny przypadek niektóre protokoły jednorazowej Użyj). |

Moduły weryfikacji wszystkie wystąpienia są skojarzone z właściwościami `TokenValidationParameters` klasy samodzielnie inicjowany z konfiguracji ASP.NET/ASP.NET Core. W większości przypadków nie trzeba zmieniać parametrów. Istnieje jeden wyjątek dla aplikacji, które nie są jednym dzierżaw (czyli aplikacji sieci web, które akceptują użytkowników z organizacji lub osobistych kont Microsoft) — w takim przypadku należy sprawdzić poprawność wystawcy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Sprawdź zakresach i rolach aplikacji w kodzie](scenario-protected-web-api-verification-scope-app-roles.md)
