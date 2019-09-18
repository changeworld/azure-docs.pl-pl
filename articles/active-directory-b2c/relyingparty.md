---
title: RelyingParty — Azure Active Directory B2C | Microsoft Docs
description: Określ element RelyingParty zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1bfa945843d185a46f1f1d79fd4dab0e991c769
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063813"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **RelyingParty** określa podróż użytkownika, aby wymusić bieżące żądanie do Azure Active Directory B2C (Azure AD B2C). Określa także listę oświadczeń, które są wymagane przez aplikację jednostki uzależnionej (RP) jako część wystawionego tokenu. Aplikacja RP, taka jak aplikacja sieci Web, aplikacji mobilnej lub klasycznej, wywołuje plik zasad RP. Plik zasad RP wykonuje określone zadanie, takie jak logowanie, Resetowanie hasła lub edytowanie profilu. Wiele aplikacji może korzystać z tych samych zasad RP, a pojedyncza aplikacja może korzystać z wielu zasad. Wszystkie aplikacje RP otrzymują ten sam token z oświadczeniami, a użytkownik przechodzi przez tę samą podróż użytkownika.

Poniższy przykład przedstawia element **RelyingParty** w pliku zasad *B2C_1A_signup_signin* :

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

Opcjonalny element **RelyingParty** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Domyślna podróż użytkownika dla aplikacji RP. |
| UserJourneyBehaviors | 0:1 | Zakres zachowań podróży użytkownika. |
| TechnicalProfile | 1:1 | Profil techniczny obsługiwany przez aplikację RP. Profil techniczny zawiera kontrakt dla aplikacji RP do kontaktu Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` Element określa odwołanie do identyfikatora podróży użytkownika, która jest zwykle zdefiniowana w zasadach podstawowych lub zasad rozszerzenia. W poniższych przykładach przedstawiono drogę rejestracji lub logowania użytkownika określoną w elemencie **RelyingParty** :

Zasady *B2C_1A_signup_signin* :

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

Element **DefaultUserJourney** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| referenceId | Tak | Identyfikator podróży użytkownika w zasadach. Aby uzyskać więcej informacji, zobacz [podróże użytkownika](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Element **UserJourneyBehaviors** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Zakres działania dotyczącego logowania jednokrotnego (SSO) w trakcie podróży użytkownika. |
| SessionExpiryType |0:1 | Zachowanie podczas uwierzytelniania sesji. Możliwe wartości: `Rolling` lub `Absolute`. `Rolling` Wartość (domyślnie) wskazuje, że użytkownik pozostanie zalogowany, o ile użytkownik jest stale aktywny w aplikacji. `Absolute` Wartość oznacza, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez okres istnienia sesji aplikacji. |
| SessionExpiryInSeconds | 0:1 | Okres istnienia pliku cookie sesji B2C's usługi Azure AD został określony jako liczba całkowita przechowywana w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. |
| JourneyInsights | 0:1 | Klucz Instrumentacji usługi Azure Application Insights, który ma być używany. |
| ContentDefinitionParameters | 0:1 | Lista par wartości klucza do dołączenia do identyfikatora URI ładowania definicji zawartości. |

### <a name="singlesignon"></a>SingleSignOn

Element **SingleSignon** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Scope | Tak | Zakres zachowania logowania jednokrotnego. Możliwe wartości: `Suppressed`, `Tenant`, `Application`lub. `Policy` `Suppressed` Wartość wskazuje, że zachowanie jest pomijane. Na przykład w przypadku sesji logowania jednokrotnego dla użytkownika nie jest utrzymywana żadna sesja, a użytkownik jest zawsze monitowany o wybranie dostawcy tożsamości. `TrustFramework` Wartość wskazuje, że zachowanie jest stosowane dla wszystkich zasad w strukturze zaufania. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla struktury zaufania nie jest monitowany o wybranie dostawcy tożsamości. `Tenant` Wartość wskazuje, że zachowanie jest stosowane do wszystkich zasad w dzierżawie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla dzierżawy nie jest monitowany o wybranie dostawcy tożsamości. `Application` Wartość wskazuje, że zachowanie jest stosowane do wszystkich zasad dla aplikacji zgłaszającej żądanie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla aplikacji nie jest monitowany o wybranie dostawcy tożsamości. `Policy` Wartość wskazuje, że zachowanie dotyczy tylko zasad. Na przykład użytkownik przechodzący przez dwie przedziały zasad dla struktury zaufania jest monitowany o wybranie dostawcy tożsamości podczas przełączania między zasadami. |
| KeepAliveInDays | Tak | Określa, jak długo użytkownik jest zalogowany. Ustawienie wartości 0 powoduje wyłączenie funkcji KMSI. Aby uzyskać więcej informacji, zobacz temat nie [wylogowuj mnie](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

Element **JourneyInsights** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TelemetryEngine | Tak | Wartość musi być `ApplicationInsights`. |
| InstrumentationKey | Tak | Ciąg, który zawiera klucz Instrumentacji dla elementu Application Insights. |
| Deweloper | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`Application Insights przyspiesza dane telemetryczne za pomocą potoku przetwarzania. To ustawienie jest dobre w przypadku programowania, ale jest ograniczone do dużych woluminów, szczegółowe dzienniki działań są przeznaczone tylko do pomocy w opracowywaniu zasad niestandardowych. Nie używaj trybu deweloperskiego w środowisku produkcyjnym. Dzienniki zbierają wszystkie oświadczenia wysyłane do i od dostawców tożsamości podczas opracowywania. Jeśli jest używany w środowisku produkcyjnym, programista przyjmuje odpowiedzialność za dane OSOBowe (informacje z możliwością zidentyfikowaną prywatnie) zebrane w dzienniku usługi App Insights. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy ta wartość jest `true`ustawiona na.|
| ClientEnabled | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`program wysyła skrypt po stronie klienta Application Insights na potrzeby śledzenia widoku strony i błędów po stronie klienta. |
| ServerEnabled | Tak | Możliwe wartości: `true` lub `false`. Jeśli `true`, program wysyła istniejący kod JSON UserJourneyRecorder jako zdarzenie niestandardowe do Application Insights. |
| TelemetryVersion | Tak | Wartość musi być `1.0.0`. |

Aby uzyskać więcej informacji, zobacz [zbieranie dzienników](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Korzystając z zasad niestandardowych w Azure AD B2C, można wysłać parametr w ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Azure AD B2C przekazuje parametry ciągu zapytania do dynamicznego pliku HTML, takiego jak plik aspx.

Poniższy przykład przekazuje parametr o nazwie `campaignId` przy użyciu `hawaii` wartości w ciągu zapytania:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Element **ContentDefinitionParameters** zawiera następujący element:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Ciąg zawierający parę wartości klucza, która jest dołączana do ciągu zapytania w identyfikatorze URI ładowania definicji zawartości. |

Element **ContentDefinitionParameter** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name | Tak | Nazwa pary klucz wartość. |

Aby uzyskać więcej informacji, zobacz [Konfigurowanie interfejsu użytkownika z zawartością dynamiczną przy użyciu zasad niestandardowych](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

Element **profilu technicznym** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Wartość musi być `PolicyProfile`. |

**Profilu technicznym** zawiera następujące elementy:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Ciąg, który zawiera nazwę profilu technicznego, który jest wyświetlany użytkownikom. |
| Opis | 0:1 | Ciąg, który zawiera opis profilu technicznego, który jest wyświetlany użytkownikom. |
| Protocol | 1:1 | Protokół używany w Federacji. |
| Metadane | 0:1 | Kolekcja *elementów* par klucz/wartość wykorzystywana przez protokół do komunikowania się z punktem końcowym w trakcie transakcji w celu skonfigurowania interakcji między jednostką uzależnioną i innymi uczestnikami społeczności. |
| OutputClaims | 0:1 | Lista typów roszczeń, które są pobierane jako dane wyjściowe w profilu technicznym. Każdy z tych elementów zawiera odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** lub w zasadach, z których dziedziczy ten plik zasad. |
| SubjectNamingInfo | 0:1 | Nazwa podmiotu używana w tokenach. |

Element **Protocol** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Name | Tak | Nazwa prawidłowego protokołu obsługiwanego przez Azure AD B2C, który jest używany jako część profilu technicznego. Możliwe wartości: `OpenIdConnect` lub `SAML2`. `OpenIdConnect` Wartość reprezentuje standard protokołu OpenID Connect Connect 1,0 zgodnie ze specyfikacją OpenID Connect Foundation. `SAML2` Reprezentuje standard protokołu SAML 2,0 zgodnie ze specyfikacją języka Oasis. Nie używaj tokenu SAML w środowisku produkcyjnym. |

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera następujący element:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 0: n | Nazwa oczekiwanego typu na liście obsługiwanych zasad, dla których zasubskrybuje się Jednostka uzależniona. To zgłoszenie służy jako dane wyjściowe profilu technicznego. |

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** w pliku zasad. |
| defaultValue | Nie | Wartość domyślna, która może być używana, jeśli wartość jest pusta. |
| PartnerClaimType | Nie | Wysyła oświadczenia w innej nazwie, zgodnie z konfiguracją w definicji elementu ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Element **SubjectNameingInfo** umożliwia kontrolowanie wartości podmiotu tokenu:
- **Token JWT** — wartość `sub` żądania. Jest to podmiot zabezpieczeń, w którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do wykonywania bezpiecznych kontroli autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu. Domyślnie, zgłoszenie podmiotu jest wypełniane IDENTYFIKATORem obiektu użytkownika w katalogu. Aby uzyskać więcej informacji, zobacz [Konfiguracja tokenu, sesji i logowania](active-directory-b2c-token-session-sso.md)jednokrotnego.
- **Token SAML** — `<Subject><NameID>` element, który identyfikuje element podmiotu.

Element **SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Claim | Tak | Odwołanie do **PartnerClaimType**zgłoszenia danych wyjściowych. Oświadczenia wyjściowe muszą być zdefiniowane w kolekcji **OutputClaims** zasad jednostki uzależnionej. |

Poniższy przykład pokazuje, jak zdefiniować jednostkę uzależnioną OpenID Connect Connect. Informacje o nazwie podmiotu są skonfigurowane jako `objectId`:

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
Token JWT zawiera `sub` zgłoszenie z identyfikatorem użytkownika:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


