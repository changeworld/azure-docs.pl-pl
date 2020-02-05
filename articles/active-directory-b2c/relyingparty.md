---
title: RelyingParty — Azure Active Directory B2C | Microsoft Docs
description: Określ element RelyingParty zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7659c8187f7f4763b51b09362c94dad9554ed1c0
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982844"
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

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Domyślna podróż użytkownika dla aplikacji RP. |
| UserJourneyBehaviors | 0:1 | Zakres zachowań podróży użytkownika. |
| TechnicalProfile | 1:1 | Profil techniczny obsługiwany przez aplikację RP. Profil techniczny zawiera kontrakt dla aplikacji RP do kontaktu Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Element `DefaultUserJourney` określa odwołanie do identyfikatora podróży użytkownika, która jest zwykle zdefiniowana w zasadach podstawowych lub zasad rozszerzenia. W poniższych przykładach przedstawiono drogę rejestracji lub logowania użytkownika określoną w elemencie **RelyingParty** :

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
| ReferenceId | Tak | Identyfikator podróży użytkownika w zasadach. Aby uzyskać więcej informacji, zobacz [podróże użytkownika](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Element **UserJourneyBehaviors** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Zakres działania dotyczącego logowania jednokrotnego (SSO) w trakcie podróży użytkownika. |
| SessionExpiryType |0:1 | Zachowanie podczas uwierzytelniania sesji. Możliwe wartości: `Rolling` lub `Absolute`. Wartość `Rolling` (domyślnie) wskazuje, że użytkownik pozostanie zalogowany, o ile użytkownik jest stale aktywny w aplikacji. Wartość `Absolute` wskazuje, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie czasu określonego przez okres istnienia sesji aplikacji. |
| SessionExpiryInSeconds | 0:1 | Okres istnienia pliku cookie sesji B2C's usługi Azure AD został określony jako liczba całkowita przechowywana w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. |
| JourneyInsights | 0:1 | Klucz Instrumentacji usługi Azure Application Insights, który ma być używany. |
| ContentDefinitionParameters | 0:1 | Lista par wartości klucza do dołączenia do identyfikatora URI ładowania definicji zawartości. |

### <a name="singlesignon"></a>SingleSignOn

Element **SingleSignon** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Zakres | Tak | Zakres zachowania logowania jednokrotnego. Możliwe wartości: `Suppressed`, `Tenant`, `Application`lub `Policy`. Wartość `Suppressed` wskazuje, że zachowanie jest pomijane. Na przykład w przypadku sesji logowania jednokrotnego dla użytkownika nie jest utrzymywana żadna sesja, a użytkownik jest zawsze monitowany o wybranie dostawcy tożsamości. Wartość `TrustFramework` wskazuje, że zachowanie jest stosowane dla wszystkich zasad w strukturze zaufania. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla struktury zaufania nie jest monitowany o wybranie dostawcy tożsamości. Wartość `Tenant` wskazuje, że zachowanie jest stosowane do wszystkich zasad w dzierżawie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla dzierżawy nie jest monitowany o wybranie dostawcy tożsamości. Wartość `Application` wskazuje, że zachowanie jest stosowane do wszystkich zasad dla aplikacji wysyłającej żądanie. Na przykład użytkownik przechodzenia przez dwie przejazdy zasad dla aplikacji nie jest monitowany o wybranie dostawcy tożsamości. Wartość `Policy` wskazuje, że zachowanie dotyczy tylko zasad. Na przykład użytkownik przechodzący przez dwie przedziały zasad dla struktury zaufania jest monitowany o wybranie dostawcy tożsamości podczas przełączania między zasadami. |
| KeepAliveInDays | Tak | Controls how long the user remains signed in. Setting the value to 0 turns off KMSI functionality. For more information, see [Keep me signed in](custom-policy-keep-me-signed-in.md). |
|EnforceIdTokenHintOnLogout| Nie|  Force to pass a previously issued ID token to the logout endpoint as a hint about the end user's current authenticated session with the client. Possible values: `false` (default), or `true`. For more infomation, see [Web sign-in with OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

The **JourneyInsights** element contains the following attributes:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TelemetryEngine | Tak | The value must be `ApplicationInsights`. |
| InstrumentationKey | Tak | The string that contains the instrumentation key for the application insights element. |
| DeveloperMode | Tak | Possible values: `true` or `false`. If `true`, Application Insights expedites the telemetry through the processing pipeline. This setting is good for development, but constrained at high volumes The detailed activity logs are designed only to aid in development of custom policies. Do not use development mode in production. Logs collect all claims sent to and from the identity providers during development. If used in production, the developer assumes responsibility for PII (Privately Identifiable Information) collected in the App Insights log that they own. These detailed logs are only collected when this value is set to `true`.|
| ClientEnabled | Tak | Possible values: `true` or `false`. If `true`, sends the Application Insights client-side script for tracking page view and client-side errors. |
| ServerEnabled | Tak | Possible values: `true` or `false`. If `true`, sends the existing UserJourneyRecorder JSON as a custom event to Application Insights. |
| TelemetryVersion | Tak | The value must be `1.0.0`. |

For more information, see [Collecting Logs](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

By using custom policies in Azure AD B2C, you can send a parameter in a query string. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Azure AD B2C passes the query string parameters to your dynamic HTML file, such as aspx file.

The following example passes a parameter named `campaignId` with a value of `hawaii` in the query string:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

The **ContentDefinitionParameters** element contains the following element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | A string that contains the key value pair that's appended to the query string of a content definition load URI. |

The **ContentDefinitionParameter** element contains the following attribute:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | The name of the key value pair. |

For more information, see [Configure the UI with dynamic content by using custom policies](custom-policy-ui-customization-dynamic.md)

## <a name="technicalprofile"></a>TechnicalProfile

The **TechnicalProfile** element contains the following attribute:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | The value must be `PolicyProfile`. |

The **TechnicalProfile** contains the following elements:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | The string that contains the name of the technical profile. |
| Opis | 0:1 | The string that contains the description of the technical profile. |
| Protocol (Protokół) | 1:1 | The protocol used for the federation. |
| Metadane | 0:1 | The collection of *Item* of key/value pairs utilized by the protocol for communicating with the endpoint in the course of a transaction to configure interaction between the relying party and other community participants. |
| OutputClaims | 1:1 | A list of claim types that are taken as output in the technical profile. Each of these elements contains reference to a **ClaimType** already defined in the **ClaimsSchema** section or in a policy from which this policy file inherits. |
| SubjectNamingInfo | 1:1 | The subject name used in tokens. |

The **Protocol** element contains the following attribute:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Nazwa | Tak | The name of a valid protocol supported by Azure AD B2C that is used as part of the technical profile. Possible values: `OpenIdConnect` or `SAML2`. The `OpenIdConnect` value represents the OpenID Connect 1.0 protocol standard as per OpenID foundation specification. The `SAML2` represents the SAML 2.0 protocol standard as per OASIS specification. Nie używaj tokenu SAML w środowisku produkcyjnym. |

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie outputclaim | 0: n | Nazwa oczekiwanego typu na liście obsługiwanych zasad, dla których zasubskrybuje się Jednostka uzależniona. To zgłoszenie służy jako dane wyjściowe profilu technicznego. |

Element **oświadczenie outputclaim** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do elementu **ClaimType** zdefiniowanego już w sekcji **ClaimsSchema** w pliku zasad. |
| DefaultValue | Nie | Wartość domyślna, która może być używana, jeśli wartość jest pusta. |
| PartnerClaimType | Nie | Wysyła oświadczenia w innej nazwie, zgodnie z konfiguracją w definicji elementu ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Element **SubjectNameingInfo** umożliwia kontrolowanie wartości podmiotu tokenu:
- **Token JWT** — `sub`. Jest to podmiot zabezpieczeń, w którym token potwierdza informacje, takie jak użytkownik aplikacji. Ta wartość jest niezmienna i nie można jej ponownie przypisać ani ponownie użyć. Może służyć do wykonywania bezpiecznych kontroli autoryzacji, na przykład gdy token jest używany w celu uzyskania dostępu do zasobu. Domyślnie, zgłoszenie podmiotu jest wypełniane IDENTYFIKATORem obiektu użytkownika w katalogu. Aby uzyskać więcej informacji, zobacz [Konfiguracja tokenu, sesji i logowania](session-behavior.md)jednokrotnego.
- **Token SAML** — element `<Subject><NameID>`, który identyfikuje element podmiotu.

Element **SubjectNamingInfo** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Claim | Tak | Odwołanie do **PartnerClaimType**zgłoszenia danych wyjściowych. Oświadczenia wyjściowe muszą być zdefiniowane w kolekcji **OutputClaims** zasad jednostki uzależnionej. |

Poniższy przykład pokazuje, jak zdefiniować jednostkę uzależnioną OpenID Connect Connect. Informacje o nazwie podmiotu są konfigurowane jako `objectId`:

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
Token JWT zawiera `sub`ego z identyfikatorem użytkownika:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
