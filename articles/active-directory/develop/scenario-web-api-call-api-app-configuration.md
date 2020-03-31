---
title: Konfigurowanie internetowego interfejsu API, który wywołuje internetowe interfejsy API | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje internetowe interfejsy API (konfiguracja kodu aplikacji)
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
ms.openlocfilehash: 82b5e1d9753fbb65fd81f24b06016d302457144e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834097"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Internetowy interfejs API, który wywołuje internetowe interfejsy API: konfiguracja kodu

Po zarejestrowaniu interfejsu API sieci web można skonfigurować kod aplikacji.

Kod używany do konfigurowania interfejsu API sieci web, tak aby wywołuje podrzędne interfejsy API sieci web opiera się na kod, który jest używany do ochrony interfejsu API sieci web. Aby uzyskać więcej informacji, zobacz [Chroniony interfejs API sieci Web: konfiguracja aplikacji](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Kod subskrybowany onTokenValidated

Oprócz konfiguracji kodu dla wszystkich chronionych interfejsów API sieci web, należy subskrybować sprawdzanie poprawności tokenu nośnika, który otrzymujesz, gdy interfejs API jest wywoływany:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
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
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Przepływ w imieniu

Metoda AddAccountToCacheFromJwt() musi:

- Tworzenie wystąpienia poufnej aplikacji klienckiej biblioteki uwierzytelniania firmy Microsoft (MSAL).
- Wywołanie `AcquireTokenOnBehalf` metody. To wywołanie wymienia token okaziciela, który został nabyty przez klienta dla internetowego interfejsu API względem tokenu nośnika dla tego samego użytkownika, ale ma wywołać interfejsu API podrzędnego interfejsu API.

### <a name="instantiate-a-confidential-client-application"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej

Ten przepływ jest dostępny tylko w przepływie klienta poufnego, dzięki czemu chroniony interfejs API sieci web udostępnia poświadczenia klienta (klucz `WithCertificate` tajny klienta lub certyfikat) do [ConfidentialClientApplicationBuilder klasy](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) za pośrednictwem `WithClientSecret` metody lub.

![Lista metod iConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
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

Na koniec zamiast potwierdzać swoją tożsamość za pośrednictwem klucza tajnego klienta lub certyfikatu, poufne aplikacje klienckie mogą udowodnić swoją tożsamość przy użyciu potwierdzeń klienta.
Aby uzyskać więcej informacji na temat tego zaawansowanego scenariusza, zobacz [Poufne potwierdzenia klienta](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Jak dzwonić w imieniu

Należy wykonać wywołanie w imieniu (OBO) wywołując [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) `IConfidentialClientApplication` metody w interfejsie.

Klasa `UserAssertion` jest zbudowana z tokenu okaziciela, który jest odbierany przez internetowy interfejs API od własnych klientów. Istnieją [dwa konstruktory:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* Taki, który pobiera token na okaziciela JSON Web Token (JWT)
* Taki, który przyjmuje wszelkiego rodzaju potwierdzenia użytkownika, inny rodzaj tokenu zabezpieczającego, którego typ jest następnie określony w dodatkowym parametrze o nazwie`assertionType`

![Właściwości i metody obsługi użytkownika](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

W praktyce przepływ OBO jest często używany do uzyskiwania tokenu dla interfejsu API podrzędnego i przechowywania go w pamięci podręcznej tokenu użytkownika MSAL.NET. Można to zrobić, aby inne części internetowego [overrides](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) interfejsu API ``AcquireTokenOnSilent`` można później wywołać na zastąpienia wywołać interfejsów API podrzędnego. To wywołanie ma wpływ odświeżania tokenów, jeśli to konieczne.

```csharp
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

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
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
# <a name="java"></a>[Java](#tab/java)

Przepływ W imieniu (OBO) służy do uzyskania tokenu do wywołania podrzędnego interfejsu API sieci web. W tym przepływie internetowy interfejs API odbiera token na okaziciela z uprawnieniami delegowanymi użytkownika z aplikacji klienckiej, a następnie wymienia ten token na inny token dostępu, aby wywołać podrzędny interfejs API sieci web.

Poniższy kod używa platformy Spring `SecurityContextHolder` Security w interfejsie API sieci web, aby uzyskać zweryfikowany token nośnika. Następnie używa biblioteki MSAL Java, aby uzyskać token `acquireToken` dla `OnBehalfOfParameters`interfejsu API podrzędnego przy użyciu wywołania z . MSAL buforuje token, dzięki czemu kolejne `acquireTokenSilently` wywołania interfejsu API można użyć, aby uzyskać token w pamięci podręcznej.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Przepływ W imieniu (OBO) służy do uzyskania tokenu do wywołania podrzędnego interfejsu API sieci web. W tym przepływie internetowy interfejs API odbiera token na okaziciela z uprawnieniami delegowanymi użytkownika z aplikacji klienckiej, a następnie wymienia ten token na inny token dostępu, aby wywołać podrzędny interfejs API sieci web.

Interfejs API sieci Web języka Python będzie musiał użyć oprogramowania pośredniczącego, aby sprawdzić poprawność tokenu nośnika odebranego od klienta. Internetowy interfejs API można następnie uzyskać token dostępu dla interfejsu API [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) podrzędnego przy użyciu biblioteki MSAL Python, wywołując metodę. Przykład demonstruujący ten przepływ za pomocą biuletynu MSAL Python nie jest jeszcze dostępny.

---

Można również zobaczyć przykład implementacji przepływu OBO w [Node.js i usługi Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokół)

Aby uzyskać więcej informacji na temat protokołu OBO, zobacz [Platformę tożsamości firmy Microsoft i OAuth 2.0 W imieniu przepływu](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje internetowe interfejsy API: uzyskiwanie tokenu dla aplikacji](scenario-web-api-call-api-acquire-token.md)
