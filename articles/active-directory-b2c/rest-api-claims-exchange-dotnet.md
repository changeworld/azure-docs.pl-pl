---
title: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika
titleSuffix: Azure AD B2C
description: Integruj wymianę oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika podczas weryfikacji danych wejściowych użytkownika.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 780d575bd7f035673510d5b1e62cff4dfd6ede16
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848761"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Integruj wymianę oświadczeń interfejsu API REST w trakcie Azure AD B2C użytkownika w trakcie sprawdzania poprawności danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Korzystając z struktury obsługi tożsamości, która jest zależna od Azure Active Directory B2C (Azure AD B2C), można zintegrować z interfejsem API RESTful w podróży użytkownika. W tym instruktażu dowiesz się, jak Azure AD B2C współdziała z usługami .NET Framework RESTful Services (Web API).

## <a name="introduction"></a>Wprowadzenie

Za pomocą Azure AD B2C można dodać własną logikę biznesową do podróży użytkownika, wywołując własną usługę RESTful. Struktura środowiska tożsamości wysyła dane do usługi RESTful w kolekcji *oświadczeń wejściowych* i odbiera dane z powrotem z RESTful w kolekcji *oświadczeń wyjściowych* . Dzięki integracji z usługą RESTful można:

* **Sprawdzanie poprawności danych wejściowych użytkownika**: Ta akcja uniemożliwia utrwalanie nieprawidłowych danych w usłudze Azure AD. Jeśli wartość użytkownika jest nieprawidłowa, usługa RESTful zwraca komunikat o błędzie, który nakazuje użytkownikowi dostarczenie wpisu. Na przykład możesz sprawdzić, czy w bazie danych klienta istnieje adres e-mail podany przez użytkownika.
* **Zastąp oświadczenia wejściowe**: na przykład, jeśli użytkownik wpisze pierwsze imię i małe litery, można sformatować nazwę tylko przy użyciu pierwszej litery.
* **Wzbogacaj dane użytkowników przez dalsze integrację z firmowymi aplikacjami biznesowymi**: usługa RESTful może odbierać adresy e-mail użytkownika, wysyłać zapytania do bazy danych klienta i zwracać numer lojalnościowy użytkownika w celu Azure AD B2C. Oświadczenia zwrotne mogą być przechowywane na koncie usługi Azure AD użytkownika, oceniane w następnych *krokach aranżacji*lub zawarte w tokenie dostępu.
* **Uruchom niestandardową logikę biznesową**: możesz wysyłać powiadomienia wypychane, aktualizować korporacyjne bazy danych, uruchamiać proces migracji użytkowników, zarządzać uprawnieniami, bazami danych inspekcji i wykonywać inne akcje.

Integrację z usługami RESTful można zaprojektować w następujący sposób:

* **Profil techniczny weryfikacji**: wywołanie usługi RESTful odbywa się w ramach weryfikacji profilu technicznego w określonym profilu technicznym. Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed przejściem użytkownika do przodu. Profil techniczny weryfikacji umożliwia:
  * Wyślij oświadczenia wejściowe.
  * Sprawdź poprawność oświadczeń wejściowych i Zgłoś niestandardowe komunikaty o błędach.
  * Wyślij wychodzące oświadczenia wyjściowe.

* **Wymiana oświadczeń**: ten projekt jest podobny do profilu technicznego weryfikacji, ale występuje w ramach kroku aranżacji. Ta definicja jest ograniczona do:
  * Wyślij oświadczenia wejściowe.
  * Wyślij wychodzące oświadczenia wyjściowe.

## <a name="restful-walkthrough"></a>Przewodnik po RESTful

W tym instruktażu tworzysz .NET Framework internetowy interfejs API, który sprawdza poprawność danych wejściowych użytkownika i udostępnia numer lojalnościowy użytkownika. Na przykład aplikacja może udzielić dostępu do korzyści z pakietu *Platinum* na podstawie numeru lojalnościowego.

Omówienie:

* Opracowywanie usługi RESTful (.NET Framework Web API)
* Korzystanie z usługi RESTful w podróży użytkownika
* Wysyłanie oświadczeń wejściowych i odczytywanie ich w kodzie
* Weryfikowanie imienia użytkownika
* Wyślij do tyłu numer lojalnościowy
* Dodawanie numeru lojalnościowego do tokenu sieci Web JSON (JWT)

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w artykule [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md) .

## <a name="step-1-create-an-aspnet-web-api"></a>Krok 1. Tworzenie interfejsu API sieci Web ASP.NET

1. W programie Visual Studio Utwórz projekt, wybierając kolejno pozycje **plik** > **Nowy** > **projekt**.
1. W oknie **Nowy projekt** wybierz pozycję **Visual C#**  > **Web** > **ASP.NET Web Application (.NET Framework)** .
1. W polu **Nazwa** wpisz nazwę aplikacji (na przykład *contoso. AADB2C. API*), a następnie wybierz **przycisk OK**.

    ![Tworzenie nowego projektu programu Visual Studio w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-create-project.png)

1. W oknie **Nowa aplikacja sieci web ASP.NET** wybierz szablon internetowego **interfejsu API** lub **aplikacji interfejsu API platformy Azure** .

    ![Wybieranie szablonu internetowego interfejsu API w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. Upewnij się, że uwierzytelnianie jest ustawione na wartość **bez uwierzytelniania**.
1. Wybierz przycisk **OK**, aby utworzyć projekt.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>Krok 2. Przygotowywanie punktu końcowego interfejsu API REST

### <a name="step-21-add-data-models"></a>Krok 2,1: Dodawanie modeli danych

Modele reprezentują oświadczenia wejściowe i dane wyjściowe oświadczeń w usłudze RESTful. Kod odczytuje dane wejściowe przez deserializacji modelu oświadczeń wejściowych z ciągu JSON na C# obiekt (model). Interfejs API sieci Web ASP.NET automatycznie deserializacji wynikowego modelu oświadczeń z powrotem do formatu JSON, a następnie zapisuje zserializowane dane do treści komunikatu odpowiedzi HTTP.

Utwórz model reprezentujący oświadczenia wejściowe, wykonując następujące czynności:

1. Jeśli Eksplorator rozwiązań nie jest jeszcze otwarty, wybierz pozycję **wyświetl** > **Eksplorator rozwiązań**.
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Modele**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Klasa**.

    ![Element menu Dodaj klasę wybraną w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-model.png)

1. Nadaj klasie nazwę `InputClaimsModel`, a następnie Dodaj następujące właściwości do klasy `InputClaimsModel`:

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

1. Utwórz nowy model, `OutputClaimsModel`a następnie Dodaj następujące właściwości do klasy `OutputClaimsModel`:

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. Utwórz jeden model, `B2CResponseContent`, który służy do generowania komunikatów o błędach sprawdzania poprawności danych wejściowych. Dodaj następujące właściwości do klasy `B2CResponseContent`, podaj brakujące odwołania, a następnie Zapisz plik:

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

### <a name="step-22-add-a-controller"></a>Krok 2,2: dodawanie kontrolera

W internetowym interfejsie API _kontroler_ jest obiektem, który obsługuje żądania HTTP. Kontroler zwraca oświadczenia wyjściowe lub, jeśli imię nie jest prawidłowe, wygeneruje komunikat o błędzie protokołu HTTP powodującego konflikt.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy folder **Kontrolery**, wybierz polecenie **Dodaj**, a następnie kliknij pozycję **Kontroler**.

    ![Dodawanie nowego kontrolera w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. W oknie **Dodawanie szkieletu** wybierz pozycję **kontroler interfejsu API sieci Web — pusty**, a następnie wybierz pozycję **Dodaj**.

    ![Wybieranie kontrolera Web API 2 — pusty w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. W oknie **Dodawanie kontrolera** Nadaj nazwę kontrolerowi **IdentityController**, a następnie wybierz pozycję **Dodaj**.

    ![Wprowadzanie nazwy kontrolera w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    Tworzenie szkieletu tworzy plik o nazwie *IdentityController.cs* w folderze *controllers* .

1. Jeśli plik *IdentityController.cs* nie jest jeszcze otwarty, kliknij go dwukrotnie, a następnie zastąp kod w pliku następującym kodem:

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

## <a name="step-3-publish-the-project-to-azure"></a>Krok 3. publikowanie projektu na platformie Azure

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **contoso. AADB2C. API** , a następnie wybierz pozycję **Publikuj**.

    ![Publikowanie w usłudze Microsoft Azure App Service za pomocą programu Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. W oknie **Publikowanie** wybierz pozycję **Microsoft Azure App Service**, a następnie wybierz pozycję **Publikuj**.

    ![Utwórz nowe App Service Microsoft Azure w programie Visual Studio](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    Zostanie otwarte okno **tworzenia App Service** . W tym celu należy utworzyć wszystkie zasoby platformy Azure niezbędne do uruchomienia aplikacji sieci Web ASP.NET na platformie Azure.

    > [!TIP]
    > Aby uzyskać więcej informacji o sposobach publikowania, zobacz [Tworzenie aplikacji sieci web ASP.NET na platformie Azure](../app-service/app-service-web-get-started-dotnet-framework.md).

1. W polu **Nazwa aplikacji sieci Web** wpisz unikatową nazwę aplikacji (prawidłowe znaki to a-z, 0-9 i łączników (-). Adres URL aplikacji sieci Web to http://< app_name >. azurewebsites. NET, gdzie *APP_NAME* jest nazwą aplikacji sieci Web. Możesz zaakceptować automatycznie wygenerowaną nazwę, która jest unikatowa.

    ![Konfigurowanie właściwości App Service](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Aby rozpocząć tworzenie zasobów platformy Azure, wybierz pozycję **Utwórz**.
    Po utworzeniu aplikacji sieci Web ASP.NET Kreator opublikuje ją na platformie Azure, a następnie uruchomi aplikację w domyślnej przeglądarce.

1. Skopiuj adres URL aplikacji sieci Web.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Krok 4. Dodawanie nowego żądania `loyaltyNumber` do schematu pliku TrustFrameworkExtensions. XML

Nie zdefiniowano jeszcze żądania `loyaltyNumber` w naszym schemacie. Dodaj definicję w ramach elementu `<BuildingBlocks>`, który można znaleźć na początku pliku *TrustFrameworkExtensions. XML* .

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

Każdy dostawca oświadczeń musi mieć co najmniej jeden profil techniczny, który określa punkty końcowe i protokoły wymagane do komunikowania się z dostawcą oświadczeń.

Dostawca oświadczeń może mieć wiele profilów technicznych z różnych powodów. Można na przykład zdefiniować wiele profilów technicznych, ponieważ dostawca oświadczeń obsługuje wiele protokołów, punkty końcowe mogą mieć różne możliwości, a wydania mogą zawierać oświadczenia, które mają różne poziomy gwarancji. Możliwe jest zaakceptowanie poufnych oświadczeń w jednej podróży użytkownika, ale nie w innej.

Poniższy fragment kodu XML zawiera węzeł dostawcy oświadczeń z dwoma profilami technicznymi:

* **Profilu technicznym ID = "REST-API-signup"** : definiuje usługę RESTful.
  * `Proprietary` jest opisany jako protokół dla dostawcy opartego na RESTful.
  * `InputClaims` definiuje oświadczenia, które będą wysyłane z Azure AD B2C do usługi REST.

    W tym przykładzie zawartość żądania `givenName` wysyłana do usługi REST jako `firstName`, zawartość `surname` jest wysyłana do usługi REST jako `lastName`i `email` wysyłana jako. Element `OutputClaims` definiuje oświadczenia pobierane z usługi RESTful z powrotem do Azure AD B2C.

* **Profilu technicznym ID = "LocalAccountSignUpWithLogonEmail"** : dodaje profil techniczny weryfikacji do istniejącego profilu technicznego (zdefiniowanego w zasadach podstawowych). W trakcie podróży proces weryfikacji profil techniczny wywołuje poprzedni profil techniczny. Jeśli usługa RESTful zwraca błąd HTTP 409 (błąd konfliktu), zostanie wyświetlony komunikat o błędzie dla użytkownika.

Zlokalizuj węzeł `<ClaimsProviders>`, a następnie Dodaj następujący fragment kodu XML w węźle `<ClaimsProviders>`:

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
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
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

Komentarze powyżej `AuthenticationType` i `AllowInsecureAuthInProduction` określają zmiany, które należy wykonać po przejściu do środowiska produkcyjnego. Aby dowiedzieć się, jak zabezpieczyć interfejsy API usługi RESTful w środowisku produkcyjnym, zobacz [Secure RESTful interfejsy API z uwierzytelnianiem Basic](secure-rest-api-dotnet-basic-auth.md) i [Secure RESTful API z uwierzytelnianiem certyfikatu](secure-rest-api-dotnet-certificate-auth.md).

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Krok 6. dodanie `loyaltyNumber` do pliku zasad jednostki uzależnionej, aby zgłoszenie zostało wysłane do aplikacji

Edytuj plik jednostki uzależnionej *SignUpOrSignIn. XML* i zmodyfikuj element profilu technicznym ID = "PolicyProfile", aby dodać następujące elementy: `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`.

Po dodaniu nowego żądania kod jednostki uzależnionej będzie wyglądać następująco:

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

## <a name="step-7-upload-the-policy-to-your-tenant"></a>Krok 7. przekazywanie zasad do dzierżawy

1. Na [Azure Portal](https://portal.azure.com)wybierz ikonę katalogów i **subskrypcji** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawcę Azure AD B2C.

1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.

1. Wybierz pozycję **platforma obsługi tożsamości**.

1. Otwórz **wszystkie zasady**.

1. Wybierz pozycję **Przekaż zasady**.

1. Zaznacz pole wyboru **Zastąp zasady, jeśli istnieje** .

1. Przekaż plik TrustFrameworkExtensions. XML i upewnij się, że przeszedł sprawdzanie poprawności.

1. Powtórz poprzedni krok przy użyciu pliku SignUpOrSignIn. XML.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>Krok 8. Testowanie zasad niestandardowych za pomocą polecenia Uruchom teraz

1. Wybierz pozycję **Azure AD B2C ustawienia**, a następnie przejdź do **struktury obsługi tożsamości**.

    > [!NOTE]
    > **Uruchomienie teraz** wymaga, aby co najmniej jedna aplikacja była przedrejestrowana w dzierżawie. Aby dowiedzieć się, jak zarejestrować aplikacje, Azure AD B2C Zobacz artykuł [Rozpoczynanie pracy](tutorial-create-tenant.md) i artykuł dotyczący [rejestracji aplikacji](tutorial-register-applications.md) .

2. Otwórz **B2C_1A_signup_signin**, załadowane zasady niestandardowe jednostki uzależnionej (RP), a następnie wybierz pozycję **Uruchom teraz**.

    ![Strona zasady niestandardowe B2C_1A_signup_signin w Azure Portal](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-run.png)

3. Przetestuj proces, wpisując **test** w **podanym polu Nazwa** .
    Azure AD B2C wyświetla komunikat o błędzie w górnej części okna.

    ![Testowanie walidacji danych wejściowych nazwy na stronie logowania do rejestracji](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-test.png)

4. W **podanym polu Nazwa** wpisz nazwę (inną niż "test").
    Azure AD B2C rejestruje użytkownika, a następnie wysyła loyaltyNumber do aplikacji. Zanotuj liczbę z tego tokenu JWT.

```JSON
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

* Po ukończeniu przewodnika [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md) zalecamy utworzenie scenariusza przy użyciu własnych niestandardowych plików zasad. W odniesieniu do Twojej dokumentacji udostępniono [przykładowe pliki zasad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw).

* Możesz pobrać kompletny kod z [przykładowego rozwiązania programu Visual Studio, aby uzyskać odwołanie](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/).

## <a name="next-steps"></a>Następne kroki

Następnym zadaniem jest zabezpieczenie interfejsu API usługi RESTful przy użyciu uwierzytelniania opartego na certyfikatach podstawowych lub klienckich. Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu uwierzytelniania podstawowego (nazwa użytkownika i hasło)](secure-rest-api-dotnet-basic-auth.md)
* [Zabezpieczanie interfejsu API usługi RESTful przy użyciu certyfikatów klienta](secure-rest-api-dotnet-certificate-auth.md)

Aby uzyskać informacje o wszystkich elementach dostępnych w profilu technicznym RESTful, zobacz temat [Reference: RESTful Technical profile](restful-technical-profile.md).
