---
title: Integracja wymiany oświadczenia interfejsu API REST w podróży użytkownika usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Integracja wymiany oświadczenia interfejsu API REST usługi Azure AD B2C podróży użytkownika jako weryfikacji danych wejściowych użytkownika.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/30/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e17647016da0e877bd8f21357a4bd38121820f22
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709363"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integracja wymiany oświadczenia interfejsu API REST usługi Azure AD B2C podróży użytkownika jako weryfikacji danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Framework obsługi tożsamości, która jest usługi Azure Active Directory B2C (Azure AD B2C), można zintegrować z interfejsu API RESTful w podróży użytkownika. W tym przewodniku dowiesz się, jak usługa Azure AD B2C współdziała z usługami .NET Framework RESTful (interfejs API sieci web).

## <a name="introduction"></a>Wprowadzenie
Za pomocą usługi Azure AD B2C, należy dodać logiki biznesowej przebieg użytkownika poprzez wywołanie usługi RESTful. Framework obsługi tożsamości wysyła dane do usługi RESTful w *oświadczeń wejściowych* kolekcji i odbiera dane z kopii RESTful w *Output oświadczeń* kolekcji. Dzięki integracji z usługą RESTful można:

* **Sprawdzanie poprawności danych wejściowych użytkownika**: Ta akcja uniemożliwia uszkodzone dane utrwalanie do usługi Azure AD. Jeśli wartość od użytkownika jest nieprawidłowa, usługą RESTful zwraca komunikat o błędzie, który informuje użytkownika o podanie wpis. Na przykład można sprawdzić, czy adres e-mail podany przez użytkownika istnieje w bazie danych klienta.
* **Zastąp oświadczenia wejściowe**: na przykład, jeśli użytkownik wprowadza nazwę pierwszego wszystkie małe i wielkie litery, można sformatować nazwę tylko pierwsza litera kapitalizacji.
* **Wzbogacić dalsze integrowanie z aplikacji biznesowych z firmowych danych użytkownika**: Your RESTful service może odbierać adres e-mail użytkownika, kwerendy bazy danych klienta i zwracać numer lojalność użytkownika do usługi Azure AD B2C. Zwracany oświadczenia mogą być przechowywane na koncie usługi Azure AD użytkownika, obliczone w następnej *Instrumentacji*, lub uwzględnione w tokenie dostępu.
* **Uruchom niestandardowe reguły biznesowe**: możesz wysyłać powiadomienia wypychane, zaktualizować baz danych firmowych, uruchamiany jest proces migracji użytkowników, zarządzanie uprawnieniami, inspekcji bazy danych i wykonywać inne akcje.

Integracja z usług RESTful można zaprojektować w następujący sposób:

* **Profil techniczne weryfikacji**: wywołanie usługi RESTful się stanie w profilu techniczne sprawdzania poprawności określonego profilu technicznych. Profil techniczne sprawdzania poprawności weryfikuje dane dostarczone przez użytkownika przed przebieg użytkownika przenosi do przodu. Profil techniczne weryfikacji można:
   * Wysyłać oświadczenia wejściowe.
   * Sprawdzanie poprawności oświadczenia wejściowe i zgłosić niestandardowych komunikatów o błędach.
   * Wysyłać oświadczenia wstecz danych wyjściowych.

* **Oświadczenia exchange**: ten projekt jest podobny do profilu techniczne sprawdzania poprawności, ale zdarza się to w ramach kroku aranżacji. Ta definicja jest ograniczona do:
   * Wysyłać oświadczenia wejściowe.
   * Wysyłać oświadczenia wstecz danych wyjściowych.

## <a name="restful-walkthrough"></a>RESTful wskazówki
W tym przewodniku wytwarzasz oprogramowanie .NET Framework składnika web API, sprawdza poprawność danych wejściowych użytkownika, który udostępnia wiele lojalność użytkownika. Na przykład aplikacja może udzielić dostępu do *platynowej korzyści* na podstawie liczby lojalność.

Omówienie:
* Tworzenie usługi RESTful (.NET Framework interfejsu API sieci web).
* Użyj usługi RESTful w podróży użytkownika.
* Wysyłać oświadczenia wejściowe i je odczytać w kodzie.
* Sprawdź poprawność imię użytkownika.
* Wyślij ponownie numer lojalność. 
* Dodaj numer lojalność do JSON Web Token (JWT).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="step-1-create-an-aspnet-web-api"></a>Krok 1: Tworzenie web API platformy ASP.NET

1. W programie Visual Studio Utwórz projekt, wybierając **pliku** > **nowy** > **projektu**.

2. W **nowy projekt** wybierz **Visual C#** > **Web** > **aplikacji sieci Web platformy ASP.NET (.NET Framework)**.

3. W **nazwa** wpisz nazwę dla aplikacji (na przykład *Contoso.AADB2C.API*), a następnie wybierz **OK**.

    ![Utwórz nowy projekt programu visual studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. W **nowej aplikacji sieci Web ASP.NET** wybierz **interfejsu API sieci Web** lub **aplikacji interfejsu API Azure** szablonu.

    ![Wybierz szablon interfejsu API sieci web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Upewnij się, że uwierzytelniania ma ustawioną wartość **bez uwierzytelniania**.

6. Wybierz **OK** Aby utworzyć projekt.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Krok 2: Przygotowanie punkt końcowy interfejsu API REST

### <a name="step-21-add-data-models"></a>Krok 2.1: Dodawanie modeli danych
Modele reprezentują oświadczenia wejściowe i danych w usłudze RESTful oświadczeń wyjściowych. Kod odczytuje dane wejściowe przy deserializacji modelu oświadczenia wejściowe z ciągu JSON do obiektu C# (model). Interfejs API sieci web ASP.NET automatycznie deserializuje model oświadczeń danych wyjściowych do formatu JSON, a następnie zapisuje dane serializowane do treści komunikatu odpowiedzi HTTP. 

Tworzenie modelu, który reprezentuje oświadczenia wejściowe, wykonując następujące czynności:

1. Jeśli w Eksploratorze rozwiązań nie jest jeszcze otwarty, wybierz **widoku** > **Eksploratora rozwiązań**. 
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**.

    ![Dodawanie modelu](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Nazwa klasy `InputClaimsModel`, a następnie dodaj następujące właściwości `InputClaimsModel` klasy:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. Utwórz nowy model `OutputClaimsModel`, a następnie dodaj następujące właściwości `OutputClaimsModel` klasy:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Utwórz jeden model więcej, `B2CResponseContent`, umożliwiający generują komunikaty o błędzie sprawdzania poprawności danych wejściowych. Dodaj następujące właściwości `B2CResponseContent` klasy, podaj brakujących odwołań, a następnie zapisz plik:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }    
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>Krok 2.2: Dodawanie kontrolera
W składniku web API _kontrolera_ jest obiekt, który obsługuje żądania HTTP. Kontroler zwraca dane wyjściowe oświadczeń lub, jeśli nie jest prawidłowy, imię zgłasza wyjątek, komunikat o błędzie HTTP konflikt.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Kontroler**.

    ![Dodawanie nowego kontrolera](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. W **Dodawanie szkieletu** wybierz **kontrolera interfejsu API sieci Web — pusty**, a następnie wybierz **Dodaj**.

    ![Pusty kontroler — wybierz Web API 2](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. W **Dodaj kontroler** okna, nazwy kontrolera **IdentityController**, a następnie wybierz **Dodaj**.

    ![Wpisz nazwę kontrolera](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Rusztowania tworzy plik o nazwie *IdentityController.cs* w *kontrolerów* folderu.

4. Jeśli *IdentityController.cs* plik nie jest już otwarty, kliknij go dwukrotnie i następnie Zastąp kod w pliku następującym kodem:

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>Krok 3: Publikowanie projektu na platformie Azure
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **Contoso.AADB2C.API** projektu, a następnie wybierz **publikowania**.

    ![Publikowanie do usługi Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. W **publikowania** wybierz **Microsoft Azure App Service**, a następnie wybierz **publikowania**.

    ![Utwórz nowy Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **Tworzenie usługi App Service** zostanie otwarte okno. W ramach tego utworzeniu wszystkich niezbędnych zasobów platformy Azure do uruchomienia aplikacji sieci web platformy ASP.NET na platformie Azure.

    > [!NOTE]
    >Aby uzyskać więcej informacji o sposobie publikowania, zobacz [tworzenie aplikacji sieci web platformy ASP.NET na platformie Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. W **Nazwa aplikacji sieci Web** wpisz unikatowej nazwy aplikacji (prawidłowe znaki to a-z, 0-9 i łączniki (-). Adres URL aplikacji sieci web jest http://<app_name>.azurewebsites.NET, gdzie *nazwa_aplikacji* to nazwa aplikacji sieci web. Możesz zaakceptować automatycznie wygenerowaną nazwę, która jest unikatowa.

    ![Podaj właściwości usługi aplikacji](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Aby rozpocząć tworzenie zasobów Azure, wybierz **Utwórz**.  
    Po utworzeniu aplikacji sieci web ASP.NET, Kreator publikuje ją do platformy Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

6. Skopiuj adres URL aplikacji sieci web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 4: Dodaj nowe `loyaltyNumber` oświadczenia do schematu pliku TrustFrameworkExtensions.xml
`loyaltyNumber` Oświadczeń nie jest jeszcze zdefiniowana w naszym schematu. Dodaj definicję w `<BuildingBlocks>` element, który znajduje się na początku *TrustFrameworkExtensions.xml* pliku.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>Krok 5: Dodawanie dostawcy oświadczeń 
Każdy dostawca oświadczeń musi mieć co najmniej jeden profil technicznych, które określają punktów końcowych i protokoły wymagane do komunikowania się z dostawcą oświadczeń. 

Dostawcy oświadczeń mogą mieć wiele profilów techniczne z różnych przyczyn. Na przykład wiele profilów techniczne można zdefiniować ponieważ dostawcy oświadczeń obsługuje wiele protokołów, punkty końcowe mogą mieć różne możliwości lub wersji może zawierać oświadczenia, które mają różne poziomy gwarancji. Może być możliwe do zaakceptowania poufnych oświadczeń w podróży jednego użytkownika, ale nie w innej wersji. 

Następujący fragment kodu XML zawiera węzeł dostawcy oświadczeń o dwa profile techniczne:

* **Identyfikator TechnicalProfile = "REST-API-SignUp"**: definiuje usługą RESTful. 
   * `Proprietary` jest ono nazywane protokołu dla dostawcy na podstawie RESTful. 
   * `InputClaims` Definiuje oświadczenia, które będą wysyłane do usługi REST usługi Azure AD B2C. 

   W tym przykładzie zawartość oświadczenia `givenName` wysyła do usługi REST jako `firstName`, zawartość oświadczenia `surname` wysyła do usługi REST jako `lastName`, i `email` wysyła jest. `OutputClaims` Element definiuje oświadczenia, które są pobierane z usług RESTful, wróć do usługi Azure AD B2C.

* **TechnicalProfile Id = "LocalAccountSignUpWithLogonEmail"**: dodaje profilu techniczne sprawdzania poprawności do istniejącego profilu technicznych (zdefiniowane w zasadach podstawowej). Podczas tworzenia konta podróży profilu techniczne sprawdzania poprawności wywołuje poprzedniego profilu technicznych. Jeśli usługi RESTful zwraca błąd HTTP 409 (błąd konfliktu), komunikat o błędzie jest wyświetlany użytkownikowi. 

Zlokalizuj `<ClaimsProviders>` węzeł, a następnie dodaj następujący fragment kodu XML w obszarze `<ClaimsProviders>` węzła:

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
        <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
        </InputClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

<!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
        </OutputClaims>
        <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
        </ValidationTechnicalProfiles>
    </TechnicalProfile>

    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 6: Dodawanie `loyaltyNumber` oświadczeń do jednostki uzależnionej strony pliku zasad, oświadczenia są wysyłane do aplikacji
Edytowanie użytkownika *SignUpOrSignIn.xml* jednostki zależnej (RP), plik i zmodyfikuj identyfikator TechnicalProfile = "PolicyProfile" elementu do dodania następujących: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Po dodaniu oświadczeń nowy kod jednostki uzależnionej strony wygląda następująco:

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Krok 7: Przekaż zasady dla Twojej dzierżawy

1. W [portalu Azure](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C**.

2. Wybierz **Framework obsługi tożsamości**.

3. Otwórz **wszystkich zasad**. 

4. Wybierz **przekazywać zasady**.

5. Wybierz **zastąpić zasady, jeśli istnieje** pole wyboru.

6. Przekaż plik TrustFrameworkExtensions.xml i upewnij się, że przekazaniem sprawdzania poprawności.

7. Powtórz poprzedni krok z plikiem SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Krok 8: Testowanie zasad niestandardowych za pomocą Uruchom teraz
1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie przejdź do **Framework obsługi tożsamości**.

    > [!NOTE]
    > **Uruchom teraz** wymaga co najmniej jednej aplikacji można preregistered dla dzierżawcy. Aby dowiedzieć się, jak zarejestrować aplikacji, zapoznaj się z usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [Rejestracja aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strony (RP) zasad niestandardowych, które można przekazać, a następnie wybierz **Uruchom teraz**.

    ![Okno B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Testowanie procesu, wpisując **testu** w **imię** pole.  
    Usługa Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.

    ![Testowanie zgodnie z zasadami](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4.  W **imię** wpisz nazwę (innego niż "Test").  
    Usługa Azure AD B2C zarejestrowaniu użytkownika, a następnie wysyła loyaltyNumber do aplikacji. Zanotuj liczbę podaną w tym JWT.

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
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, zaleca się tworzenia scenariusz przy użyciu plików zasady niestandardowe. Użytkownikowi, firma Microsoft umieściła [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Możesz pobrać pełną kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).
    
## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie interfejsu API RESTful z uwierzytelnianiem podstawowym (nazwy użytkownika i hasła)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Zabezpieczanie interfejsu API RESTful certyfikaty klienta](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
