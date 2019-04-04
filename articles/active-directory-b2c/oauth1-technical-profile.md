---
title: Definiowanie profilu technicznego OAuth1 w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definiowanie profilu technicznego OAuth1 w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 86de73394d96d1122abce44504d2b0fd99a01841
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915786"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego OAuth1 w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C zapewnia obsługę [protokołu OAuth 1.0](https://tools.ietf.org/html/rfc5849) dostawcy tożsamości. W tym artykule opisano szczegóły profilu technicznego do interakcji z dostawcy oświadczeń, który obsługuje ten standardowy protokół. Za pomocą OAuth1 profilu technicznego może tworzyć federacje z OAuth1 na podstawie dostawcy tożsamości, takich jak serwis Twitter, co pozwala użytkownikom Zaloguj się przy użyciu istniejącej społecznościowych lub tożsamościami w przedsiębiorstwie.

## <a name="protocol"></a>Protokół

**Nazwa** atrybutu **protokołu** element musi być równa `OAuth1`. Na przykład protokół **Twitter OAUTH1** profilu technicznego jest `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>Oświadczeń wejściowych

**InputClaims** i **InputClaimsTransformations** elementy są puste lub nieobecne.

## <a name="output-claims"></a>Oświadczeń danych wyjściowych

**OutputClaims** element zawiera listę oświadczeń zwrócona przez dostawcę tożsamości OAuth1. Może być konieczne mapowania nazwy oświadczenia, zdefiniowane w zasadach do nazwy zdefiniowane w dostawcy tożsamości. Mogą również obejmować oświadczenia, które nie są zwracane przez dostawcę tożsamości, tak długo, jak można ustawić **DefaultValue** atrybutu.

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.

Poniższy przykład przedstawia oświadczenia zwrócona przez dostawcę tożsamości usługi Twitter:

- **User_id** oświadczenia, który jest zamapowany **socialIdpUserId** oświadczenia.
- **Screen_name** oświadczenia, który jest zamapowany **displayName** oświadczenia.
- **E-mail** oświadczenia bez mapowania nazw.

Profil techniczny zwraca również wartość oświadczenia, które nie są zwracane przez dostawcę tożsamości: 

- **IdentityProvider** oświadczenia, który zawiera nazwę dostawcy tożsamości.
- **AuthenticationSource** oświadczenie z wartością domyślną `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_id | Yes | Identyfikator aplikacji dostawcy tożsamości. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| request_token_endpoint | Yes | Adres URL punktu końcowego tokenu żądania zgodnie z RFC 5849. |
| authorization_endpoint | Yes | Adres URL punktu końcowego autoryzacji, zgodnie z RFC 5849. |
| access_token_endpoint | Yes | Adres URL punktu końcowego tokenu zgodnie z RFC 5849. |
| ClaimsEndpoint | Nie | Adres URL punktu końcowego informacji użytkownika. | 
| ClaimsResponseFormat | Nie | Format odpowiedzi oświadczeń.|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKeys** element zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| client_secret | Yes | Klucz tajny klienta aplikacji dostawcy tożsamości.   | 

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Po skonfigurowaniu adres URL przekierowania dostawcy tożsamości, wprowadź `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Upewnij się zastąpić **dzierżawy** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com) i **policyId** o identyfikatorze zasady (na przykład b2c_1a_policy). Identyfikator URI przekierowania musi być zapisana w same małe litery. Należy dodać przekierowanie z pakietem zbiorczym aktualizacji dla wszystkich zasad, które używają logowania dostawcy tożsamości. 

Jeśli używasz **z usługi b2clogin.com** domeny zamiast **login.microsoftonline.com** upewnij się korzystać z usługi b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodawanie usługi Twitter jako dostawcy tożsamości OAuth1 za pomocą zasad niestandardowych](active-directory-b2c-custom-setup-twitter-idp.md)













