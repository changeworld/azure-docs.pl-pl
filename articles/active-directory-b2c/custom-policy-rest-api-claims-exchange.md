---
title: Wymiany oświadczeń interfejsu API REST — usługa Azure Active Directory B2C
description: Dodaj wymianę oświadczeń interfejsu API REST do zasad niestandardowych w usłudze Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330726"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Przewodnik: Dodawanie wymiany oświadczeń interfejsu API REST do zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) umożliwia deweloperom tożsamości zintegrowanie interakcji z interfejsem API RESTful w podróży użytkownika. Na końcu tego przewodnika będzie można utworzyć podróż użytkownika usługi Azure AD B2C, która współdziała z [usługami RESTful.](custom-policy-rest-api-intro.md)

W tym scenariuszu wzbogacamy dane tokenu użytkownika, integrując się z korporacyjnym przepływem pracy. Podczas rejestracji lub logowania się za pomocą konta lokalnego lub federacyjnego usługa Azure AD B2C wywołuje interfejs API REST, aby uzyskać rozszerzone dane profilu użytkownika ze zdalnego źródła danych. W tym przykładzie usługi Azure AD B2C wysyła unikatowy identyfikator użytkownika, objectId. Interfejs API REST zwraca następnie saldo konta użytkownika (numer losowy). Użyj tego przykładu jako punktu wyjścia do integracji z własnym systemem CRM, bazy danych marketingowych, lub dowolnego przepływu pracy line-of-business.

Interakcja można również zaprojektować jako profil techniczny sprawdzania poprawności. Jest to odpowiednie, gdy interfejs API REST będzie sprawdzanie poprawności danych na ekranie i zwracanie oświadczeń. Aby uzyskać więcej informacji, zobacz [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C w celu weryfikacji danych wejściowych użytkownika.](custom-policy-rest-api-claims-validation.md)

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Aby zarejestrować się i zalogować za pomocą kont lokalnych, powinny być dostępne robocze zasady niestandardowe.
- Dowiedz się, jak [zintegrować wymianę oświadczeń interfejsu API REST w zasadach niestandardowych usługi Azure AD B2C.](custom-policy-rest-api-intro.md)

## <a name="prepare-a-rest-api-endpoint"></a>Przygotowywanie punktu końcowego interfejsu API REST

W tym instruktażu powinien mieć interfejs API REST, który sprawdza, czy obiekt usługi Azure AD B2C użytkownika jest zarejestrowany w systemie zaplecza. Jeśli jest zarejestrowany, interfejs API REST zwraca saldo konta użytkownika. W przeciwnym razie interfejs API REST rejestruje nowe konto w `50.00`katalogu i zwraca saldo początkowe .

Poniższy kod JSON ilustruje dane usługi Azure AD B2C zostanie wysłany do punktu końcowego interfejsu API REST. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Gdy interfejs API REST weryfikuje dane, musi zwrócić http 200 (Ok), z następującymi danymi JSON:

```json
{
    "balance": "760.50"
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
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Konfigurowanie profilu technicznego interfejsu API RESTful 

Spokojny [profil techniczny](restful-technical-profile.md) zapewnia wsparcie dla wzajemnego połączenia z własną usługą RESTful. Usługa Azure AD B2C wysyła dane do `InputClaims` usługi RESTful `OutputClaims` w kolekcji i odbiera dane z powrotem w kolekcji. Znajdź **ClaimsProviders** element <em>**`TrustFrameworkExtensions.xml`**</em> w pliku i dodać nowego dostawcy oświadczeń w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

W tym przykładzie `userLanguage` zostanie wysłany do `lang` usługi REST, jak w ramach ładunku JSON. Wartość `userLanguage` oświadczenia zawiera bieżący identyfikator języka użytkownika. Aby uzyskać więcej informacji, zobacz [program rozpoznawania oświadczeń](claim-resolver-overview.md).

Komentarze powyżej `AuthenticationType` `AllowInsecureAuthInProduction` i określić zmiany, które należy wprowadzić podczas przenoszenia do środowiska produkcyjnego. Aby dowiedzieć się, jak zabezpieczyć interfejsy API restful do produkcji, zobacz [Bezpieczny interfejs API restful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Dodawanie kroku aranżacji

[Procesy podróży użytkownika](userjourneys.md) określają jawne ścieżki, za pomocą których zasady umożliwiają aplikacji jednostki uzależniającej uzyskanie żądanych oświadczeń dla użytkownika. Proces użytkownika jest reprezentowany jako sekwencja aranżacji, która musi być przestrzegana dla pomyślnej transakcji. Kroki aranżacji można dodawać lub odejmować. W takim przypadku zostanie dodasz nowy krok aranżacji, który jest używany do rozszerzenia informacji dostarczonych do aplikacji po rejestracji użytkownika lub logowania za pośrednictwem wywołania interfejsu API REST.

1. Otwórz plik podstawowy zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Wyszukaj `<UserJourneys>` element. Skopiuj cały element, a następnie usuń go.
1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Wklej `<UserJourneys>` do pliku rozszerzeń, po `<ClaimsProviders>` zamknięciu elementu.
1. Znajdź `<UserJourney Id="SignUpOrSignIn">`program i dodaj następujący krok aranżacji przed ostatnim.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refaktoryzacja ostatniego kroku `Order` `8`aranżacji, zmieniając na . Ostatnie dwa kroki aranżacji powinny wyglądać następująco:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Powtórz dwa ostatnie kroki dla **profilEdit** i **PasswordReset** podróży użytkownika.


## <a name="include-a-claim-in-the-token"></a>Dołącz roszczenie do tokenu 

Aby zwrócić `balance` oświadczenie z powrotem do aplikacji jednostki <em> `SocialAndLocalAccounts/` </em> uzależniającej, dodaj oświadczenie wyjściowe do pliku. Dodanie oświadczenia wyjściowego spowoduje wystawienie oświadczenia do tokenu po pomyślnej podróży użytkownika i zostanie wysłane do aplikacji. Zmodyfikuj element profilu technicznego `balance` w sekcji jednostki uzależniona, aby dodać jako oświadczenie wyjściowe.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Powtórz ten krok dla procesów podróży użytkowników **ProfileEdit.xml**i **PasswordReset.xml.**

Zapisywanie zmienionych plików: *TrustFrameworkBase.xml*i *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*i *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
1. Wybierz **platformę środowiska tożsamości**.
1. Wybierz **pozycję Przekaż zasady niestandardowe**, a następnie przekaż zmienione pliki zasad: *TrustFrameworkBase.xml*i *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*i *PasswordReset.xml*. 
1. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz.**
1. Możesz zarejestrować się przy użyciu adresu e-mail lub konta na Facebooku.
1. Token wysłany z powrotem `balance` do aplikacji zawiera oświadczenie.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Następne kroki


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zabezpieczyć interfejsy API, zobacz następujące artykuły:

- [Przewodnik: Integrowanie wymiany oświadczeń interfejsu API REST w podróży użytkownika usługi Azure AD B2C jako krok aranżacji](custom-policy-rest-api-claims-exchange.md)
- [Zabezpiecz swój interfejs API RESTful](secure-rest-api.md)
- [Referencje: RESTful profil techniczny](restful-technical-profile.md)
