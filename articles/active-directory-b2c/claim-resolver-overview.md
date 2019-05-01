---
title: Około oświadczenia rozpoznawania nazw w zasadach niestandardowych usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o rozwiązujący oświadczeń używanych w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1f4bb5deb0c30ba113f315c69393e92bddfe0d27
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703472"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Temat rozwiązujący oświadczenia w zasadach niestandardowych usługi Azure Active Directory B2C

Oświadczenia rozpoznawania nazw w usłudze Azure Active Directory (Azure AD) B2C [zasady niestandardowe](active-directory-b2c-overview-custom.md) zapewnia informacje kontekstowe o żądanie autoryzacji, takich jak nazwa zasad, identyfikator korelacji żądania, język interfejsu użytkownika i.

Aby użyć oświadczeń program rozpoznawania nazw w oświadczenie wejściowe i wyjściowe, należy zdefiniować ciąg **oświadczenia**w obszarze [ClaimsSchema](claimsschema.md) elementu, a następnie ustaw **DefaultValue** do oświadczeń Program rozpoznawania nazw w danych wejściowych lub wyjściowych oświadczeń elementu. Usługa Azure AD B2C odczytuje wartość oświadczenia programu rozpoznawania nazw i używa wartości w profilu technicznym. 

W poniższym przykładzie typ oświadczenia o nazwie `correlationId` jest zdefiniowana za pomocą **DataType** z `string`.  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

W profilu technicznym rozpoznawania oświadczenia są mapowane na typ oświadczenia. Usługa Azure AD B2C, wypełnia wartości oświadczeń programu rozpoznawania nazw `{Context:CorrelationId}` w oświadczenie `correlationId` i wysyła oświadczenie do profilu technicznego.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Mechanizm rozpoznawania typów oświadczeń

W poniższych sekcjach wymieniono dostępne oświadczenia rozpoznawania nazw.

### <a name="culture"></a>Kultura

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Culture:LanguageName} | Dwuliterowa kod ISO dla języka. | pl |
| {Kultury: LCID}   | Identyfikator LCID kod języka. | 21 |
| {Kultury: RegionName} | Dwuliterowa kod ISO dla regionu. | USA |
| {Culture:RFC5646} | Kod języka RFC5646. | en-US |

### <a name="policy"></a>Zasady

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Nazwa jednostki uzależnionej zasad. | B2C_1A_signup_signin |
| {Zasad: RelyingPartyTenantId} | Identyfikator dzierżawy jednostki uzależnionej zasad firmy. | Twój tenant.onmicrosoft.com |
| {Zasad: TenantObjectId} | Identyfikator obiektu dzierżawy jednostki uzależnionej zasad firmy. | 00000000-0000-0000-0000-000000000000 |
| {Zasad: TrustFrameworkTenantId} | Identyfikator dzierżawy w ramach relacji zaufania. | Twój tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` Parametr ciągu zapytania. | ND |
| {OIDC:ClientId} |`client_id` Parametr ciągu zapytania. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` Parametr ciągu zapytania. | facebook.com |
| {OIDC:LoginHint} |  `login_hint` Parametr ciągu zapytania. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`. | ND |
| {OIDC:Nonce} |`Nonce` Parametr ciągu zapytania. | defaultNonce |
| {OIDC:Prompt} | `prompt` Parametr ciągu zapytania. | logowanie |
| {OIDC:Resource} |`resource` Parametr ciągu zapytania. | ND |
| {OIDC:scope} |`scope` Parametr ciągu zapytania. | openid |

### <a name="context"></a>Kontekst

| Claim | Opis | Przykład |
| ----- | ----------- | --------|
| {Kontekstu: BuildNumber} | Wersja platformy środowiska tożsamości (numer kompilacji).  | 1.0.507.0 |
| {Context:CorrelationId} | Identyfikator korelacji.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Data Godzina w formacie UTC.  | 10 10 2018 12:00:00 PM |
| {Kontekstu: DeploymentMode} |Tryb wdrożenia zasad.  | Produkcja |
| {Kontekstu: IPAddress} | Adres IP użytkownika. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parametry non-protocol

Nazwa parametru dołączone jako część żądania OIDC lub OAuth2 mogą zostać zmapowane do oświadczeń w podróży użytkownika. Na przykład żądania od aplikacji może zawierać parametr ciągu zapytania o nazwie `app_session`, `loyalty_number`, lub dowolnego niestandardowego ciągu zapytania.

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Parametr ciągu zapytania. | Hawaje |
| {OAUTH-KV:app_session} | Parametr ciągu zapytania. | A3C5R |
| {OAUTH-KV:loyalty_number} | Parametr ciągu zapytania. | 1234 |
| {OAUTH KV: dowolny ciąg zapytania niestandardowego} | Parametr ciągu zapytania. | ND |

### <a name="oauth2"></a>OAuth2

| Claim | Opis | Przykład |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Token dostępu. | ND |

## <a name="how-to-use-claim-resolvers"></a>Jak używać oświadczeń rozpoznawania nazw

### <a name="restful-technical-profile"></a>Profil architektury REST Technical Preview

W [zgodne ze specyfikacją REST](restful-technical-profile.md) profilu technicznego, możesz chcieć wysłać języka użytkownika, nazwę zasad, zakresu i identyfikator klienta. Na podstawie tych oświadczeń z interfejsu API REST można uruchomić niestandardowej logiki biznesowej i w razie potrzeby należy zgłosić zlokalizowany komunikat. 

Poniższy przykład przedstawia RESTful profilu technicznego:

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

Używanie elementów rozpoznawania oświadczenia, można wypełnić wstępnie nazwą logowania lub bezpośrednie logowanie do określonych społecznościowego dostawcy tożsamości, takich jak Facebook, LinkedIn lub konta Microsoft. Aby uzyskać więcej informacji, zobacz [skonfigurować bezpośrednie logowanie przy użyciu usługi Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamiczne Dostosowywanie interfejsu użytkownika

Usługa Azure AD B2C umożliwia przekazywanie parametrów ciągu zapytania do punktów końcowych definicji zawartości HTML, dzięki czemu mogą powodować dynamiczne renderowanie zawartości strony. Na przykład możesz zmienić obraz tła na stronie tworzenia konta lub logowania usługi Azure AD B2C na podstawie parametru niestandardowego, które przechodzą z sieci web lub aplikacji mobilnej. Aby uzyskać więcej informacji, zobacz [dynamicznego konfigurowania interfejsu użytkownika za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Możesz również dostosować stronę HTML, na podstawie parametru języka lub zmienić zawartość, w oparciu o identyfikator klienta.

Poniższy przykład przekazuje w ciągu zapytania parametr o nazwie **campaignId** o wartości `hawaii`, **języka** kod `en-US`, i **aplikacji** reprezentuje identyfikator klienta:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

W rezultacie usługi Azure AD B2C wysyła powyższych parametrów do zawartości strony HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Profil usługi Application Insights Technical Preview

Za pomocą usługi Azure Application Insights i rozpoznawania nazw oświadczeń można uzyskiwanie szczegółowych informacji dotyczących zachowania użytkowników. W profilu technicznym usługi Application Insights wysyła do usługi Azure Application Insights oświadczeń wejściowych, które są zachowywane. Aby uzyskać więcej informacji, zobacz [podróży śledzenie zachowania użytkowników w usłudze Azure AD B2C przy użyciu usługi Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). Poniższy przykład wysyła identyfikator zasad, identyfikator korelacji, języka i identyfikator klienta do usługi Azure Application Insights.

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
