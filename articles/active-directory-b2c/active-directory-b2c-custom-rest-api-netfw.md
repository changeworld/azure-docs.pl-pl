---
title: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e44bb1ed6a7a090b4b1213ca14be2b42642475e4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717288"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integracja interfejsu API REST wymianą oświadczeń podróży użytkownika usługi Azure AD B2C jako sprawdzanie poprawności danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Za pomocą platformy środowiska tożsamości, która podporządkowana narzędziu Azure Active Directory B2C (Azure AD B2C) można zintegrować z interfejsu API RESTful w podróży użytkownika. W tym przewodniku dowiesz się, jak usługa Azure AD B2C współdziała z usługami .NET Framework RESTful (interfejsu API sieci web).

## <a name="introduction"></a>Wprowadzenie
Za pomocą usługi Azure AD B2C, możesz dodać własną logiką biznesową podróży użytkownika, wywołując usługi RESTful. Struktura środowiska tożsamości wysyła dane do usługi RESTful w *wejściowych roszczenia* kolekcji i odbiera dane z kopii zgodne ze specyfikacją REST w *danych wyjściowych oświadczeń* kolekcji. Dzięki integracji z usługą RESTful można wykonywać następujące czynności:

* **Sprawdza poprawność danych wejściowych użytkownika**: Ta akcja uniemożliwia źle sformułowane danych utrwalania w usłudze Azure AD. Jeśli wartość przez użytkownika nie jest prawidłowy, usługi RESTful zwraca komunikat o błędzie z monitem użytkownika o podanie wpis. Na przykład można sprawdzić, czy adres e-mail, dostarczone przez użytkownika istnieje w bazie danych klienta.
* **Zastąp oświadczeń wejściowych**: Na przykład jeśli użytkownik wprowadzi nazwę pierwszego wszystkie małe lub wielkie litery, możesz sformatować nazwy tylko pierwszą literą wielką literą.
* **Wzbogacanie danych użytkownika dalsze integrowanie z aplikacji firmowych line-of-business**: Usługi RESTful może odbierać adres e-mail użytkownika, wykonywania zapytań klienta w bazie danych i zwracać numer lojalności użytkownika do usługi Azure AD B2C. Zwracany oświadczenia mogą być przechowywane w usłudze Azure AD konta, ocenione w ciągu następnych *kroki aranżacji*, lub zawartych w tokenie dostępu.
* **Uruchom niestandardową logikę biznesową**: Można wysyłać powiadomienia wypychane, aktualizacji baz danych firmowych, uruchamiany jest proces migracji użytkowników, zarządzanie uprawnieniami, inspekcji bazy danych i wykonywania innych akcji.

Integracja z usług RESTful można zaprojektować w następujący sposób:

* **Profil techniczny weryfikacji**: Wywołanie usługi RESTful odbywa się w ramach profilu technicznego sprawdzania poprawności określonego profilu technicznego. Profil techniczny weryfikacji sprawdza poprawność danych wprowadzonych przez użytkownika przed podróży użytkownika przenosi do przodu. Z profilu technicznego weryfikacji możesz wykonywać następujące czynności:
   * Wysyłać oświadczenia wejściowego.
   * Sprawdzanie poprawności danych wejściowych oświadczeń i zgłosić niestandardowe komunikaty o błędach.
   * Wysyłać oświadczenia wstecz danych wyjściowych.

* **Wymiana oświadczeń**: Ten projekt jest podobny do profilu technicznego sprawdzania poprawności, ale się stanie, w ramach kroku aranżacji. Ta definicja jest ograniczone do:
   * Wysyłać oświadczenia wejściowego.
   * Wysyłać oświadczenia wstecz danych wyjściowych.

## <a name="restful-walkthrough"></a>Przewodnik rESTful
W tym przewodniku tworzysz .NET Framework internetowego interfejsu API, sprawdza poprawność danych wejściowych użytkownika, która oferuje szereg dostępnych na urządzeniach użytkowników. Na przykład udzielić aplikacji dostępu do *-pakiet platynowy korzyści* na podstawie liczby dostępnych na urządzeniach.

Omówienie:
* Twórz usługi RESTful (.NET Framework interfejsu API sieci web).
* Użyj usługi RESTful w podróży użytkownika.
* Wysyłanie danych wejściowych oświadczeń i je odczytać w kodzie.
* Sprawdź poprawność imię użytkownika.
* Wyślij ponownie numer lojalnościowych.
* Dodaj numer lojalności do sieci Web tokenu JSON (JWT).

## <a name="prerequisites"></a>Wymagania wstępne
Wykonaj kroki [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) artykułu.

## <a name="step-1-create-an-aspnet-web-api"></a>Krok 1: Tworzenie internetowego interfejsu API platformy ASP.NET

1. W programie Visual Studio Utwórz projekt, wybierając **pliku** > **New** > **projektu**.

2. W **nowy projekt** wybierz **Visual C#** > **Web** > **aplikacji sieci Web platformy ASP.NET (.NET Framework)**.

3. W **nazwa** wpisz nazwę aplikacji (na przykład *Contoso.AADB2C.API*), a następnie wybierz pozycję **OK**.

    ![Utwórz nowy projekt programu visual studio](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. W **Nowa aplikacja internetowa ASP.NET** wybierz **interfejsu API sieci Web** lub **aplikacji interfejsu API usługi Azure** szablonu.

    ![Wybierz szablon interfejsu API sieci web](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. Upewnij się, że uwierzytelnianie jest ustawione na **bez uwierzytelniania**.

6. Wybierz przycisk **OK**, aby utworzyć projekt.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Krok 2: Przygotowanie punktu końcowego interfejsu API REST

### <a name="step-21-add-data-models"></a>Krok 2.1. Dodawanie modeli danych
Modele reprezentują oświadczeń wejściowych i danych wyjściowych oświadczeń danych w usłudze RESTful. Kod odczytuje dane wejściowe przy deserializacji modelu oświadczeń wejściowych z ciągu JSON do obiektu języka C# (model). ASP.NET web API automatycznie deserializuje model oświadczeń danych wyjściowych do formatu JSON, a następnie zapisuje dane serializowane do treści komunikatu odpowiedzi HTTP.

Utwórz model, który reprezentuje oświadczeń wejściowych, wykonując następujące czynności:

1. Jeśli w Eksploratorze rozwiązań nie jest jeszcze otwarty, wybierz **widoku** > **Eksploratora rozwiązań**.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**.

    ![Dodawanie modelu](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. Nazwa klasy `InputClaimsModel`, a następnie dodaj poniższe właściwości do `InputClaimsModel` klasy:

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

4. Utwórz nowy model `OutputClaimsModel`, a następnie dodaj poniższe właściwości do `OutputClaimsModel` klasy:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. Utwórz jeden model więcej, `B2CResponseContent`, umożliwiający generują komunikaty o błędach weryfikacji danych wejściowych. Dodaj następujące właściwości do `B2CResponseContent` klasy, podaj brakujących odwołań, a następnie zapisz plik:

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

### <a name="step-22-add-a-controller"></a>Krok 2.2. Dodawanie kontrolera
W interfejsie web API _kontrolera_ jest obiektem, który obsługuje żądania HTTP. Ten kontroler zwraca dane wyjściowe oświadczenia lub, jeśli imię jest nieprawidłowe, zwraca komunikat o błędzie HTTP konflikt.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Kontroler**.

    ![Dodaj nowy kontroler](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. W **Dodawanie szkieletu** wybierz **kontroler internetowego interfejsu API — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Pusty kontroler - wybierz Web API 2](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. W **Dodaj kontroler** okna, nazwy kontrolera **IdentityController**, a następnie wybierz pozycję **Dodaj**.

    ![Wpisz nazwę kontrolera](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Szkieletu tworzy plik o nazwie *IdentityController.cs* w *kontrolerów* folderu.

4. Jeśli *IdentityController.cs* plik nie jest już otwarty, kliknij go dwukrotnie, a następnie Zastąp kod w pliku następującym kodem:

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
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **Contoso.AADB2C.API** projektu, a następnie wybierz **Publikuj**.

    ![Publikowanie w usłudze Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. W **Publikuj** wybierz **Microsoft Azure App Service**, a następnie wybierz pozycję **Publikuj**.

    ![Utwórz nowy Microsoft Azure App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **Tworzenie usługi App Service** zostanie otwarte okno. Możesz utworzyć wszystkich zasobów platformy Azure niezbędnych do uruchomienia aplikacji internetowej ASP.NET na platformie Azure.

    > [!NOTE]
    >Aby uzyskać więcej informacji o sposobie publikowania, zobacz [tworzenie aplikacji internetowej ASP.NET na platformie Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. W **Nazwa aplikacji sieci Web** wpisz unikatową nazwę aplikacji (prawidłowe znaki to a-z, 0-9 i łączniki (-). Adres URL aplikacji sieci web jest http://<app_name>.azurewebsites.NET, gdzie *nazwa_aplikacji* to nazwa aplikacji sieci web. Możesz zaakceptować automatycznie wygenerowaną nazwę, która jest unikatowa.

    ![Podaj właściwości usługi App Service](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Aby rozpocząć tworzenie zasobów platformy Azure, wybierz pozycję **Utwórz**.  
    Po utworzeniu aplikacji internetowej platformy ASP.NET Kreator publikuje go na platformę Azure, a następnie uruchamia aplikację w domyślnej przeglądarce.

6. Skopiuj adres URL aplikacji sieci web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 4: Dodaj nowy `loyaltyNumber` oświadczenie do schematu pliku TrustFrameworkExtensions.xml
`loyaltyNumber` Oświadczenia nie został jeszcze zdefiniowany w naszym schematu. Dodaj definicję w ramach `<BuildingBlocks>` element, który znajduje się na początku *TrustFrameworkExtensions.xml* pliku.

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

## <a name="step-5-add-a-claims-provider"></a>Krok 5. Dodawanie dostawcy oświadczeń
Każdego dostawcy oświadczeń musi mieć co najmniej jeden profil technicznych, które określają punktów końcowych i protokoły wymagane do komunikowania się z dostawcą oświadczeń.

Dostawcy oświadczeń może mieć wiele profilów technicznych z różnych powodów. Na przykład wiele profilów Technical Preview może być zdefiniowana, ponieważ dostawcy oświadczeń obsługuje wiele protokołów, punkty końcowe mogą mieć różne możliwości lub wersji mogą zawierać oświadczenia, które mają różne poziomy gwarancji. Może być akceptowane oświadczenia poufnych w podróży jednego użytkownika, ale nie w innej wersji.

Poniższy fragment kodu XML zawiera węzeł dostawcy oświadczeń, dwa profile techniczne:

* **Identyfikator profilu technicznego = "REST-API-SignUp"**: Definiuje usługi RESTful.
  * `Proprietary` jest określana jako protokół dla dostawcy usług na podstawie zgodne ze specyfikacją REST.
  * `InputClaims` Definiuje oświadczenia, które będą wysyłane z usługi Azure AD B2C do usługi REST.

    W tym przykładzie zawartość oświadczenie `givenName` wysyła do usługi REST jako `firstName`, zawartość oświadczenie `surname` wysyła do usługi REST jako `lastName`, i `email` wysyła się. `OutputClaims` Element definiuje oświadczenia, które są pobierane z usługi RESTful, wróć do usługi Azure AD B2C.

* **Identyfikator profilu technicznego = "LocalAccountSignUpWithLogonEmail"**: Dodaje profilu technicznego sprawdzania poprawności do istniejącego profilu technicznego (zdefiniowane w zasadach podstawowych). Podczas tworzenia konta podróży profilu technicznego weryfikacji wywołuje poprzedniego profilu technicznego. Jeśli usługi RESTful zwraca błąd HTTP 409 (błąd konfliktu), wyświetlony komunikat o błędzie dla użytkownika.

Znajdź `<ClaimsProviders>` węzła, a następnie dodaj następujący fragment kodu XML w obszarze `<ClaimsProviders>` węzła:

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
        <Item Key="AllowInsecureAuthInProduction">true</Item>
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

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 6: Dodaj `loyaltyNumber` oświadczenia do jednostki uzależnionej strona pliku zasad, więc oświadczenia są wysyłane do aplikacji
Edytuj swoje *SignUpOrSignIn.xml* jednostki zależnej (RP) plik, a następnie zmodyfikuj identyfikator profilu technicznego = elementu "PolicyProfile" Dodaj następujący kod: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Po dodaniu nowego oświadczenia, jednostki uzależnionej strona kod wygląda następująco:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Krok 7: Przekazywanie zasad dla Twojej dzierżawy

1. W [witryny Azure portal](https://portal.azure.com), przełącz się do [kontekstu dzierżawy usługi Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md), a następnie otwórz **usługi Azure AD B2C**.

2. Wybierz **struktura środowiska tożsamości**.

3. Otwórz **wszystkie zasady**.

4. Wybierz **przekazywać zasady**.

5. Wybierz **Zastąp zasady Jeśli istnieje** pole wyboru.

6. Przekaż plik TrustFrameworkExtensions.xml i upewnij się, że przekazuje sprawdzania poprawności.

7. Powtórz poprzedni krok z plikiem SignUpOrSignIn.xml.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Krok 8: Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz
1. Wybierz **ustawienia usługi Azure AD B2C**, a następnie przejdź do **struktura środowiska tożsamości**.

    > [!NOTE]
    > **Uruchom teraz** wymaga co najmniej jedną aplikację, aby być jest wstępnie zarejestrowane w ramach dzierżawy. Aby dowiedzieć się, jak zarejestrować aplikacji, zobacz temat usługi Azure AD B2C [wprowadzenie](active-directory-b2c-get-started.md) artykułu lub [rejestracji aplikacji](active-directory-b2c-app-registration.md) artykułu.

2. Otwórz **B2C_1A_signup_signin**, jednostki uzależnionej strona (RP) zasad niestandardowych, które przekazane, a następnie wybierz **Uruchom teraz**.

    ![W oknie B2C_1A_signup_signin](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. Przetestuj proces, wpisując **testu** w **imię** pole.  
    Usługa Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.

    ![Testowanie zasad](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. W **imię** wpisz nazwę (innego niż "Test").  
    Usługa Azure AD B2C loguje się użytkownik, a następnie wysyła loyaltyNumber do aplikacji. Zanotuj liczbę podaną w tym token JWT.

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Opcjonalnie) Pobierz pliki zasad kompletny i kodu
* Po ukończeniu [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) wskazówki, firma Microsoft zaleca tworzenie scenariusza za pomocą plików zasad niestandardowych. Dla Twojej informacji udostępniliśmy [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).
* Możesz pobrać kompletny kod z [przykładowe rozwiązanie Visual Studio dla odwołania](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie interfejsu API RESTful za pomocą uwierzytelniania podstawowego (nazwa użytkownika i hasło)](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [Zabezpieczanie interfejsu API RESTful z wykorzystaniem certyfikatów klienta](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
