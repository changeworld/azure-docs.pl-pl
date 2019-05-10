---
title: Internetowy interfejs API tego wywołania podrzędne interfejsów API sieci web (konfiguracji kodu aplikacji) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć internetowy interfejs API wywołuje interfejsy API (konfiguracji kodu aplikacji) sieci web
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dedef2d22df9c8c81410296bdb0c4814bd98b80
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507131"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Czy interfejsy API — Konfiguracja kodu sieci web wywołań interfejsu API sieci Web

Po zarejestrowaniu interfejsu API sieci web można skonfigurować w kodzie aplikacji.

Kod, aby skonfigurować interfejs API sieci web wywołuje metodę transmisji interfejsów API sieci web opartą na kod używany do projektu interfejsu API sieci web. Aby uzyskać więcej informacji, zobacz [chroniony internetowy interfejs API — Konfiguracja aplikacji](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Subskrybuje OnTokenValidated kodu

Na podstawie konfiguracji kodu żadnych chronionych internetowych interfejsów API musisz mieć subskrypcję do weryfikacji tokenu elementu nośnego, odebrania po wywołaniu interfejsu API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
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

Metoda AddAccountToCacheFromJwt() musi:

- Tworzy aplikację MSAL poufne klienta.
- Wywołaj `AcquireTokenOnBehalf` do wymiany tokenu elementu nośnego, który został uzyskany przez klienta dla sieci web interfejsu API względem token elementu nośnego dla tego samego użytkownika, ale dla naszego interfejsu API do wywoływania podrzędnego interfejsu API.

### <a name="instantiate-a-confidential-client-application"></a>Utwórz wystąpienie aplikacji poufne klienta

Ten przepływ jest dostępna tylko w przepływ poufne klienta tak chroniony internetowy interfejs API zapewnia poświadczenia klienta (klucz tajny klienta lub certyfikat) [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.confidentialclientapplicationbuilder?view=azure-dotnet-preview) za pośrednictwem `WithClientSecret` lub `WithCertificate`metod, odpowiednio.

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

### <a name="how-to-call-on-behalf-of"></a>Jak wywołać w imieniu z

Wywołanie w imieniu z (OBO) odbywa się przez wywołanie metody [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.apiconfig.acquiretokenonbehalfofparameterbuilder?view=azure-dotnet-preview) metody `IConfidentialClientApplication` interfejsu.

`ClientAssertion` Jest tworzona na podstawie tokenu elementu nośnego odebranych przez internetowy interfejs API z własną klientów. Istnieją [dwa konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), ta, która przyjmuje tokenu JWT bearer i ta, która przyjmuje dowolnego rodzaju potwierdzenie użytkownika (innego rodzaju token zabezpieczający, jakiego typu jest następnie określone w dodatkowy parametr o nazwie `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

W praktyce przepływu OBO jest często używane do uzyskać token dla podrzędnego interfejsu API i zapisz go w pamięci podręcznej tokenu użytkownika platformy MSAL.NET, dzięki czemu inne części interfejsu API sieci web później można wywołać na [zastępuje](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) z ``AcquireTokenOnSilent`` do wywoływania podrzędnego interfejsów API. Ma to wpływu na tokeny odświeżania, w razie potrzeby.

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

## <a name="protocol"></a>Protokół

Aby uzyskać więcej informacji na temat protokołu w imieniu z, zobacz [platforma tożsamości firmy Microsoft i przepływ OAuth 2.0 "w imieniu"](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji](scenario-web-api-call-api-acquire-token.md)
