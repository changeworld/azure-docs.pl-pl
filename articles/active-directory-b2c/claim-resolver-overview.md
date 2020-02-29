---
title: Rozwiązywanie problemów w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak używać resolverów oświadczeń w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 02277d2da2e431ac1cefdd9b018af4c25f7d5a9a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189841"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informacje o rozpoznawaniu roszczeń w Azure Active Directory B2C zasadach niestandardowych

Mechanizmy rozpoznawania nazw w Azure Active Directory B2C (Azure AD B2C) [zasady niestandardowe](custom-policy-overview.md) zawierają informacje kontekstu dotyczące żądania autoryzacji, takie jak nazwa zasad, identyfikator korelacji żądania, język interfejsu użytkownika i inne.

Aby użyć mechanizmu rozwiązywania konfliktów w ramach oświadczenia wejściowego lub wychodzącego, należy zdefiniować element **ClaimType**typu String w elemencie [ClaimsSchema](claimsschema.md) , a następnie ustawić wartość **DefaultValue** na resolver oświadczenia w elemencie oświadczenia wejściowego lub wychodzącego. Azure AD B2C odczytuje wartość mechanizmu rozwiązywania konfliktów i używa wartości w profilu technicznym.

W poniższym przykładzie typ zgłoszenia o nazwie `correlationId` jest zdefiniowany za pomocą **typu danych** `string`.

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

W profilu technicznym zamapuj mechanizm rozwiązywania konfliktów na typ zgłoszenia. Azure AD B2C wypełnia wartość `{Context:CorrelationId}` mechanizmu rozwiązywania konfliktów w `correlationId` roszczeń i wysyła je do profilu technicznego.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typy mechanizmu rozwiązywania konfliktów

W poniższych sekcjach znajduje się lista dostępnych elementów rozwiązywania problemów.

### <a name="culture"></a>Kultura

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Culture: LanguageName} | Dwuliterowy kod ISO dla języka. | pl |
| {Culture: LCID}   | Identyfikator LCID kodu języka. | 1033 |
| {Culture: RegionName} | Dwuliterowy kod ISO dla regionu. | USA |
| {Culture: RFC5646} | Kod języka RFC5646. | pl-PL |

### <a name="policy"></a>Zasady

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Policy: PolicyId} | Nazwa zasad jednostki uzależnionej. | B2C_1A_signup_signin |
| {Policy: RelyingPartyTenantId} | Identyfikator dzierżawy zasad jednostki uzależnionej. | your-tenant.onmicrosoft.com |
| {Policy: TenantObjectId} | Identyfikator obiektu dzierżawy zasad jednostki uzależnionej. | 00000000-0000-0000-0000-000000000000 |
| {Policy: TrustFrameworkTenantId} | Identyfikator dzierżawy struktury zaufania. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |`acr_values` parametr ciągu zapytania. | Nie dotyczy |
| {OIDC:ClientId} |`client_id` parametr ciągu zapytania. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |`domain_hint` parametr ciągu zapytania. | facebook.com |
| {OIDC: LoginHint} |  `login_hint` parametr ciągu zapytania. | someone@contoso.com |
| {OIDC:MaxAge} | Element `max_age`. | Nie dotyczy |
| {OIDC: nonce} |`Nonce` parametr ciągu zapytania. | defaultNonce |
| {OIDC: Prompt} | `prompt` parametr ciągu zapytania. | logowanie |
| {OIDC: Resource} |`resource` parametr ciągu zapytania. | Nie dotyczy |
| {OIDC: Scope} |`scope` parametr ciągu zapytania. | OpenID Connect |
| {OIDC: RedirectUri} |`redirect_uri` parametr ciągu zapytania. | https://jwt.ms |

### <a name="context"></a>Kontekst

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Wersja struktury obsługi tożsamości (numer kompilacji).  | 1.0.507.0 |
| {Context: identyfikator korelacji} | Identyfikator korelacji.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Data i godzina w formacie UTC.  | 10/10/2018 12:00:00 PM |
| {Context: DeploymentMode} |Tryb wdrażania zasad.  | Produkcja |
| {Context: IPAddress} | Adres IP użytkownika. | 11.111.111.11 |
| {Context: KMSI} | Wskazuje, czy pole wyboru nie [wylogowuj mnie](custom-policy-keep-me-signed-in.md) jest zaznaczone. |  true |

### <a name="non-protocol-parameters"></a>Parametry niebędące protokołem

Wszelkie nazwy parametrów dołączone jako część żądania OIDC lub OAuth2 mogą być mapowane do roszczeń w podróży użytkownika. Na przykład żądanie z aplikacji może zawierać parametr ciągu zapytania o nazwie `app_session`, `loyalty_number`lub dowolnego niestandardowego ciągu zapytania.

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | Parametr ciągu zapytania. | Hawaje |
| {OAUTH-KV: app_session} | Parametr ciągu zapytania. | A3C5R |
| {OAUTH-KV: loyalty_number} | Parametr ciągu zapytania. | 1234 |
| {OAUTH-KV: dowolny niestandardowy ciąg zapytania} | Parametr ciągu zapytania. | Nie dotyczy |

### <a name="oauth2"></a>OAuth2

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Token dostępu. | Nie dotyczy |


### <a name="saml"></a>SAML

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | Wartość `AuthnContextClassRef` elementu z żądania SAML. | urn: języka Oasis: names: TC: SAML: 2.0: AC: klasy: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | Atrybut `Format` z elementu `NameIDPolicy` żądania SAML. | urn: języka Oasis: names: TC: SAML: 1.1: NameID-format: emailAddress |
| {SAML: Issuer} |  Wartość elementu SAML `Issuer` żądania SAML.| https://contoso.com |
| {SAML: AllowCreate} | Wartość atrybutu `AllowCreate` z elementu `NameIDPolicy` żądania SAML. | True |
| {SAML: ForceAuthn} | Wartość atrybutu `ForceAuthN` z elementu `AuthnRequest` żądania SAML. | True |
| {SAML: ProviderName} | Wartość atrybutu `ProviderName` z elementu `AuthnRequest` żądania SAML.| Contoso.com |

## <a name="using-claim-resolvers"></a>Korzystanie z resolverów roszczeń

Można użyć resolverów oświadczeń z następującymi elementami:

| Element | Element | Ustawienia |
| ----- | ----------------------- | --------|
|Profil techniczny Application Insights |`InputClaim` | |
|Profil techniczny [Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [OpenID Connect Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [transformacji oświadczeń](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Profil techniczny [dostawcy RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Profil techniczny [SAML2](saml-technical-profile.md)| `OutputClaim`| 1, 2|
|Profil techniczny [z własnym potwierdzeniem](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Profil techniczny [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Ustawienia:
1. Metadane `IncludeClaimResolvingInClaimsHandling` muszą być ustawione na `true`.
1. `AlwaysUseDefaultValue` atrybutu oświadczeń wejściowych lub wyjściowych musi być ustawiona na `true`.

## <a name="claim-resolvers-samples"></a>Przykłady elementów rozpoznawania nazw

### <a name="restful-technical-profile"></a>Profil techniczny RESTful

W profilu technicznym [RESTful](restful-technical-profile.md) może być konieczne wysłanie języka użytkownika, nazwy zasad, zakresu i identyfikatora klienta. Na podstawie tych oświadczeń interfejs API REST może uruchamiać niestandardową logikę biznesową i w razie potrzeby zgłaszać zlokalizowany komunikat o błędzie.

W poniższym przykładzie przedstawiono profil techniczny RESTful w tym scenariuszu:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Bezpośrednie logowanie

Korzystając z resolverów roszczeń, można wstępnie wypełnić nazwę logowania lub bezpośrednio zalogować do określonego dostawcy tożsamości społecznościowej, takiego jak Facebook, LinkedIn lub konto Microsoft. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamiczne dostosowywanie interfejsu użytkownika

Azure AD B2C umożliwia przekazywanie parametrów ciągu zapytania do punktów końcowych definicji zawartości HTML w celu dynamicznego renderowania zawartości strony. Na przykład pozwala to na modyfikowanie obrazu tła na stronie rejestracji i logowania na Azure AD B2C na podstawie parametru niestandardowego, który jest przekazywany z aplikacji sieci Web lub mobilnej. Aby uzyskać więcej informacji, zobacz [dynamiczne Konfigurowanie interfejsu użytkownika przy użyciu zasad niestandardowych w programie Azure Active Directory B2C](custom-policy-ui-customization.md). Możesz również lokalizować stronę HTML na podstawie parametru języka lub można zmienić zawartość na podstawie identyfikatora klienta.

Poniższy przykład przekazuje parametr ciągu zapytania o nazwie **campaignId** wartością `hawaii`, kod **języka** `en-US`i **aplikację** reprezentującą identyfikator klienta:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

W związku z tym Azure AD B2C wysyła powyższe parametry do strony zawartości HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definicja zawartości

W `LoadUri`[ContentDefinition](contentdefinitions.md) można wysyłać resolvery roszczeń w celu ściągania zawartości z różnych miejsc, na podstawie używanych parametrów.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Profil techniczny Application Insights

Za pomocą usługi Azure Application Insights i rozwiązania do rozwiązywania problemów możesz uzyskiwać wgląd w zachowania użytkowników. W profilu technicznym Application Insights wysyłane są oświadczenia wejściowe, które są utrwalane w usłudze Azure Application Insights. Aby uzyskać więcej informacji, zobacz [śledzenie zachowania użytkowników w Azure AD B2C podróże przy użyciu Application Insights](analytics-with-application-insights.md). Poniższy przykład wysyła identyfikator zasad, identyfikator korelacji, język i identyfikator klienta do usługi Azure Application Insights.

```XML
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Zasady jednostki uzależnionej

W profilu technicznym zasad [jednostki uzależnionej](relyingparty.md) możesz chcieć wysłać identyfikator dzierżawy lub identyfikator korelacji do aplikacji jednostki uzależnionej w ramach tokenu JWT.

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
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
