---
title: RelyingParty — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Określ element RelyingParty zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9e8718d9216bb2f4a83f8e0373b4788210015b75
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66253771"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty** element określa podróży użytkownika w celu wymuszenia dla bieżącego żądania do usługi Azure Active Directory (Azure AD) B2C. Określa również lista oświadczeń, których potrzebuje aplikacji jednostki uzależnionej (RP) jako część wystawiony token. Aplikację jednostki Uzależnionej, np. aplikacji sieci web, mobilnych i klasycznych, wywołuje plik zasad jednostki Uzależnionej. Plik zasad RP wykonuje określonych zadań, takich jak logowanie, resetowanie hasła lub edycji profilu. Wiele aplikacji można użyć tych samych zasad RP i pojedynczej aplikacji można użyć wielu zasad. Wszystkich aplikacji jednostki Uzależnionej otrzymują tego samego tokenu przy użyciu oświadczeń i użytkownik przechodzi przez ten sam podróży użytkownika.

W poniższym przykładzie przedstawiono **RelyingParty** element *B2C_1A_signup_signin* pliku zasad:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description> 
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Opcjonalny **RelyingParty** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Domyślną podróż użytkownika dla aplikacji jednostki Uzależnionej. |
| UserJourneyBehaviors | 0:1 | Zakres zachowania podróży użytkownika. |
| TechnicalProfile | 1:1 | Profil techniczny, który jest obsługiwany przez aplikację jednostki Uzależnionej. Profil techniczny zawiera kontrakt aplikacji jednostki Uzależnionej, skontaktuj się z usługi Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` Element Określa odwołanie do identyfikatora podróży użytkownika, który zazwyczaj jest definiowany w zasadach Base lub rozszerzenia. W poniższych przykładach pokazano podróży użytkownika rejestracji lub logowania, określone w **RelyingParty** elementu:

*B2C_1A_signup_signin* zasad:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* lub *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**DefaultUserJourney** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator podróży użytkownika w ramach zasad. Aby uzyskać więcej informacji, zobacz [podróży użytkownika](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

**UserJourneyBehaviors** element zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Zakres pojedynczego logowania jednokrotnego (SSO) zachowanie sesji podróży użytkownika. |
| SessionExpiryType |0:1 | Zachowanie sesji uwierzytelniania. Możliwe wartości: `Rolling` lub `Absolute`. `Rolling` Wartość (ustawienie domyślne) wskazuje, czy użytkownik pozostaje zalogowany, tak długo, jak użytkownik jest stale aktywny w aplikacji. `Absolute` Wartość wskazuje, czy użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez sesję aplikacji okresu istnienia. |
| SessionExpiryInSeconds | 0:1 | Okres istnienia pliku cookie z sesji usługi Azure AD B2C, określony jako liczba całkowita jest przechowywane w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. |
| JourneyInsights | 0:1 | Klucz Instrumentacji usługi Azure Application Insights, która ma być używany. |
| ContentDefinitionParameters | 0:1 | Lista par kluczy i wartości do dołączenia do ładowania definicji zawartości identyfikatora URI. |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn** element zawiera się w następujących atrybutów:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Scope | Tak | Zakres jednego zachowanie logowania jednokrotnego. Możliwe wartości: `Suppressed`, `Tenant`, `Application`, lub `Policy`. `Suppressed` Wartość wskazuje, że zachowanie jest pomijane. Na przykład w przypadku jednej sesji logowania jednokrotnego, sesja nie jest zachowywana na potrzeby użytkownika, a użytkownik zawsze jest monitowany o wyboru dostawcy tożsamości. `TrustFramework` Wartość wskazuje, że zachowanie jest stosowana dla wszystkich zasad w ramach relacji zaufania. Na przykład dwóch kursów zasad dla struktury zaufania w nawigowaniu po użytkownik nie jest monitowany o wyboru dostawcy tożsamości. `Tenant` Wartość wskazuje, że zachowanie jest stosowane do wszystkich zasad w ramach dzierżawy. Na przykład użytkownik w nawigowaniu po dwóch kursów zasad dla dzierżawy nie jest monitowany o wyboru dostawcy tożsamości. `Application` Wartość wskazuje, że zachowanie jest stosowane do wszystkich zasad dla aplikacji, dzięki czemu żądania. Na przykład dwóch kursów zasad dla aplikacji w nawigowaniu po użytkownik nie jest monitowany o wyboru dostawcy tożsamości. `Policy` Wartość wskazuje, że zachowanie ma zastosowanie tylko do zasad. Na przykład dwóch kursów zasad dla struktury zaufania w nawigowaniu po użytkownik jest monitowany o wyboru dostawcy tożsamości podczas przełączania między zasadami. |
| KeepAliveInDays | Tak | Określa, jak długo użytkownik pozostaje zalogowany. Ustawienie wartości 0 spowoduje wyłączenie funkcji KMSI. Aby uzyskać więcej informacji, zobacz [nie wylogowuj mnie](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

**JourneyInsights** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TelemetryEngine | Yes | Wartość musi być `ApplicationInsights`. | 
| InstrumentationKey | Tak | Ciąg, który zawiera klucz instrumentacji dla elementu szczegółowych informacji w aplikacji. |
| DeveloperMode | Yes | Możliwe wartości: `true` lub `false`. Jeśli `true`, Application Insights przyspiesza dane telemetryczne za pośrednictwem potoku przetwarzania. To ustawienie jest dobry do tworzenia aplikacji, ale ograniczone w dużej ilości danych z dzienników aktywności szczegółowe są przeznaczone tylko do pomocy podczas tworzenia zasad niestandardowych. Nie należy używać trybu projektowania w środowisku produkcyjnym. Dzienniki zbierać wszystkie oświadczenia wysyłane do i z dostawcami tożsamości, podczas programowania. Jeśli używane w środowisku produkcyjnym, deweloper przyjmuje odpowiedzialność za dane osobowe (prywatnie osobowe) zebrane w dzienniku usługi App Insights, w której jest właścicielem. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy ta wartość jest równa `true`.|
| ClientEnabled | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`, wysyła skrypt po stronie klienta usługi Application Insights do śledzenia błędów po stronie klienta i widoku strony. | 
| ServerEnabled | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`, wysyła istniejących JSON UserJourneyRecorder jako zdarzenie niestandardowe do usługi Application Insights. | 
| TelemetryVersion | Yes | Wartość musi być `1.0.0`. | 

Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Za pomocą zasad niestandardowych w usłudze Azure AD B2C, możesz wysłać parametr ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Usługa Azure AD B2C przekazuje zapytanie parametry dynamiczny plik HTML, takich jak pliku aspx. 

Poniższy przykład przekazuje parametr o nazwie `campaignId` o wartości `hawaii` w ciągu zapytania:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Ciąg, który zawiera pary wartości klucza, który jest dołączany do ciągu zapytania identyfikatora URI obciążenia definicji zawartości. |

**ContentDefinitionParameter** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name | Yes | Nazwa parę klucz-wartość. |

Aby uzyskać więcej informacji, zobacz [skonfigurować interfejs użytkownika z zawartością dynamiczną za pomocą zasad niestandardowych](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

**Profilu technicznego** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- | 
| Id | Tak | Wartość musi być `PolicyProfile`. |

**Profilu technicznego** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Nazwa wyświetlana | 0:1 | Ciąg, który zawiera nazwę profilu technicznego, który jest widoczny dla użytkowników. |
| Opis | 0:1 | Ciąg, który zawiera opis profilu technicznego, który jest widoczny dla użytkowników. |
| Protocol | 1:1 | Protokół używany do Federacji. |
| Metadane | 0:1 | Kolekcja *elementu* par klucz/wartość wykorzystana przez protokół komunikacji z punktem końcowym w toku transakcji można skonfigurować interakcję między jednostkę uzależnioną i innych uczestników społeczności. |
| OutputClaims | 0:1 | Lista typów oświadczeń, które są wykonywane jako dane wyjściowe w profilu technicznym. Każdy z tych elementów zawiera odwołanie do **oświadczenia** już zdefiniowana w **ClaimsSchema** sekcji lub w zasadach, z której dziedziczy ten plik zasad. |
| SubjectNamingInfo | 0:1 | Nazwa podmiotu używana w tokenach. |

**Protokołu** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name | Yes | Nazwa prawidłowe protokołu obsługiwanego przez usługę Azure AD B2C, która jest używana jako część profilu technicznego. Możliwe wartości: `OpenIdConnect` lub `SAML2`. `OpenIdConnect` Wartość reprezentuje standardowy protokół OpenID Connect 1.0, zgodnie z specyfikacją foundation OpenID. `SAML2` Reprezentuje standardowy protokół SAML 2.0, zgodnie z specyfikacją języka OASIS. Nie należy używać tokenu SAML w środowisku produkcyjnym. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| oświadczenie outputClaim | 0: n | Nazwa typu oczekiwanego oświadczenia na liście obsługiwanych dla zasad, do którego subskrybuje strony jednostki uzależnionej. To oświadczenie służy jako dane wyjściowe dla profilu technicznego. |

**Oświadczenie OutputClaim** element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do **oświadczenia** już zdefiniowana w **ClaimsSchema** sekcji w pliku zasad. |
| defaultValue | Nie | Wartość domyślna, który może służyć, jeśli wartość oświadczenia jest pusta. |
| PartnerClaimType | Nie | Wysyła oświadczenie inną nazwę, zgodnie z konfiguracją w definicji typu oświadczenia. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Za pomocą **SubjectNameingInfo** elementu, można kontrolować wartość tokenu podmiotu:
- **JWT token** — `sub` oświadczenia. To jest podmiotem zabezpieczeń o tym, które token określa informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienny i nie może być ponownie przypisywany ani ponownie. Może służyć do sprawdzania autoryzacji bezpieczne, np. gdy token jest używany do uzyskania dostępu do zasobu. Domyślnie roszczenie podmiotu jest wypełniana identyfikator obiektu użytkownika w katalogu. Aby uzyskać więcej informacji, zobacz [tokenów, sesji i konfiguracji rejestracji jednokrotnej](active-directory-b2c-token-session-sso.md).
- **SAML token** — `<Subject><NameID>` element, który identyfikuje element podmiotu.

**SubjectNamingInfo** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Typ oświadczenia | Tak | Odwołanie do oświadczeń wychodzących **PartnerClaimType**. Dane wyjściowe oświadczenia, muszą być zdefiniowane w zasadach jednostki uzależnionej strona **OutputClaims** kolekcji. |

Poniższy przykład pokazuje jak zdefiniować, OpenId Connect jednostki zależnej. Informacje o nazwie podmiotu jest skonfigurowany jako `objectId`:

```XML
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Zawiera JWT token `sub` oświadczenie z użytkownika identyfikator obiektu:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


