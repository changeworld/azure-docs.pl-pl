---
title: Konfigurowanie chronionych aplikacji interfejsu API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API i skonfigurować kod aplikacji.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882475"
---
# <a name="protected-web-api-code-configuration"></a>Chroniony interfejs API sieci Web: konfiguracja kodu

Aby skonfigurować kod chronionego interfejsu API sieci Web, musisz zrozumieć:

- Co definiuje interfejsy API jako chronione.
- Jak skonfigurować token okaziciela.
- Jak sprawdzić poprawność tokenu.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Co definiuje ASP.NET i ASP.NET podstawowych interfejsów API jako chronionych?

Podobnie jak aplikacje sieci Web, ASP.NET i ASP.NET core interfejsów API sieci web są chronione, ponieważ ich akcje kontrolera są poprzedzone **atrybutem [Authorize].** Akcje kontrolera można wywołać tylko wtedy, gdy interfejs API jest wywoływany z autoryzowaną tożsamością.

Zastanów się nad następującymi pytaniami:

- Tylko aplikacja może wywołać internetowy interfejs API. W jaki sposób interfejs API zna tożsamość aplikacji, która go wywołuje?
- Jeśli aplikacja wywołuje interfejs API w imieniu użytkownika, jaka jest tożsamość użytkownika?

## <a name="bearer-token"></a>Token na okaziciela

Token nośnika, który jest ustawiony w nagłówku, gdy aplikacja jest wywoływana przechowuje informacje o tożsamości aplikacji. Przechowuje również informacje o użytkowniku, chyba że aplikacja sieci web akceptuje wywołania usługi do usługi z aplikacji demona.

Oto przykład kodu języka C#, który pokazuje klienta wywołującego interfejs API po nabyciu tokenu z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Aplikacja kliencka żąda tokenu nośnika do punktu końcowego platformy tożsamości firmy Microsoft *dla internetowego interfejsu API.* Internetowy interfejs API jest jedyną aplikacją, która powinna zweryfikować token i wyświetlić oświadczenia, które zawiera. Aplikacje klienckie nigdy nie należy próbować sprawdzić oświadczenia w tokenach.
>
> W przyszłości internetowy interfejs API może wymagać szyfrowania tokenu. To wymaganie uniemożliwiłoby dostęp do aplikacji klienckich, które mogą wyświetlać tokeny dostępu.

## <a name="jwtbearer-configuration"></a>Konfiguracja JwtBearer

W tej sekcji opisano sposób konfigurowania tokenu nośnika.

### <a name="config-file"></a>Plik konfiguracyjny

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

Gdy aplikacja jest wywoływana na akcję kontrolera, który posiada **atrybut [Authorize],** ASP.NET i ASP.NET Core wyodrębnić token dostępu z tokenu nośnika nagłówka autoryzacji. Token dostępu jest następnie przekazywał dalej do oprogramowania pośredniczącego JwtBearer, które wywołuje rozszerzenia Microsoft IdentityModel extensions dla platformy .NET.

W ASP.NET Core to oprogramowanie pośredniczące jest inicjowane w pliku Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Oprogramowanie pośredniczące jest dodawane do internetowego interfejsu API za pomocą następującej instrukcji:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Obecnie szablony ASP.NET Core tworzą internetowe interfejsy API usługi Azure Active Directory (Azure AD), które logują się do użytkowników w organizacji lub dowolnej organizacji. Nie logują się do użytkowników za pomocą kont osobistych. Ale można zmienić szablony, aby użyć punktu końcowego platformy tożsamości firmy Microsoft, dodając ten kod do Startup.cs:

```csharp
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

Poprzedni fragment kodu jest wyodrębniany z przyrostowego samouczka przyrostowego interfejsu API sieci web ASP.NET Core w witrynie [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). **AddProtecTedWebApi** metoda, która ma więcej niż pokazuje fragment kodu, jest wywoływana z Startup.cs.

## <a name="token-validation"></a>Sprawdzanie poprawności tokenu

W poprzednim urywek oprogramowanie pośredniczące JwtBearer, takie jak oprogramowanie pośredniczące OpenID Connect w `TokenValidationParameters`aplikacjach sieci Web, sprawdza poprawność tokenu na podstawie wartości pliku . Token jest odszyfrowywane w razie potrzeby, oświadczenia są wyodrębniane, a podpis jest weryfikowany. Oprogramowanie pośredniczące następnie sprawdza poprawność tokenu, sprawdzając te dane:

- Grupa odbiorców: token jest przeznaczony dla internetowego interfejsu API.
- Sub: Został wystawiony dla aplikacji, która może wywoływać internetowy interfejs API.
- Wystawca: Został wydany przez zaufaną usługę tokenu zabezpieczającego (STS).
- Wygaśnięcie: Jego żywotność jest w zasięgu.
- Podpis: Nie został zmodyfikowany.

Mogą również istnieć specjalne weryfikacje. Na przykład można sprawdzić poprawność, że klucze podpisywania, gdy osadzone w tokenie są zaufane i że token nie jest odtwarzany. Na koniec niektóre protokoły wymagają określonych weryfikacji.

### <a name="validators"></a>Walidatory

Kroki sprawdzania poprawności są przechwytywane w modułach sprawdzania poprawności, które są dostarczane przez [rozszerzenia Microsoft IdentityModel dla](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteki open source platformy .NET. Moduły sprawdzania poprawności są zdefiniowane w pliku źródłowym biblioteki [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

W tej tabeli opisano walidatory:

| Walidatora | Opis |
|---------|---------|
| **Sprawdzanie poprawnościuwaga** | Zapewnia token jest dla aplikacji, która sprawdza poprawność tokenu dla Ciebie. |
| **Sprawdźuwiękujeczysło** | Zapewnia, że token został wystawiony przez zaufanego sts, co oznacza, że jest od kogoś, komu ufasz. |
| **ValidateIssuerSigningKey** | Zapewnia, że aplikacja sprawdzania poprawności tokenu ufa klucz, który został użyty do podpisania tokenu. Istnieje specjalny przypadek, w którym klucz jest osadzony w tokenie. Ale ten przypadek zwykle nie występuje. |
| **Sprawdzanie poprawnościczasu życia** | Zapewnia, że token jest nadal lub już prawidłowy. Walidator sprawdza, czy okres istnienia tokenu znajduje się w zakresie określonym przez **notbefore** i **wygasa** oświadczeń. |
| **Sprawdzanie poprawności podpisu** | Zapewnia, że token nie został zmodyfikowany. |
| **Sprawdzanie poprawności Doplay** | Zapewnia, że token nie jest odtwarzany. Istnieje szczególny przypadek dla niektórych protokołów jednorazowego użycia. |

Moduły sprawdzania poprawności są skojarzone z właściwościami **klasy TokenValidationParameters.** Właściwości są inicjowane z ASP.NET i ASP.NET core konfiguracji.

W większości przypadków nie trzeba zmieniać parametrów. Aplikacje, które nie są pojedynczymi dzierżawami, są wyjątkami. Te aplikacje sieci Web akceptują użytkowników z dowolnej organizacji lub z osobistych kont Microsoft. Emitenci w takim przypadku muszą zostać zatwierdzeni.

## <a name="token-validation-in-azure-functions"></a>Sprawdzanie poprawności tokenu w usługach Azure Functions

Można również sprawdzić poprawność tokenów dostępu przychodzącego w usłudze Azure Functions. Przykłady takiej weryfikacji można znaleźć w programach [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)i [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Weryfikowanie zakresów i ról aplikacji w kodzie](scenario-protected-web-api-verification-scope-app-roles.md)
