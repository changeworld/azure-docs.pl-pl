---
title: Definiowanie profilu technicznego OpenID Connect Connect w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fde27c468b6b5285e9e98f10a5fd04ee53c3c775
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198007"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny OpenID Connect Connect w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości protokołu [OpenID Connect Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) . OpenID Connect Connect 1,0 definiuje warstwę tożsamości na serwerze OAuth 2,0 i reprezentuje stan grafiki w nowoczesnych protokołach uwierzytelniania. Za pomocą profilu technicznego OpenID Connect Connect można sfederować przy użyciu dostawcy tożsamości usługi OpenID Connect Connect, takiego jak Azure AD. Federowanie z dostawcą tożsamości umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi mieć wartość `OpenIdConnect`. Na przykład protokół dla profilu technicznego **MSA-OIDC** jest `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

Elementy **InputClaims** i **InputClaimsTransformations** nie są wymagane. Ale możesz chcieć wysłać dodatkowe parametry do dostawcy tożsamości. Poniższy przykład dodaje parametr **domain_hint** ciągu zapytania z wartością `contoso.com` do żądania autoryzacji.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości programu OpenID Connect Connect. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, o ile atrybut `DefaultValue` jest ustawiony.

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

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_id | Yes | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli ta wartość jest określona, Azure AD B2C sprawdza, czy token znajduje się w ramach żądania zwróconego przez dostawcę tożsamości i jest równy określonemu. |
| METADANE | Yes | Adres URL wskazujący na dokument konfiguracji JSON sformatowany zgodnie ze specyfikacją odnajdywania OpenID Connect Connect, który jest również znany jako dobrze znany punkt końcowy konfiguracji OpenID Connect. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_types | Nie | Typ odpowiedzi zgodnie z specyfikacją OpenID Connect Connect Core 1,0. Możliwe wartości: `id_token`, `code`lub `token`. |
| response_mode | Nie | Metoda wykorzystywana przez dostawcę tożsamości do wysyłania wyniku z powrotem do Azure AD B2C. Możliwe wartości: `query`, `form_post` (wartość domyślna) lub `fragment`. |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie z specyfikacją OpenID Connect Connect Core 1,0. Takie jak `openid`, `profile`i `email`. |
| HttpBinding | Nie | Oczekiwano powiązania HTTP z punktami końcowymi tokenu dostępu i tokenów oświadczeń. Możliwe wartości: `GET` lub `POST`.  |
| ValidTokenIssuerPrefixes | Nie | Klucz, którego można użyć do zalogowania się do poszczególnych dzierżawców w przypadku korzystania z dostawcy tożsamości z wieloma dzierżawcami, takiego jak Azure Active Directory. |
| UsePolicyInRedirectUri | Nie | Wskazuje, czy należy używać zasad podczas konstruowania identyfikatora URI przekierowania. Podczas konfigurowania aplikacji w dostawcy tożsamości należy określić identyfikator URI przekierowania. Identyfikator URI przekierowania wskazuje Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  W przypadku określenia `false`należy dodać identyfikator URI przekierowania dla każdej używanej zasady. Na przykład: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nie | Wskazuje, czy żądanie do usługi zewnętrznej powinno być oznaczone jako błąd, jeśli kod stanu HTTP znajduje się w zakresie 5xx. Wartość domyślna to `false`. |
| DiscoverMetadataByTokenIssuer | Nie | Wskazuje, czy metadane OIDC powinny być odnajdywane przy użyciu wystawcy w tokenie JWT. |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy w profilu technicznym znajduje się [rozpoznawanie oświadczeń](claim-resolver-overview.md) . Możliwe wartości: `true`lub `false` (wartość domyślna). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw tę opcję na `true`. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_secret | Yes | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz kryptograficzny jest wymagany tylko wtedy, gdy metadane **response_types** są ustawione na `code`. W takim przypadku Azure AD B2C wykonuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są ustawione na `id_token` można pominąć klucz kryptograficzny.  |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania identyfikatora URI przekierowania dostawcy tożsamości wprowadź `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Pamiętaj, aby zastąpić `{your-tenant-name}` nazwą swojej dzierżawy. Identyfikator URI przekierowania musi zawierać tylko małe litery.

Przykłady:

- [Dodawanie konta Microsoft (MSA) jako dostawcy tożsamości przy użyciu zasad niestandardowych](identity-provider-microsoft-account-custom.md)
- [Logowanie przy użyciu kont usługi Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Zezwalaj użytkownikom na logowanie się do wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad niestandardowych](identity-provider-azure-ad-multi-tenant-custom.md)
