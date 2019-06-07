---
title: Definiowanie profilu technicznego OpenId Connect w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definiowanie profilu technicznego OpenId Connect w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6d16415aa5111388ec2d2a1009ff477574ae42c5
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512916"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego OpenId Connect w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C zapewnia obsługę [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokół dostawcy tożsamości. Reprezentuje stan wiedzy w protokołach nowoczesnego uwierzytelniania OpenID Connect 1.0 i definiuje warstwę tożsamości na podstawie protokołu OAuth 2.0. Za pomocą protokołu OpenId Connect profilu technicznego może tworzyć federacje ze dostawcy OpenId Connect na podstawie tożsamości, takiego jak Azure AD. Federowanie za pomocą dostawcy tożsamości umożliwia użytkownikom Zaloguj się przy użyciu istniejącej społecznościowych lub tożsamościami w przedsiębiorstwie.

## <a name="protocol"></a>Protocol

**Nazwa** atrybutu **protokołu** element musi być równa `OpenIdConnect`. Na przykład protokół **MSA OIDC** profilu technicznego jest `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...    
```

## <a name="input-claims"></a>Oświadczeń wejściowych

**InputClaims** i **InputClaimsTransformations** elementy nie są wymagane. Ale możesz chcieć wysłać dodatkowe parametry do dostawcy tożsamości. W poniższym przykładzie dodano **domain_hint** parametr ciągu z wartością zapytania `contoso.com` żądania autoryzacji.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczeń danych wyjściowych

**OutputClaims** element zawiera listę oświadczeń zwrócona przez dostawcę tożsamości OpenId Connect. Może być konieczne mapowania nazwy oświadczenia, zdefiniowane w zasadach do nazwy zdefiniowane w dostawcy tożsamości. Możesz również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, tak długo, jak można ustawić `DefaultValue` atrybutu.

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.

Oświadczenia zwrócone przez dostawcę tożsamości Account Microsoft można znaleźć w poniższym przykładzie:

- **Sub** oświadczenia, który jest zamapowany **issuerUserId** oświadczenia.
- **Nazwa** oświadczenia, który jest zamapowany **displayName** oświadczenia.
- **E-mail** bez mapowania nazw.

Profil techniczny zwraca również wartość oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** oświadczenia, który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** oświadczenie z wartością domyślną **socialIdpAuthentication**.

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
| client_id | Yes | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli zostanie określony, usługi Azure AD B2C sprawdza, czy token jest oświadczenie zwrócona przez dostawcę tożsamości i jest taki sam, jak określona. |
| METADANE | Tak | Adres URL wskazujący dokument konfiguracji JSON formatowana zgodnie ze specyfikacją odnajdywania protokołu OpenID Connect, która jest także znana jako punkt końcowy konfiguracji openid dobrze znane. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_types | Nie | Typ odpowiedzi zgodnie ze specyfikacją protokołu OpenID Connect 1.0 Core. Możliwe wartości: `id_token`, `code`, lub `token`. |
| response_mode | Nie | Metody, która używa dostawcy tożsamości w celu wysłania wynik z powrotem do usługi Azure AD B2C. Możliwe wartości: `query`, `form_post` (ustawienie domyślne) lub `fragment`. |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie ze specyfikacją protokołu OpenID Connect 1.0 Core. Takie jak `openid`, `profile`, i `email`. |
| HttpBinding | Nie | Oczekiwano powiązanie HTTP do tokenów i oświadczeń tokenu punkty końcowe dostępu. Możliwe wartości: `GET` lub `POST`.  |
| ValidTokenIssuerPrefixes | Nie | Klucz, który może służyć do logowania się do każdego dzierżawcy, podczas korzystania z dostawcy tożsamości wielu dzierżawców, takich jak Azure Active Directory. |
| UsePolicyInRedirectUri | Nie | Wskazuje, czy należy użyć zasad, podczas tworzenia identyfikatora URI przekierowania. Po skonfigurowaniu aplikacji w dostawcy tożsamości, należy określić identyfikator URI przekierowania. Wskazuje identyfikator URI przekierowania usługi Azure AD B2C `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com może zmienić za pomocą usługi name.b2clogin.com dzierżawy).  Jeśli określisz `false`, należy dodać identyfikator URI przekierowania dla każdej zasady, możesz użyć. Na przykład: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nie | Wskazuje, czy żądanie do usługi zewnętrznej powinien być oznaczony jako błąd, jeśli kod stanu Http jest z zakresu 5xx. Wartość domyślna to `false`. |
| DiscoverMetadataByTokenIssuer | Nie | Wskazuje, czy metadane OIDC powinny zostać wykryte przy użyciu Wystawca tokenu JWT. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKeys** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz szyfrowania jest wymagany tylko wtedy, gdy **response_types** metadanych jest ustawiona na `code`. W tym przypadku usługi Azure AD B2C sprawia, że inne wywołanie wymienić kod autoryzacji do tokena dostępu. Jeśli ustawiono metadanych `id_token` można pominąć klucz kryptograficzny.  |  

## <a name="redirect-uri"></a>Identyfikator Uri przekierowania
 
Po skonfigurowaniu przekierowania URI dostawcy tożsamości, wprowadź `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Upewnij się zastąpić **dzierżawy** przy użyciu nazwy dzierżawy (na przykład contosob2c.onmicrosoft.com) lub identyfikator dzierżawy. Identyfikator URI przekierowania musi być zapisana w same małe litery.

Jeśli używasz **z usługi b2clogin.com** domeny zamiast **login.microsoftonline.com** upewnij się korzystać z usługi b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodaj konto Microsoft (MSA) jako dostawcy tożsamości za pomocą zasad niestandardowych](active-directory-b2c-custom-setup-msa-idp.md)
- [Zaloguj się przy użyciu konta usługi Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Zezwalaj użytkownikom na logowanie się do dostawcy tożsamości wielu dzierżaw usługi Azure AD za pomocą zasad niestandardowych](active-directory-b2c-setup-commonaad-custom.md)

 














