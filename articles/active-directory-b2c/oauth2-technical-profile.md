---
title: Definiowanie profilu technicznego OAuth2 w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować profil techniczny OAuth2.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 65634cb6573a0f163f7a0ca07bf26af9512e240b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683887"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego OAuth2 w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C umożliwia dostawcy tożsamości protokołu OAuth2. OAuth2 jest podstawowy protokół autoryzacji i uwierzytelnianie delegowane. Aby uzyskać więcej informacji, zobacz [RFC 6749 OAuth 2.0 autoryzacji Framework](https://tools.ietf.org/html/rfc6749). Z profilu technicznego OAuth2 może tworzyć federacje ze dostawcę tożsamości na podstawie protokołu OAuth2, takie jak Facebook. Federowanie za pomocą dostawcy tożsamości umożliwia użytkownikom Zaloguj się przy użyciu istniejącej społecznościowych lub tożsamościami w przedsiębiorstwie.

## <a name="protocol"></a>Protokół

**Nazwa** atrybutu **protokołu** element musi być równa `OAuth2`. Na przykład protokół **uwierzytelniania Facebook OAUTH** profilu technicznego jest `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
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

**OutputClaims** element zawiera listę oświadczeń zwrócona przez dostawcę tożsamości protokołu OAuth2. Może być konieczne mapowania nazwy oświadczenia, zdefiniowane w zasadach do nazwy zdefiniowane w dostawcy tożsamości. Mogą również obejmować oświadczenia, które nie są zwracane przez dostawcę tożsamości, tak długo, jak można ustawić `DefaultValue` atrybutu.

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.

Poniższy przykład przedstawia oświadczenia zwrócona przez dostawcę tożsamości usługi Facebook:

- **Imię** oświadczeń jest mapowany na **givenName** oświadczenia.
- **Nazwisko** oświadczeń jest mapowany na **nazwisko** oświadczenia.
- **DisplayName** oświadczenia bez mapowania nazw.
- **E-mail** oświadczenia bez mapowania nazw.

Profil techniczny zwraca również wartość oświadczenia, które nie są zwracane przez dostawcę tożsamości: 

- **IdentityProvider** oświadczenia, który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** oświadczenie z wartością domyślną **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_id | Yes | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli zostanie określony, usługi Azure AD B2C sprawdza, czy token jest oświadczenie zwrócona przez dostawcę tożsamości i jest taki sam, jak określona. |
| authorization_endpoint | Yes | Adres URL punktu końcowego autoryzacji, zgodnie z RFC 6749. |
| AccessTokenEndpoint | Yes | Adres URL punktu końcowego tokenu zgodnie z RFC 6749. |  
| ClaimsEndpoint | Yes | Adres URL punktu końcowego informacji użytkownika zgodnie z RFC 6749. | 
| AccessTokenResponseFormat | Nie | Format wywołanie punktu końcowego tokenu dostępu. Na przykład Facebook wymaga metody HTTP GET, ale odpowiedzi tokenu dostępu jest w formacie JSON. |
| AdditionalRequestQueryParameters | Nie | Żądanie dodatkowe parametry zapytania. Na przykład można wysłać dodatkowe parametry do dostawcy tożsamości. Może zawierać wiele parametrów przy użyciu ogranicznika przecinkami. | 
| ClaimsEndpointAccessTokenName | Nie | Nazwa parametru ciągu zapytania tokenu dostępu. Niektórzy dostawcy tożsamości oświadczeń z punktami końcowymi obsługuje żądania GET HTTP. W tym przypadku tokenu elementu nośnego, są wysyłane przy użyciu parametru ciągu zapytania zamiast nagłówka autoryzacji. |
| ClaimsEndpointFormatName | Nie | Nazwa parametru ciągu zapytania w formacie. Na przykład można ustawić nazwę jako `format` w tym usługi LinkedIn oświadczeń punktu końcowego `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Nie | Wartość parametru ciągu zapytania w formacie. Na przykład można ustawić wartość jako `json` w tym usługi LinkedIn oświadczeń punktu końcowego `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_mode | Nie | Metody, która używa dostawcy tożsamości w celu wysłania wynik z powrotem do usługi Azure AD B2C. Możliwe wartości: `query`, `form_post` (ustawienie domyślne) lub `fragment`. |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie ze specyfikacją protokołu OAuth2 dostawcy tożsamości. Takie jak `openid`, `profile`, i `email`. |
| HttpBinding | Nie | Oczekiwano powiązanie HTTP do tokenów i oświadczeń tokenu punkty końcowe dostępu. Możliwe wartości: `GET` lub `POST`.  |
| ResponseErrorCodeParamName | Nie | Nazwa parametru, który zawiera komunikat o błędzie zwracany za pośrednictwem protokołu HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Nie | Zawiera dodatkowe parametry, które mogą zostać zwrócone w odpowiedzi z **AccessTokenEndpoint** przez niektórych dostawców tożsamości. Na przykład odpowiedź z **AccessTokenEndpoint** zawiera dodatkowy parametr, takie jak `openid`, który jest to parametr obowiązkowy, oprócz access_token w **ClaimsEndpoint** żądania zapytania ciąg. Wiele nazw parametr powinien być poprzedzone znakiem zmiany znaczenia i rozdzielonych przecinkami ',' ogranicznik. |
| ExtraParamsInClaimsEndpointRequest | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w **ClaimsEndpoint** żądania przez niektórych dostawców tożsamości. Wiele nazw parametr powinien być poprzedzone znakiem zmiany znaczenia i rozdzielonych przecinkami ',' ogranicznik. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKeys** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Yes | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz szyfrowania jest wymagany tylko wtedy, gdy **response_types** metadanych jest ustawiona na `code`. W tym przypadku usługi Azure AD B2C sprawia, że inne wywołanie wymienić kod autoryzacji do tokena dostępu. Jeśli ustawiono metadanych `id_token`, można pominąć klucz kryptograficzny. |  

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Po skonfigurowaniu adres URL przekierowania dostawcy tożsamości, wprowadź `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Upewnij się zastąpić **dzierżawy** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com) i **policyId** o identyfikatorze zasady (na przykład b2c_1a_policy). Identyfikator URI przekierowania musi być zapisana w same małe litery.

Jeśli używasz **z usługi b2clogin.com** domeny zamiast **login.microsoftonline.com** upewnij się korzystać z usługi b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodaj Google + jako dostawcy tożsamości OAuth2 za pomocą zasad niestandardowych](active-directory-b2c-custom-setup-goog-idp.md)













