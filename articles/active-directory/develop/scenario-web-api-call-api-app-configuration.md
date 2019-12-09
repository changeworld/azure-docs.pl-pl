---
title: Konfigurowanie interfejsu API sieci Web, który wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje interfejsy API sieci Web (Konfiguracja kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919753"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Interfejs API sieci Web, który wywołuje interfejsy API sieci Web — konfiguracja kodu

Po zarejestrowaniu internetowego interfejsu API można skonfigurować kod dla aplikacji.

Kod służący do konfigurowania internetowego interfejsu API w taki sposób, aby wywołuje podrzędne interfejsy API sieci Web w oparciu o kod używany do ochrony internetowego interfejsu API. Aby uzyskać więcej informacji, zobacz [chroniona sieć Web API — konfiguracja aplikacji](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Kod subskrybowany do OnTokenValidated

Na podstawie konfiguracji kodu dla wszelkich chronionych interfejsów API sieci Web należy zasubskrybować weryfikację tokenu okaziciela, który jest odbierany w przypadku wywołania interfejsu API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Przepływ „w imieniu”

Metoda AddAccountToCacheFromJwt () musi:

- Tworzenie wystąpienia MSAL poufnej aplikacji klienckiej.
- Wywołaj `AcquireTokenOnBehalf`, aby wymienić token okaziciela, który został uzyskany przez klienta dla internetowego interfejsu API, względem tokenu okaziciela dla tego samego użytkownika, ale w przypadku interfejsu API wywołującego podrzędny interfejs API.

### <a name="instantiate-a-confidential-client-application"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej

Ten przepływ jest dostępny tylko w tajemnicy klienta, dlatego chroniony internetowy interfejs API udostępnia poświadczenia klienta (klucz tajny klienta lub certyfikat) do [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) przy użyciu odpowiednio metod `WithClientSecret` lub `WithCertificate`.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Na koniec zamiast klucza tajnego klienta lub certyfikatu poufne aplikacje klienckie mogą również udowodnić swoją tożsamość za pomocą potwierdzeń klientów.
Ten zaawansowany scenariusz jest szczegółowo opisany w [potwierdzeniach klienta](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Jak wywołać w imieniu

Wywołanie w imieniu (OBO) jest wykonywane przez wywołanie metody [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) w interfejsie `IConfidentialClientApplication`.

`UserAssertion` jest tworzona na podstawie tokenu okaziciela otrzymanego przez internetowy interfejs API od własnych klientów. Istnieją [dwa konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), jeden, który pobiera token okaziciela JWT, i jeden, który ma dowolny rodzaj potwierdzenia użytkownika (inny rodzaj tokenu zabezpieczającego, który jest następnie określony w dodatkowym parametrze o nazwie `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

W rzeczywistości przepływ OBO jest często używany do uzyskiwania tokenu dla podrzędnego interfejsu API i przechowywania go w pamięci podręcznej tokenów użytkownika MSAL.NET, dzięki czemu inne części interfejsu API sieci Web mogą później wywoływać [przesłonięcia](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent``, aby wywołać podrzędne interfejsy API. To wywołanie ma wpływ na odświeżanie tokenów, w razie konieczności.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

Możesz również zapoznać się z przykładem dotyczącym implementacji przepływu w [NodeJS i Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokół)

Aby uzyskać więcej informacji o protokole w imieniu, zobacz [Microsoft Identity platform i OAuth 2,0 w imieniu usługi Flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji](scenario-web-api-call-api-acquire-token.md)
