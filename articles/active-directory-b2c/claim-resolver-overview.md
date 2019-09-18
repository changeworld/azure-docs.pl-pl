---
title: Informacje o resolverach roszczeń w Azure Active Directory B2C zasadach niestandardowych | Microsoft Docs
description: Dowiedz się więcej na temat sposobu używania metod rozpoznawania oświadczeń w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f08c85cee2378f4a879daf197af7a2adf0c20f45
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064393"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informacje o rozpoznawaniu roszczeń w Azure Active Directory B2C zasadach niestandardowych

Mechanizmy rozpoznawania nazw w Azure Active Directory B2C (Azure AD B2C) [zasady niestandardowe](active-directory-b2c-overview-custom.md) zawierają informacje kontekstu dotyczące żądania autoryzacji, takie jak nazwa zasad, identyfikator korelacji żądania, język interfejsu użytkownika i inne.

Aby użyć mechanizmu rozwiązywania konfliktów w ramach oświadczenia wejściowego lub wychodzącego,należy zdefiniować element ClaimType typu String w elemencie [ClaimsSchema](claimsschema.md) , a następnie ustawić wartość **DefaultValue** na resolver oświadczenia w elemencie oświadczenia wejściowego lub wychodzącego. Azure AD B2C odczytuje wartość mechanizmu rozwiązywania konfliktów i używa wartości w profilu technicznym.

W poniższym przykładzie typ wystąpienia `correlationId` `string`o nazwie jest zdefiniowany za pomocą typu **danych** .

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

W profilu technicznym zamapuj mechanizm rozwiązywania konfliktów na typ zgłoszenia. Azure AD B2C wypełnia wartość programu rozpoznawania `{Context:CorrelationId}` roszczeń do roszczeń `correlationId` i wysyła je do profilu technicznego.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typy mechanizmu rozwiązywania konfliktów

W poniższych sekcjach znajduje się lista dostępnych elementów rozwiązywania problemów.

### <a name="culture"></a>Kultura

| Oświadczenie | Opis | Przykład |
| ----- | ----------- | --------|
| {Culture: LanguageName} | Dwuliterowy kod ISO dla języka. | pl-PL |
| {Culture: LCID}   | Identyfikator LCID kodu języka. | 1033 |
| {Culture: RegionName} | Dwuliterowy kod ISO dla regionu. | USA |
| {Culture: RFC5646} | Kod języka RFC5646. | en-US |

### <a name="policy"></a>Zasady

| Oświadczenie | Opis | Przykład |
| ----- | ----------- | --------|
| {Policy: PolicyId} | Nazwa zasad jednostki uzależnionej. | B2C_1A_signup_signin |
| {Policy: RelyingPartyTenantId} | Identyfikator dzierżawy zasad jednostki uzależnionej. | your-tenant.onmicrosoft.com |
| {Policy: TenantObjectId} | Identyfikator obiektu dzierżawy zasad jednostki uzależnionej. | 00000000-0000-0000-0000-000000000000 |
| {Policy: TrustFrameworkTenantId} | Identyfikator dzierżawy struktury zaufania. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Oświadczenie | Opis | Przykład |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |Parametr `acr_values` ciągu zapytania. | ND |
| {OIDC:ClientId} |Parametr `client_id` ciągu zapytania. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |Parametr `domain_hint` ciągu zapytania. | facebook.com |
| {OIDC: LoginHint} |  Parametr `login_hint` ciągu zapytania. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`. | ND |
| {OIDC: nonce} |Parametr `Nonce` ciągu zapytania. | defaultNonce |
| {OIDC: Prompt} | Parametr `prompt` ciągu zapytania. | logowanie |
| {OIDC: Resource} |Parametr `resource` ciągu zapytania. | ND |
| {OIDC: Scope} |Parametr `scope` ciągu zapytania. | OpenID Connect |

### <a name="context"></a>Kontekst

| Oświadczenie | Opis | Przykład |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Wersja struktury obsługi tożsamości (numer kompilacji).  | 1.0.507.0 |
| {Context: identyfikator korelacji} | Identyfikator korelacji.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Data i godzina w formacie UTC.  | 10/10/2018 12:00:00 PM |
| {Context: DeploymentMode} |Tryb wdrażania zasad.  | Produkcja |
| {Context: IPAddress} | Adres IP użytkownika. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parametry niebędące protokołem

Wszelkie nazwy parametrów dołączone jako część żądania OIDC lub OAuth2 mogą być mapowane do roszczeń w podróży użytkownika. Na przykład żądanie z aplikacji może zawierać parametr ciągu zapytania z nazwą `app_session`, `loyalty_number`lub dowolnym niestandardowym ciągiem zapytania.

| Oświadczenie | Opis | Przykład |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | Parametr ciągu zapytania. | Hawaje |
| {OAUTH-KV: app_session} | Parametr ciągu zapytania. | A3C5R |
| {OAUTH-KV: loyalty_number} | Parametr ciągu zapytania. | 1234 |
| {OAUTH-KV: dowolny niestandardowy ciąg zapytania} | Parametr ciągu zapytania. | ND |

### <a name="oauth2"></a>OAuth2

| Oświadczenie | Opis | Przykład |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Token dostępu. | ND |

## <a name="how-to-use-claim-resolvers"></a>Jak korzystać z resolverów roszczeń

### <a name="restful-technical-profile"></a>Profil techniczny RESTful

W profilu technicznym [RESTful](restful-technical-profile.md) może być konieczne wysłanie języka użytkownika, nazwy zasad, zakresu i identyfikatora klienta. Na podstawie tych oświadczeń interfejs API REST może uruchamiać niestandardową logikę biznesową i w razie potrzeby zgłaszać zlokalizowany komunikat o błędzie.

W poniższym przykładzie przedstawiono profil techniczny RESTful:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Bezpośrednie logowanie

Korzystając z resolverów roszczeń, można wstępnie wypełnić nazwę logowania lub bezpośrednio zalogować do określonego dostawcy tożsamości społecznościowej, takiego jak Facebook, LinkedIn lub konto Microsoft. Aby uzyskać więcej informacji, zobacz [Konfigurowanie bezpośredniego logowania przy użyciu Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamiczne dostosowywanie interfejsu użytkownika

Azure AD B2C umożliwia przekazywanie parametrów ciągu zapytania do punktów końcowych definicji zawartości HTML, dzięki czemu można dynamicznie renderować zawartość strony. Na przykład można zmienić obraz tła na stronie rejestracji lub logowania Azure AD B2C na podstawie parametru niestandardowego, który jest przekazywany z aplikacji sieci Web lub mobilnej. Aby uzyskać więcej informacji, zobacz [dynamiczne Konfigurowanie interfejsu użytkownika przy użyciu zasad niestandardowych w programie Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Możesz również lokalizować stronę HTML na podstawie parametru języka lub można zmienić zawartość na podstawie identyfikatora klienta.

Poniższy przykład przekazuje w ciągu zapytania parametr o nazwie **campaignId** z `hawaii`wartością, kod `en-US`języka i **aplikację** reprezentującą identyfikator klienta:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

W wyniku Azure AD B2C do strony zawartości HTML są wysyłane powyższe parametry:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Profil techniczny Application Insights

Za pomocą usługi Azure Application Insights i rozwiązania do rozwiązywania problemów możesz uzyskiwać wgląd w zachowania użytkowników. W profilu technicznym Application Insights wysyłane są oświadczenia wejściowe, które są utrwalane w usłudze Azure Application Insights. Aby uzyskać więcej informacji, zobacz [śledzenie zachowania użytkowników w Azure AD B2C podróże przy użyciu Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). Poniższy przykład wysyła identyfikator zasad, identyfikator korelacji, język i identyfikator klienta do usługi Azure Application Insights.

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
