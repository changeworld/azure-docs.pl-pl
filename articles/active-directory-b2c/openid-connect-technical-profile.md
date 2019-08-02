---
title: Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1e8f03b17c5e8ea68affa9fe83875382fd5d8512
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716716"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C zapewnia obsługę dostawcy tożsamości protokołu [OpenID Connect Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect Connect 1,0 definiuje warstwę tożsamości na serwerze OAuth 2,0 i reprezentuje stan grafiki w nowoczesnych protokołach uwierzytelniania. Za pomocą profilu technicznego OpenID Connect Connect można sfederować przy użyciu dostawcy tożsamości usługi OpenID Connect Connect, takiego jak Azure AD. Federowanie z dostawcą tożsamości umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="protocol"></a>Protocol

Atrybut **name** elementu **Protocol** musi być ustawiony na `OpenIdConnect`. Na przykład protokół dla profilu `OpenIdConnect`technicznego **MSA-OIDC** :

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Elementy **InputClaims** i **InputClaimsTransformations** nie są wymagane. Ale możesz chcieć wysłać dodatkowe parametry do dostawcy tożsamości. Poniższy przykład dodaje parametr ciągu zapytania **domain_hint** z wartością `contoso.com` do żądania autoryzacji.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości programu OpenID Connect Connect. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, o ile `DefaultValue` atrybut ten jest ustawiony.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości konta Microsoft:

- Zastrzeżenie **podrzędne** , które jest mapowane na **issuerUserId** .
- **Nazwa** , która jest mapowana do żądania **DisplayName** .
- **Adres e-mail** bez mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** z wartością domyślną **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli ta wartość jest określona, Azure AD B2C sprawdza, czy token znajduje się w ramach żądania zwróconego przez dostawcę tożsamości i jest równy określonemu. |
| METADANE | Tak | Adres URL wskazujący na dokument konfiguracji JSON sformatowany zgodnie ze specyfikacją odnajdywania OpenID Connect Connect, który jest również znany jako dobrze znany punkt końcowy konfiguracji OpenID Connect. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_types | Nie | Typ odpowiedzi zgodnie z specyfikacją OpenID Connect Connect Core 1,0. Możliwe wartości: `id_token`, `code`, lub `token`. |
| response_mode | Nie | Metoda wykorzystywana przez dostawcę tożsamości do wysyłania wyniku z powrotem do Azure AD B2C. Możliwe wartości: `query`, `form_post` (wartość domyślna), `fragment`lub. |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie z specyfikacją OpenID Connect Connect Core 1,0. Takie jak `openid`, `profile`i. `email` |
| HttpBinding | Nie | Oczekiwano powiązania HTTP z punktami końcowymi tokenu dostępu i tokenów oświadczeń. Możliwe wartości: `GET` lub `POST`.  |
| ValidTokenIssuerPrefixes | Nie | Klucz, którego można użyć do zalogowania się do poszczególnych dzierżawców w przypadku korzystania z dostawcy tożsamości z wieloma dzierżawcami, takiego jak Azure Active Directory. |
| UsePolicyInRedirectUri | Nie | Wskazuje, czy należy używać zasad podczas konstruowania identyfikatora URI przekierowania. Podczas konfigurowania aplikacji w dostawcy tożsamości należy określić identyfikator URI przekierowania. Identyfikator URI przekierowania wskazuje na Azure AD B2C `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` , (login.microsoftonline.com może ulec zmianie z Your-tenant-Name.b2clogin.com).  W przypadku określenia `false`tego elementu należy dodać identyfikator URI przekierowania dla każdej używanej zasady. Na przykład: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nie | Wskazuje, czy żądanie do usługi zewnętrznej powinno być oznaczone jako błąd, jeśli kod stanu HTTP znajduje się w zakresie 5xx. Wartość domyślna to `false`. |
| DiscoverMetadataByTokenIssuer | Nie | Wskazuje, czy metadane OIDC powinny być odnajdywane przy użyciu wystawcy w tokenie JWT. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz kryptograficzny jest wymagany tylko wtedy, gdy metadane **response_types** są ustawione na `code`. W takim przypadku Azure AD B2C wykonuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są ustawione na `id_token` wartość można pominąć klucz kryptograficzny.  |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania identyfikatora URI przekierowania dostawcy tożsamości wprowadź `https://login.microsoftonline.com/te/tenant/oauth2/authresp`wartość. Pamiętaj o zamianie **dzierżawy** na nazwę dzierżawy (na przykład contosob2c.onmicrosoft.com) lub identyfikator dzierżawy. Identyfikator URI przekierowania musi zawierać tylko małe litery.

Jeśli używasz domeny **b2clogin.com** zamiast **login.microsoftonline.com** upewnij się, że używasz b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodawanie konta Microsoft (MSA) jako dostawcy tożsamości przy użyciu zasad niestandardowych](active-directory-b2c-custom-setup-msa-idp.md)
- [Logowanie przy użyciu kont usługi Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Zezwalaj użytkownikom na logowanie się do wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad niestandardowych](active-directory-b2c-setup-commonaad-custom.md)

 














