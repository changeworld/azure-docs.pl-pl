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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536900"
---
# <a name="protected-web-api-code-configuration"></a>Chroniony internetowy interfejs API: Konfiguracja kodu

Aby skonfigurować kod dla chronionego internetowego interfejsu API, należy zrozumieć definiuje interfejsów API jako chroniony, jak skonfigurować token elementu nośnego i sposób sprawdzania poprawności tokenu.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Definiuje ASP.NET/ASP.NET podstawowe interfejsy API jako chronione?

Takich jak aplikacje sieci web, interfejsów API sieci web ASP.NET/ASP.NET Core są "chronione", ponieważ ich akcji kontrolera mają prefiks `[Authorize]` atrybutu. Dlatego akcji kontrolera można wywołać tylko wtedy, gdy interfejs API jest wywoływana przy użyciu tożsamości, który jest autoryzowany.

Należy rozważyć następujące kwestie:

- Jak internetowy interfejs API znać tożsamość aplikacji, który ją wywołuje? (Tylko aplikację można wywołać interfejsu API sieci web).
- Jeśli aplikacja wywołuje internetowy interfejs API w imieniu użytkownika, co to jest tożsamością użytkownika?

## <a name="bearer-token"></a>Token elementu nośnego

Informacje o tożsamości aplikacji, a także o użytkowniku (chyba że aplikacja sieci web akceptuje wywołań do usługi z poziomu aplikacji demona), są przechowywane w token elementu nośnego, który jest ustawiony w nagłówku, gdy aplikacja jest wywoływana.

Oto C# przykładu kodu, który pokazuje klienta wywołania interfejsu API po uzyskuje token z Biblioteka Microsoft Authentication Library for .NET (platformy MSAL.NET):

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
> Zażądano tokenu elementu nośnego przez aplikację kliencką do endpoint platforma tożsamości firmy Microsoft *dla internetowego interfejsu API*. Interfejs API sieci web to tylko aplikacji, która należy zweryfikować tokenu i wyświetlić oświadczenia, które zawiera. Aplikacje klienckie nigdy nie należy próbować sprawdzanie oświadczenia w tokeny. (Interfejs API sieci web może wymagać, w przyszłości, szyfrowane tokenu. To wymaganie uniemożliwi dostęp do aplikacji klienckich, które może wyświetlać tokeny dostępu).

## <a name="jwtbearer-configuration"></a>Konfiguracja JwtBearer

W tej sekcji opisano sposób konfigurowania tokenu elementu nośnego.

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

### <a name="code-initialization"></a>Kod inicjowania

Gdy aplikacja jest wywoływana w akcji kontrolera, który przechowuje `[Authorize]` atrybutu, ASP.NET/ASP.NET Core analizuje tokenu elementu nośnego w nagłówku autoryzacji żądania wywołującego i wyodrębnia tokenu dostępu. Token jest następnie przekazywany do oprogramowania pośredniczącego JwtBearer, która wywołuje rozszerzenia modelu IdentityModel firmy Microsoft dla platformy .NET.

W programie ASP.NET Core to oprogramowanie pośredniczące jest inicjowana w pliku Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Oprogramowanie pośredniczące zostanie dodany do internetowego interfejsu API przez tę instrukcję:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Obecnie szablony ASP.NET Core tworzyć usługi Azure Active Directory (Azure AD), internetowych interfejsów API, który Zaloguj się użytkowników w organizacji lub każdej organizacji, nie za pomocą konta osobistego. Jednak można łatwo zmienić ich do używania punktu końcowego platformy tożsamości firmy Microsoft, dodając kod do pliku Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Weryfikacja tokenu

Oprogramowanie pośredniczące JwtBearer, takich jak oprogramowania pośredniczącego OpenID Connect, w aplikacji sieci web, jest wskazany przez `TokenValidationParameters` można zweryfikować tokenu. Token jest odszyfrowywany (zgodnie z potrzebami), oświadczenia są wyodrębniane i podpis jest weryfikowany. Oprogramowanie pośredniczące następnie weryfikuje token przez sprawdzenie, czy te dane:

- Jest ona przeznaczona dla internetowego interfejsu API (odbiorcy).
- Został wystawiony dla aplikacji, które mogą być wywoływanie internetowego interfejsu API (pod).
- Został wystawiony przez usługę tokenu zabezpieczającego zaufanych (STS) (Wystawca).
- Jego okres istnienia znajduje się w zakresie (wygaśnięcia).
- Nie został on zmodyfikowany (podpis).

Można także specjalnych operacji sprawdzania poprawności. Na przykład istnieje możliwość zweryfikowania, że klucze podpisywania, (gdy są osadzone w tokenie) są zaufane, i czy token nie jest odtwarzany. Ponadto niektóre protokoły wymagają określonych operacji sprawdzania poprawności.

### <a name="validators"></a>Moduły weryfikacji

Kroki weryfikacji są przechwytywane modułów sprawdzania poprawności, które są w [rozszerzenia modelu IdentityModel Microsoft dla platformy .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteki typu open source, w jednym pliku źródłowym: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Moduły weryfikacji zostały opisane w poniższej tabeli:

| Moduł sprawdzania poprawności | Opis |
|---------|---------|
| `ValidateAudience` | Gwarantuje, że token jest dla aplikacji, która sprawdza poprawność tokenu (dla mnie). |
| `ValidateIssuer` | Gwarantuje, że token został wystawiony przez zaufany usługi STS (od osoby, której można zaufać). |
| `ValidateIssuerSigningKey` | Zapewnia aplikacji, sprawdzanie poprawności tokenu relacji zaufania, klucz, który został użyty do podpisywania tokenu. (Przypadek specjalny, gdy klucz jest osadzony w tokenie. Nie są zwykle wymagane.) |
| `ValidateLifetime` | Gwarantuje, że token jest prawidłowy nadal (lub już). Moduł weryfikacji sprawdza, czy okres istnienia tokenu (`notbefore` i `expires` oświadczenia) znajduje się w zakresie. |
| `ValidateSignature` | Gwarantuje, że token nie została zmieniona. |
| `ValidateTokenReplay` | Gwarantuje, że token nie jest odtwarzany. (Specjalny przypadek niektóre protokoły jednorazowej Użyj). |

Moduły weryfikacji wszystkie wystąpienia są skojarzone z właściwościami `TokenValidationParameters` klasy samodzielnie inicjowany z konfiguracji ASP.NET/ASP.NET Core. W większości przypadków nie trzeba zmieniać parametrów. Istnieje jeden wyjątek dla aplikacji, które nie są jednego dzierżawcy. (Czyli aplikacji sieci web, które akceptują użytkowników z dowolnej organizacji lub osobistych kont Microsoft.) W takim przypadku można zweryfikować wystawcy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Sprawdź zakresach i rolach aplikacji w kodzie](scenario-protected-web-api-verification-scope-app-roles.md)
