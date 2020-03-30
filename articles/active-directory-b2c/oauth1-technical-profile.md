---
title: Definiowanie profilu technicznego OAuth1 w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OAuth 1.0 w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184081"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego OAuth1 w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości [protokołu OAuth 1.0.](https://tools.ietf.org/html/rfc5849) W tym artykule opisano specyfikę profilu technicznego do interakcji z dostawcą oświadczeń, który obsługuje ten znormalizowany protokół. Dzięki profilowi technicznemu OAuth1 możesz ujednać informacje z dostawcą tożsamości opartym na UAuth1, takim jak Twitter. Federacja z dostawcą tożsamości umożliwia użytkownikom logowanie się przy istniejących tożsamościach społecznościowych lub korporacyjnych.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `OAuth1`na . Na przykład protokół profilu technicznego **Twitter-OAUTH1** to `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** i **InputClaimsTransformations** elementy są puste lub nieobecne.

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń zwróconych przez dostawcę tożsamości OAuth1. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również dołączyć oświadczenia, które nie są zwracane przez dostawcę tożsamości, tak długo, jak ustawić **DefaultValue** atrybut.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości Twitter:

- Oświadczenie **user_id,** które jest mapowane do **oświadczenia identyfikatora issuerUserId.**
- **Oświadczenie screen_name,** które jest mapowane na **oświadczenie displayName.**
- Oświadczenie **e-mail** bez mapowania nazw.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **Oświadczenie identityProvider,** który zawiera nazwę dostawcy tożsamości.
- Oświadczenie **authenticationSource** o wartości `socialIdpAuthentication`domyślnej .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| ProviderName | Nie | Nazwa dostawcy tożsamości. |
| request_token_endpoint | Tak | Adres URL punktu końcowego tokenu żądania zgodnie z RFC 5849. |
| authorization_endpoint | Tak | Adres URL punktu końcowego autoryzacji zgodnie z RFC 5849. |
| access_token_endpoint | Tak | Adres URL punktu końcowego tokenu zgodnie z RFC 5849. |
| Punkt żądania | Nie | Adres URL punktu końcowego informacji o użytkowniku. |
| ClaimsResponseFormatat | Nie | Format odpowiedzi oświadczeń.|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości.   |

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Podczas konfigurowania adresu URL przekierowania dostawcy `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`tożsamości wprowadź . Upewnij się, aby zastąpić **dzierżawy** nazwą dzierżawy (na przykład contosob2c.onmicrosoft.com) i **policyId** identyfikatorem zasad (na przykład b2c_1a_policy). Identyfikator URI przekierowania musi być we wszystkich małych literach. Dodaj adres URL przekierowania dla wszystkich zasad, które używają logowania dostawcy tożsamości.

Jeśli używasz domeny **b2clogin.com** zamiast **login.microsoftonline.com** Pamiętaj, aby używać b2clogin.com zamiast login.microsoftonline.com.

Przykłady:

- [Dodawanie Twittera jako dostawcy tożsamości OAuth1 przy użyciu zasad niestandardowych](identity-provider-twitter-custom.md)













