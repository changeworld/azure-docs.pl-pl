---
title: Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33bad4982d54eb18e91be28511fb9137223f4a91
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950973"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny OAuth2 w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości protokołu OAuth2. OAuth2 to podstawowy protokół do autoryzacji i uwierzytelniania delegowanego. Aby uzyskać więcej informacji, zobacz artykuł [RFC 6749 platformy autoryzacji OAuth 2,0](https://tools.ietf.org/html/rfc6749). Profil techniczny OAuth2 umożliwia sfederować z dostawcą tożsamości opartym na OAuth2, takim jak Facebook. Federowanie z dostawcą tożsamości umożliwia użytkownikom logowanie się przy użyciu istniejących tożsamości społecznościowych lub firmowych.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **name** elementu **Protocol** musi mieć wartość `OAuth2`. Na przykład protokół dla profilu technicznego **Facebook-OAuth** jest `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
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

Element **OutputClaims** zawiera listę oświadczeń zwracanych przez dostawcę tożsamości OAuth2. Może być konieczne zamapowanie nazwy żądania zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Możesz również uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości, dopóki ustawisz atrybut `DefaultValue`.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości w usłudze Facebook:

- **First_name** jest mapowany na **daną** wartość.
- **Last_name** jest mapowany do żądania **nazwisko** .
- Nazwa **wyświetlana** bez mapowania nazwy.
- Bez mapowania nazwy.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **IdentityProvider** , który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** z wartością domyślną **socialIdpAuthentication**.

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
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience | Nie | Odbiorcy id_token. Jeśli ta wartość jest określona, Azure AD B2C sprawdza, czy token znajduje się w ramach żądania zwróconego przez dostawcę tożsamości i jest równy określonemu. |
| authorization_endpoint | Tak | Adres URL punktu końcowego autoryzacji zgodnie z dokumentem RFC 6749. |
| AccessTokenEndpoint | Tak | Adres URL tokenu dla punktu końcowego w formacie RFC 6749. |
| ClaimsEndpoint | Tak | Adres URL punktu końcowego informacji o użytkowniku zgodnie z dokumentem RFC 6749. |
| AccessTokenResponseFormat | Nie | Format wywołania punktu końcowego tokenu dostępu. Na przykład serwis Facebook wymaga metody HTTP GET, ale odpowiedź tokenu dostępu jest w formacie JSON. |
| AdditionalRequestQueryParameters | Nie | Dodatkowe parametry kwerendy żądania. Na przykład możesz chcieć wysłać dodatkowe parametry do dostawcy tożsamości. Można uwzględnić wiele parametrów przy użyciu ogranicznika przecinka. |
| ClaimsEndpointAccessTokenName | Nie | Nazwa parametru ciągu zapytania tokenu dostępu. Niektórzy punkty końcowe oświadczeń dostawców tożsamości obsługują pobieranie żądania HTTP. W takim przypadku token okaziciela jest wysyłany przy użyciu parametru ciągu zapytania zamiast nagłówka autoryzacji. |
| ClaimsEndpointFormatName | Nie | Nazwa parametru ciągu zapytania. Na przykład możesz ustawić nazwę jako `format` w tym punkcie końcowym oświadczeń usługi LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ClaimsEndpointFormat | Nie | Wartość parametru ciągu zapytania. Na przykład można ustawić wartość jako `json` w tym punkcie końcowym oświadczeń usługi LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| response_mode | Nie | Metoda wykorzystywana przez dostawcę tożsamości do wysyłania wyniku z powrotem do Azure AD B2C. Możliwe wartości: `query`, `form_post` (wartość domyślna) lub `fragment`. |
| scope | Nie | Zakres żądania, który jest zdefiniowany zgodnie ze specyfikacją dostawcy tożsamości OAuth2. Takie jak `openid`, `profile`i `email`. |
| HttpBinding | Nie | Oczekiwano powiązania HTTP z punktami końcowymi tokenu dostępu i tokenów oświadczeń. Możliwe wartości: `GET` lub `POST`.  |
| ResponseErrorCodeParamName | Nie | Nazwa parametru, który zawiera komunikat o błędzie zwrócony za pośrednictwem protokołu HTTP 200 (ok). |
| ExtraParamsInAccessTokenEndpointResponse | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w odpowiedzi z **AccessTokenEndpoint** przez niektórych dostawców tożsamości. Na przykład odpowiedź z **AccessTokenEndpoint** zawiera dodatkowy parametr, taki jak `openid`, który jest obowiązkowym parametrem poza access_token w ciągu zapytania żądania **ClaimsEndpoint** . Nazwy wielu parametrów powinny być wyprowadzane i oddzielane przecinkami ",". |
| ExtraParamsInClaimsEndpointRequest | Nie | Zawiera dodatkowe parametry, które mogą być zwracane w żądaniu **ClaimsEndpoint** przez niektórych dostawców tożsamości. Nazwy wielu parametrów powinny być wyprowadzane i oddzielane przecinkami ",". |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz kryptograficzny jest wymagany tylko wtedy, gdy metadane **response_types** są ustawione na `code`. W takim przypadku Azure AD B2C wykonuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są ustawione na `id_token`, można pominąć klucz kryptograficzny. |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania adresu URL przekierowania dostawcy tożsamości wprowadź `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Pamiętaj o zamianie **dzierżawy** na nazwę dzierżawy (na przykład contosob2c.onmicrosoft.com) i **policyId** z identyfikatorem zasad (na przykład b2c_1a_policy). Identyfikator URI przekierowania musi zawierać tylko małe litery.

Jeśli używasz domeny **b2clogin.com** zamiast **login.microsoftonline.com** upewnij się, że używasz b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodaj firmę Google + jako dostawcę tożsamości OAuth2 przy użyciu zasad niestandardowych](active-directory-b2c-custom-setup-goog-idp.md)













