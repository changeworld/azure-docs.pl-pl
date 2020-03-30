---
title: REST API twierdzi, że jest wybrana jako weryfikacja
titleSuffix: Azure AD B2C
description: Przewodnik do tworzenia usługi Azure AD B2C podróży użytkownika, który współdziała z usług RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330816"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C w celu sprawdzania poprawności danych wejściowych użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Struktura środowiska tożsamości (IEF), która stanowi podstawę usługi Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperom tożsamości zintegrowanie interakcji z interfejsem API RESTful w podróży użytkownika.  Na końcu tego przewodnika będzie można utworzyć proces użytkownika usługi Azure AD B2C, który współdziała z [usługami RESTful](custom-policy-rest-api-intro.md) w celu sprawdzenia poprawności danych wejściowych użytkownika.

W tym scenariuszu dodamy możliwość dla użytkowników, aby wprowadzić numer lojalnościowy na stronie rejestracji usługi Azure AD B2C. Sprawdzimy, czy ta kombinacja wiadomości e-mail i numeru lojalnościowego jest mapowana na kod promocyjny, wysyłając te dane do interfejsu API REST. Jeśli interfejs API REST znajdzie kod promocyjny dla tego użytkownika, zostanie on zwrócony do usługi Azure AD B2C. Na koniec kod promocyjny zostanie wstawiony do oświadczeń tokenu dla aplikacji do użycia.

Można również zaprojektować interakcję jako krok aranżacji. Jest to odpowiednie, gdy interfejs API REST nie będzie sprawdzania poprawności danych na ekranie i zawsze zwracać oświadczenia. Aby uzyskać więcej informacji, zobacz [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C jako krok aranżacji.](custom-policy-rest-api-claims-exchange.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Aby zarejestrować się i zalogować za pomocą kont lokalnych, powinny być dostępne robocze zasady niestandardowe.
- Dowiedz się, jak [zintegrować wymianę oświadczeń interfejsu API REST w zasadach niestandardowych usługi Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Przygotowywanie punktu końcowego interfejsu API REST

W tym instruktażu powinien mieć interfejs API REST, który sprawdza, czy adres e-mail jest zarejestrowany w systemie zaplecza z identyfikatorem lojalnościowym. Jeśli jest zarejestrowany, interfejs API REST powinien zwrócić kod promocyjny rejestracji, który klient może wykorzystać do zakupu towarów w aplikacji. W przeciwnym razie interfejs API REST powinien zwrócić komunikat o błędzie HTTP 409: "Identyfikator lojalności '{loyalty ID}' nie jest skojarzony z adresem e-mail '{email}'."

Poniższy kod JSON ilustruje dane usługi Azure AD B2C zostanie wysłany do punktu końcowego interfejsu API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Gdy interfejs API REST weryfikuje dane, musi zwrócić http 200 (Ok), z następującymi danymi JSON:

```json
{
    "promoCode": "24534"
}
```

Jeśli sprawdzanie poprawności nie powiodło się, interfejs API REST musi `userMessage` zwrócić HTTP 409 (Konflikt), z elementem JSON. IEF oczekuje `userMessage` oświadczenia, że interfejs API REST zwraca. To oświadczenie zostanie przedstawione jako ciąg do użytkownika, jeśli sprawdzanie poprawności nie powiedzie się.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Konfiguracja punktu końcowego interfejsu API REST wykracza poza zakres tego artykułu. Utworzyliśmy przykład [usługi Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-reference) Dostęp do pełnego kodu funkcji platformy Azure można uzyskać w [witrynie GitHub.](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)

## <a name="define-claims"></a>Definiowanie oświadczeń

Oświadczenie zapewnia tymczasowe przechowywanie danych podczas wykonywania zasad usługi Azure AD B2C. Można zadeklarować oświadczenia w sekcji [schemat oświadczeń.](claimsschema.md) 

1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Wyszukaj [element BuildingBlocks.](buildingblocks.md) Jeśli element nie istnieje, dodaj go.
1. Znajdź [ClaimsSchema](claimsschema.md) element. Jeśli element nie istnieje, dodaj go.
1. Dodaj następujące oświadczenia do **claimsschema** elementu.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Konfigurowanie profilu technicznego interfejsu API RESTful 

Spokojny [profil techniczny](restful-technical-profile.md) zapewnia wsparcie dla wzajemnego połączenia z własną usługą RESTful. Usługa Azure AD B2C wysyła dane do `InputClaims` usługi RESTful `OutputClaims` w kolekcji i odbiera dane z powrotem w kolekcji. Znajdź **ClaimsProviders** element i dodać nowego dostawcy oświadczeń w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

W tym przykładzie `userLanguage` zostanie wysłany do `lang` usługi REST, jak w ramach ładunku JSON. Wartość `userLanguage` oświadczenia zawiera bieżący identyfikator języka użytkownika. Aby uzyskać więcej informacji, zobacz [program rozpoznawania oświadczeń](claim-resolver-overview.md).

Komentarze powyżej `AuthenticationType` `AllowInsecureAuthInProduction` i określić zmiany, które należy wprowadzić podczas przenoszenia do środowiska produkcyjnego. Aby dowiedzieć się, jak zabezpieczyć interfejsy API restful do produkcji, zobacz [Bezpieczny interfejs API restful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Sprawdzanie poprawności danych wejściowych użytkownika

Aby uzyskać numer lojalnościowy użytkownika podczas rejestracji, należy zezwolić użytkownikowi na wprowadzenie tych danych na ekranie. Dodaj oświadczenie wyjściowe **loyaltyId** do strony rejestracji, dodając je do elementu istniejącego profilu `OutputClaims` technicznego rejestracji. Określ całą listę oświadczeń danych wyjściowych, aby kontrolować kolejność, w jakiej oświadczenia są prezentowane na ekranie.  

Dodaj odwołanie do profilu technicznego sprawdzania poprawności do profilu `REST-ValidateProfile`technicznego rejestracji, który wywołuje . Nowy profil techniczny sprawdzania poprawności zostanie dodany `<ValidationTechnicalProfiles>` do górnej części kolekcji zdefiniowanej w zasadach podstawowych. To zachowanie oznacza, że tylko po pomyślnym sprawd owiednie poprawności usługa Azure AD B2C przechodzi do tworzenia konta w katalogu.   

1. Znajdź **ClaimsProviders** element. Dodaj nowego dostawcę oświadczeń w następujący sposób:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Dołącz roszczenie do tokenu 

Aby zwrócić oświadczenie kodu promocyjnego z powrotem do aplikacji jednostki uzależniającej, dodaj oświadczenie danych wyjściowych do <em> `SocialAndLocalAccounts/` </em> pliku. Oświadczenie danych wyjściowych pozwoli oświadczenia, które mają zostać dodane do tokenu po pomyślnej podróży użytkownika i zostaną wysłane do aplikacji. Zmodyfikuj element profilu technicznego w `promoCode` sekcji jednostki uzależniona, aby dodać jako oświadczenie wyjściowe.
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **platformę środowiska tożsamości**.
1. Wybierz **pozycję Przekaż zasady niestandardowe**, a następnie przekaż zmienione pliki zasad: *TrustFrameworkExtensions.xml*i *SignUpOrSignin.xml*. 
1. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz.**
1. Powinieneś być w stanie zarejestrować się przy użyciu adresu e-mail.
1. Kliknij link **Zarejestruj się teraz.**
1. W **pliku Identyfikator lojalnościowy**wpisz 1234 i kliknij przycisk **Kontynuuj**. W tym momencie powinien zostać wyświetlony komunikat o błędzie sprawdzania poprawności.
1. Zmień na inną wartość i kliknij przycisk **Kontynuuj**.
1. Token wysłany z powrotem `promoCode` do aplikacji zawiera oświadczenie.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

- [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C jako krok aranżacji](custom-policy-rest-api-claims-exchange.md)
- [Zabezpiecz swój interfejs API RESTful](secure-rest-api.md)
- [Referencje: RESTful profil techniczny](restful-technical-profile.md)
