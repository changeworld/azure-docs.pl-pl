---
title: Zabezpieczanie usługi RESTful przy użyciu uwierzytelniania podstawowego protokołu HTTP
titleSuffix: Azure AD B2C
description: Zabezpiecz niestandardowe wymianę oświadczeń interfejsu API REST w Azure AD B2C przy użyciu uwierzytelniania podstawowego protokołu HTTP.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 505d92e33606dac327778cae179df44efbfdf853
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183860"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>Zabezpieczanie usług RESTful za pomocą uwierzytelniania podstawowego protokołu HTTP

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W [powiązanym Azure AD B2C artykule](rest-api-claims-exchange-dotnet.md)utworzysz usługę RESTful (Web API), która integruje się z przejazdami użytkownika Azure Active Directory B2C (Azure AD B2C) bez uwierzytelniania.

W tym artykule opisano Dodawanie uwierzytelniania podstawowego protokołu HTTP do usługi RESTful, tak aby tylko zweryfikowani użytkownicy, w tym B2C, mieli dostęp do interfejsu API. W przypadku uwierzytelniania podstawowego HTTP należy ustawić poświadczenia użytkownika (Identyfikator aplikacji i klucz tajny aplikacji) w zasadach niestandardowych.

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie podstawowe w interfejsie Web API ASP.NET](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w artykule [integracja z interfejsem API REST w przypadku wymiany oświadczeń w Azure AD B2C podróży użytkownika](rest-api-claims-exchange-dotnet.md) .

## <a name="step-1-add-authentication-support"></a>Krok 1. Dodawanie obsługi uwierzytelniania

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>Krok 1,1: Dodawanie ustawień aplikacji do pliku Web. config projektu

1. Otwórz projekt programu Visual Studio, który został utworzony wcześniej.

2. Dodaj następujące ustawienia aplikacji do pliku Web. config w elemencie `appSettings`:

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. Utwórz hasło, a następnie ustaw wartość `WebApp:ClientSecret`.

    Aby wygenerować złożone hasło, uruchom następujący kod programu PowerShell. Możesz użyć dowolnej dowolnej wartości.

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>Krok 1,2: Instalowanie bibliotek OWIN

Aby rozpocząć, Dodaj pakiety NuGet oprogramowania OWIN do projektu za pomocą konsoli Menedżera pakietów programu Visual Studio:

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>Krok 1,3: Dodawanie klasy pośredniczącej uwierzytelniania

Dodaj klasę `ClientAuthMiddleware.cs` w folderze *App_Start* . W tym celu:

1. Kliknij prawym przyciskiem myszy folder *App_Start* , wybierz polecenie **Dodaj**, a następnie wybierz pozycję **Klasa**.

   ![Dodaj klasę ClientAuthMiddleware.cs do folderu App_Start](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. W polu **Nazwa** wpisz **ClientAuthMiddleware.cs**.

   ![Tworzenie nowej C# klasy w oknie dialogowym Dodaj nowy element w programie Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. Otwórz plik *App_Start \clientauthmiddleware.cs* i Zastąp zawartość pliku następującym kodem:

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

### <a name="step-14-add-an-owin-startup-class"></a>Krok 1,4: Dodawanie klasy uruchamiania OWIN

Dodaj klasę uruchomieniową OWIN o nazwie `Startup.cs` do interfejsu API. W tym celu:
1. Kliknij prawym przyciskiem myszy projekt, wybierz pozycję **dodaj** > **nowy element**, a następnie wyszukaj ciąg **Owin**.

   ![Tworzenie klasy uruchomieniowej OWIN w oknie dialogowym Dodawanie nowego elementu w programie Visual Studio](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-OWIN-startup.png)

2. Otwórz plik *Startup.cs* i Zastąp zawartość pliku następującym kodem:

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

### <a name="step-15-protect-the-identity-api-class"></a>Krok 1,5: ochrona klasy interfejsu API tożsamości

Otwórz Controllers\IdentityController.cs i Dodaj tag `[Authorize]` do klasy Controller. Ten tag ogranicza dostęp do kontrolera do użytkowników, którzy spełniają wymagania dotyczące autoryzacji.

![Dodaj tag Autoryzuj do kontrolera](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>Krok 2. publikowanie na platformie Azure

Aby opublikować projekt, w Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **contoso. AADB2C. API** , a następnie wybierz polecenie **Publikuj**.

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>Krok 3. Dodawanie identyfikatora aplikacji usług RESTful Services i klucza tajnego aplikacji do Azure AD B2C

Gdy usługa RESTful jest chroniona za pomocą identyfikatora klienta (username) i wpisu tajnego, należy przechowywać poświadczenia w dzierżawie Azure AD B2C. Zasady niestandardowe udostępniają poświadczenia, gdy wywoła usługi RESTful.

### <a name="step-31-add-a-restful-services-client-id"></a>Krok 3,1: Dodawanie identyfikatora klienta usług RESTful Services

1. W dzierżawie Azure AD B2C wybierz pozycję **Ustawienia B2C** > **Framework Experience platform**.


2. Wybierz pozycję **klucze zasad** , aby wyświetlić klucze, które są dostępne w dzierżawie.

3. Wybierz pozycję **Dodaj**.

4. W obszarze **Opcje**wybierz pozycję **Ręczne**.

5. W obszarze **Nazwa**wpisz **B2cRestClientId**.
    Prefiks *B2C_1A_* może zostać dodany automatycznie.

6. W polu **wpis tajny** wprowadź zdefiniowany wcześniej identyfikator aplikacji.

7. W obszarze **użycie klucza**wybierz pozycję **podpis**.

8. Wybierz pozycję **Utwórz**.

9. Upewnij się, że utworzono klucz `B2C_1A_B2cRestClientId`.

### <a name="step-32-add-a-restful-services-client-secret"></a>Krok 3,2: Dodawanie klucza tajnego klienta usług RESTful Services

1. W dzierżawie Azure AD B2C wybierz pozycję **Ustawienia B2C** > **Framework Experience platform**.

2. Wybierz pozycję **klucze zasad** , aby wyświetlić klucze dostępne w dzierżawie.

3. Wybierz pozycję **Dodaj**.

4. W obszarze **Opcje**wybierz pozycję **Ręczne**.

5. W obszarze **Nazwa**wpisz **B2cRestClientSecret**.
    Prefiks *B2C_1A_* może zostać dodany automatycznie.

6. W polu **wpis tajny** wprowadź wcześniej zdefiniowany klucz tajny aplikacji.

7. W obszarze **użycie klucza**wybierz pozycję **podpis**.

8. Wybierz pozycję **Utwórz**.

9. Upewnij się, że utworzono klucz `B2C_1A_B2cRestClientSecret`.

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>Krok 4. zmiana profilu technicznego w celu obsługi uwierzytelniania podstawowego w zasadach rozszerzenia

1. W katalogu roboczym Otwórz plik zasad rozszerzenia (TrustFrameworkExtensions. xml).

2. Wyszukaj węzeł `<TechnicalProfile>`, który zawiera `Id="REST-API-SignUp"`.

3. Znajdź element `<Metadata>`.

4. Zmień wartość *AuthenticationType* na *podstawowa*w następujący sposób:

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. Bezpośrednio po elemencie `<Metadata>` zamykającym Dodaj następujący fragment kodu XML:

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    Po dodaniu fragmentu Twój profil techniczny powinien wyglądać podobnie do następującego kodu XML:

    ![Dodaj podstawowe elementy XML uwierzytelniania do profilu technicznym](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5. przekazywanie zasad do dzierżawy

1. Na [Azure Portal](https://portal.azure.com)wybierz ikonę katalogów i **subskrypcji** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

1. Wybierz pozycję **platforma obsługi tożsamości**.

1. Otwórz **wszystkie zasady**.

1. Wybierz pozycję **Przekaż zasady**.

1. Zaznacz pole wyboru **Zastąp zasady, jeśli istnieje** .

1. Przekaż plik *TrustFrameworkExtensions. XML* , a następnie upewnij się, że przeszedł sprawdzanie poprawności.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6. Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz

1. Otwórz **Azure AD B2C ustawienia**, a następnie wybierz pozycję **platforma obsługi tożsamości**.

    >[!NOTE]
    >Uruchomienie teraz wymaga, aby co najmniej jedna aplikacja była przedrejestrowana w dzierżawie. Aby dowiedzieć się, jak zarejestrować aplikacje, Azure AD B2C Zobacz artykuł [Rozpoczynanie pracy](tutorial-create-tenant.md) i artykuł dotyczący [rejestracji aplikacji](tutorial-register-applications.md) .

2. Otwórz **B2C_1A_signup_signin**, załadowane zasady niestandardowe jednostki uzależnionej (RP), a następnie wybierz pozycję **Uruchom teraz**.

3. Przetestuj proces, wpisując **test** w **podanym polu Nazwa** .
    Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.

    ![Testowanie walidacji danych wejściowych nazwy w interfejsie API tożsamości](./media/secure-rest-api-dotnet-basic-auth/rest-api-netfw-test.png)

4. W **podanym polu Nazwa** wpisz nazwę (inną niż "test").
    Azure AD B2C zarejestrowaniu użytkownika, a następnie wysłania do aplikacji numeru lojalnościowego. Zwróć uwagę na liczbę w tym przykładzie:

    ```
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
    }.{
      "exp": 1507125903,
      "nbf": 1507122303,
      "ver": "1.0",
      "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-download-the-complete-policy-files-and-code"></a>Obowiązkowe Pobieranie kompletnych plików zasad i kodu

* Po ukończeniu przewodnika [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md) zalecamy utworzenie scenariusza przy użyciu własnych niestandardowych plików zasad. W odniesieniu do Twojej dokumentacji udostępniono [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).
* Możesz pobrać kompletny kod z [przykładowego rozwiązania programu Visual Studio, aby uzyskać odwołanie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic).

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu certyfikatów klienta](secure-rest-api-dotnet-certificate-auth.md)
