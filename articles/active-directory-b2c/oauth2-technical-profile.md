---
title: Definiowanie profilu technicznego OAuth2 w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 86ec7a5745a58546faf6f0ff15d6dc5f452baa88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184047"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego OAuth2 w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości protokołu OAuth2. OAuth2 jest podstawowym protokołem autoryzacji i uwierzytelniania delegowanego. Aby uzyskać więcej informacji, zobacz [RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). Dzięki profilowi technicznemu OAuth2 możesz ujednać informacje z dostawcą tożsamości opartym na UAuth2, takim jak Facebook. Federacja u dostawcy tożsamości umożliwia użytkownikom logowanie się przy istniejących tożsamościach społecznościowych lub korporacyjnych.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `OAuth2`na . Na przykład protokół profilu technicznego **Facebook-OAUTH** to: `OAuth2`

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** i **InputClaimsTransformations** elementy nie są wymagane. Ale może chcesz wysłać dodatkowe parametry do dostawcy tożsamości. Poniższy przykład dodaje **parametr** domain_hint ciąg kwerendy `contoso.com` o wartości do żądania autoryzacji.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń zwróconych przez dostawcę tożsamości OAuth2. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również dołączyć oświadczenia, które nie są zwracane przez `DefaultValue` dostawcę tożsamości, tak długo, jak ustawić atrybut.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości Facebooka:

- Oświadczenie **first_name** jest mapowane na **oświadczenie givenName.**
- Oświadczenie **last_name** jest odwzorowane na oświadczenie o **nazwisku.**
- Oświadczenie **displayName** bez mapowania nazw.
- Oświadczenie **e-mail** bez mapowania nazw.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **Oświadczenie identityProvider,** który zawiera nazwę dostawcy tożsamości.
- Oświadczenie **authenticationSource** z domyślną wartością **socialIdpAuthentication**.

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

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience (IdTokenAudience) | Nie | Publiczność id_token. Jeśli określono, usługi Azure AD B2C sprawdza, czy token jest w oświadczeniu zwrócone przez dostawcę tożsamości i jest równa określonej. |
| authorization_endpoint | Tak | Adres URL punktu końcowego autoryzacji zgodnie z RFC 6749. |
| AccessTokenEndpoint | Tak | Adres URL punktu końcowego tokenu zgodnie z RFC 6749. |
| Punkt żądania | Tak | Adres URL punktu końcowego informacji o użytkowniku zgodnie z RFC 6749. |
| AccessTokenResponseFormat | Nie | Format wywołania punktu końcowego tokenu dostępu. Na przykład Facebook wymaga metody HTTP GET, ale odpowiedź tokenu dostępu jest w formacie JSON. |
| DodatkoweParametry zapytania | Nie | Dodatkowe parametry kwerendy żądania. Na przykład można wysłać dodatkowe parametry do dostawcy tożsamości. Za pomocą ogranicznika przecinka można dołączyć wiele parametrów. |
| ClaimsEndpointAccessTokenName | Nie | Nazwa parametru ciągu zapytania tokenu dostępu. Niektóre punkty końcowe oświadczeń dostawców tożsamości obsługują żądanie GET HTTP. W takim przypadku token nośnika jest wysyłany przy użyciu parametru ciągu zapytania zamiast nagłówka autoryzacji. |
| ClaimsEndpointFormatName (Nazwaformat) | Nie | Nazwa parametru ciągu kwerendy formatu. Na przykład można ustawić nazwę, tak jak `format` w `https://api.linkedin.com/v1/people/~?format=json`tym punkcie końcowym oświadczeń LinkedIn . |
| ClaimsEndpointFormat | Nie | Wartość parametru ciągu zapytania formatu. Na przykład można ustawić wartość, jak `json` w tym `https://api.linkedin.com/v1/people/~?format=json`linkedin roszczeń punktu końcowego . |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_mode | Nie | Metoda używana przez dostawcę tożsamości do wysyłania wyników z powrotem do usługi Azure AD B2C. Możliwe `query`wartości: `form_post` , (domyślnie) lub `fragment`. |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie ze specyfikacją dostawcy tożsamości OAuth2. Takie `openid`jak `profile`, `email`i . |
| Funkcja HttpBinding | Nie | Oczekiwane powiązanie HTTP do tokenu dostępu i punktów końcowych tokenu oświadczeń. Możliwe `GET` wartości: `POST`lub .  |
| Nazwa pliku ResponseErrorCodeParamName | Nie | Nazwa parametru zawierającego komunikat o błędzie zwrócona za pośrednictwem protokołu HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointOdpowiedzialność | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w odpowiedzi z **AccessTokenEndpoint** przez niektórych dostawców tożsamości. Na przykład odpowiedź z **AccessTokenEndpoint** zawiera dodatkowy `openid`parametr, taki jak , który jest parametrem obowiązkowym oprócz access_token w ciągu zapytania żądania **ClaimsEndpoint.** Wiele nazw parametrów powinny być zmienione i oddzielone przecinkiem ',' ogranicznika. |
| ExtraParamsInClaimsEndpointRequest | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w **ClaimsEndpoint** żądania przez niektórych dostawców tożsamości. Wiele nazw parametrów powinny być zmienione i oddzielone przecinkiem ',' ogranicznika. |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |
| ResolveJsonPathsInJsonTokens  | Nie | Wskazuje, czy profil techniczny rozpoznaje ścieżki JSON. Możliwe wartości: `true` `false` , lub (domyślnie). Użyj tych metadanych do odczytu danych z zagnieżdżonego elementu JSON. W [OutputClaim](technicalprofiles.md#outputclaims)ustaw `PartnerClaimType` na element ścieżki JSON, który chcesz wyprowadzić. Na przykład: `firstName.localized` `data.0.to.0.email`, lub .|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz kryptograficzny jest wymagany tylko wtedy, gdy **metadane response_types** są ustawione na `code`. W takim przypadku usługa Azure AD B2C wywołuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są `id_token`ustawione na , można pominąć klucz kryptograficzny. |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania adresu URL przekierowania dostawcy `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`tożsamości wprowadź . Upewnij się, aby zastąpić **dzierżawy** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com) i **policyId** identyfikatorem zasad (na przykład b2c_1a_policy). Identyfikator URI przekierowania musi być we wszystkich małych literach.

Jeśli używasz domeny **b2clogin.com** zamiast **login.microsoftonline.com** Pamiętaj, aby używać b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodawanie Google+ jako dostawcy tożsamości OAuth2 przy użyciu zasad niestandardowych](identity-provider-google-custom.md)













