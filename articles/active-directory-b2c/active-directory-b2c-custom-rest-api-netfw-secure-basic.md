---
title: Zabezpieczanie usług RESTful przy użyciu uwierzytelniania podstawowego HTTP w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Zabezpieczenia sieci niestandardowe wymiany oświadczenia interfejsu API REST, w sieci Azure AD B2C przy użyciu uwierzytelniania podstawowego HTTP.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 18aeeef1aa2ef322f78e7e10118464d542926e59
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709798"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Zabezpieczanie usług RESTful przy użyciu uwierzytelniania podstawowego HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W [powiązanego artykułu usługi Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md), tworzenia usługi RESTful (interfejs API sieci web), która integruje się z usługi Azure Active Directory B2C podróże użytkownika (Azure AD B2C) bez uwierzytelniania. 

W tym artykule możesz dodać podstawowe uwierzytelnianie HTTP z usługą RESTful, aby tylko weryfikacji użytkowników, w tym B2C, mogą uzyskiwać dostęp do interfejsu API. Podstawowe uwierzytelnianie HTTP należy ustawić poświadczenia użytkownika (identyfikator aplikacji i klucz tajny aplikacji) w zasadach niestandardowych. 

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie podstawowe w składniku ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [integracji interfejsu API REST oświadczeń wymiany w podróży użytkownika usługi Azure AD B2C](active-directory-b2c-custom-rest-api-netfw.md) artykułu.

## <a name="step-1-add-authentication-support"></a>Krok 1: Dodaj obsługę uwierzytelniania

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Krok 1.1: Dodaj ustawienia aplikacji do pliku web.config projektu
1. Otwórz projekt programu Visual Studio, który został utworzony wcześniej. 

2. Dodaj poniższe ustawienia aplikacji do pliku web.config w obszarze `appSettings` elementu:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Utwórz hasło, a następnie ustaw `WebApp:ClientSecret` wartość.

    Aby wygenerować skomplikowanego hasła, uruchom następujący kod programu PowerShell. Można użyć dowolną wartość.

    ```PowerShell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Krok 1.2: Instalacja bibliotek OWIN
Aby rozpocząć, Dodaj pakiety NuGet oprogramowanie pośredniczące OWIN do projektu przy użyciu konsoli Menedżera pakietów programu Visual Studio:

```
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Krok 1.3. Klasa oprogramowanie pośredniczące uwierzytelniania dodać
Dodaj `ClientAuthMiddleware.cs` klasy w obszarze *App_Start* folderu. W tym celu:

1. Kliknij prawym przyciskiem myszy *App_Start* folderu, wybierz opcję **Dodaj**, a następnie wybierz **klasy**.

   ![Dodaj klasę ClientAuthMiddleware.cs w folderze App_Start](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. W **nazwa** wpisz **ClientAuthMiddleware.cs**.

   ![Tworzenie nowej klasy C#](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Otwórz *App_Start\ClientAuthMiddleware.cs* plików i Zastąp plik zawartości z następującym kodem:

    ```csharp
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>Krok 1.4: Dodawanie klasy początkowej OWIN
Dodaj klasę początkową OWIN o nazwie `Startup.cs` do interfejsu API. W tym celu:
1. Kliknij prawym przyciskiem myszy projekt, wybierz **Dodaj** > **nowy element**, a następnie wyszukaj **OWIN**.

   ![Dodawanie klasy początkowej OWIN](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Otwórz *Startup.cs* plików i Zastąp plik zawartości z następującym kodem:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                    app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>Krok 1.5: Klasa interfejsu API tożsamości chronić
Otwórz Controllers\IdentityController.cs i Dodaj `[Authorize]` tag do klasy kontrolera. Ten tag ogranicza dostęp do kontrolera do użytkowników, którzy spełniają wymagania autoryzacji.

![Dodaj tag autoryzacji do kontrolera](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Krok 2: Publikowanie na platformie Azure
Aby opublikować projekt, w Eksploratorze rozwiązań, kliknij prawym przyciskiem myszy **Contoso.AADB2C.API** projektu, a następnie wybierz **publikowania**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Krok 3: Dodawanie usługi RESTful identyfikator i aplikacji klucz tajny aplikacji do usługi Azure AD B2C
Po zabezpieczeniu według Identyfikatora klienta (nazwa użytkownika) i klucz tajny usługi RESTful, poświadczenia muszą być przechowywane w dzierżawie usługi Azure AD B2C. Zasad niestandardowych zawiera poświadczenia, gdy wywołuje usług RESTful. 

### <a name="step-31-add-a-restful-services-client-id"></a>Krok 3.1: Identyfikator klienta usługi RESTful dodać
1. W dzierżawie usługi Azure AD B2C, wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**.


2. Wybierz **zasad kluczy** Aby wyświetlić klucze, które są dostępne w dzierżawie.

3. Wybierz pozycję **Dodaj**.

4. Aby uzyskać **opcje**, wybierz pozycję **ręcznego**.

5. Aby uzyskać **nazwa**, typ **B2cRestClientId**.  
    Prefiks *B2C_1A_* mogą być dodawane automatycznie.

6. W **klucz tajny** wprowadź identyfikator aplikacji, wcześniej zdefiniowany.

7. Aby uzyskać **użycie klucza**, wybierz pozycję **klucz tajny**.

8. Wybierz pozycję **Utwórz**.

9. Upewnij się, że utworzono `B2C_1A_B2cRestClientId` klucza.

### <a name="step-32-add-a-restful-services-client-secret"></a>Krok 3.2: Klucz tajny klienta usługi RESTful dodać
1. W dzierżawie usługi Azure AD B2C, wybierz **ustawieniami B2C** > **Framework obsługi tożsamości**.

2. Wybierz **zasad kluczy** Aby wyświetlić dostępne w Twojej dzierżawie kluczy.

3. Wybierz pozycję **Dodaj**.

4. Aby uzyskać **opcje**, wybierz pozycję **ręcznego**.

5. Aby uzyskać **nazwa**, typ **B2cRestClientSecret**.  
    Prefiks *B2C_1A_* mogą być dodawane automatycznie.

6. W **klucz tajny** wprowadź wcześniej zdefiniowany klucz tajny aplikacji.

7. Aby uzyskać **użycie klucza**, wybierz pozycję **klucz tajny**.

8. Wybierz pozycję **Utwórz**.

9. Upewnij się, że utworzono `B2C_1A_B2cRestClientSecret` klucza.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Krok 4: Zmiana techniczne profilu do obsługi uwierzytelniania podstawowego w zasadach rozszerzenia
1. W katalogu roboczym otwórz plik zasad rozszerzenia (TrustFrameworkExtensions.xml).

2. Wyszukaj `<TechnicalProfile>` węzła, który zawiera `Id="REST-API-SignUp"`.

3. Zlokalizuj `<Metadata>` elementu.

4. Zmień *AuthenticationType* do *podstawowe*w następujący sposób:
    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Natychmiast po upływie `<Metadata>` elementu, Dodaj następujący fragment kodu XML: 

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```
    Po dodaniu fragmentu profilu techniczne powinna wyglądać następujący kod XML:
    
    ![Dodawanie elementów XML uwierzytelnianie podstawowe](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Przekaż zasady dla Twojej dzierżawy

1. W [portalu Azure](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C**.

2. Wybierz **Framework obsługi tożsamości**.

3. Otwórz **wszystkich zasad**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.

6. Przekaż *TrustFrameworkExtensions.xml* pliku, a następnie upewnij się, że przekazaniem sprawdzania poprawności.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Test zasady niestandardowe przy użyciu Uruchom teraz
1. Otwórz **ustawienia usługi Azure AD B2C**, a następnie wybierz **Framework obsługi tożsamości**.

    >[!NOTE]
    >Uruchom teraz wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strony (RP) zasad niestandardowych, które można przekazać, a następnie wybierz **Uruchom teraz**.

3. Testowanie procesu, wpisując **testu** w **imię** pole.  
    Usługa Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.

    ![Testowanie tożsamości interfejsu API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. W **imię** wpisz nazwę (innego niż "Test").  
    Usługa Azure AD B2C zarejestrowaniu użytkownika, a następnie wysyła numer lojalność do aplikacji. Zanotuj liczbę podaną w tym przykładzie:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
      "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
      "acr": "b2c_1a_signup_signin",
      "nonce": "defaultNonce",
      "iat": 1507122303,
      "auth_time": 1507122303,
      "loyaltyNumber": "290",
      "given_name": "Emily",
      "emails": ["B2cdemo@outlook.com"]
    }
    ```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcjonalnie) Pobierz pliki pełną zasad i kod
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, zaleca się tworzenia scenariusz przy użyciu plików zasady niestandardowe. Użytkownikowi, firma Microsoft umieściła [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Możesz pobrać pełną kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).

## <a name="next-steps"></a>Kolejne kroki
* [Aby zabezpieczyć interfejs API RESTful wykorzystuje się certyfikaty klienta](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)

