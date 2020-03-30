---
title: Definiowanie profilu technicznego OpenID Connect w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny OpenID Connect w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332758"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego OpenID Connect w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę dostawcy tożsamości protokołu [OpenID Connect.](https://openid.net/2015/04/17/openid-connect-certification-program/) OpenID Connect 1.0 definiuje warstwę tożsamości na górze OAuth 2.0 i reprezentuje stan techniki w nowoczesnych protokołach uwierzytelniania. Za pomocą profilu technicznego OpenID Connect można połączyć się z dostawcą tożsamości opartym na usłudze OpenID Connect, takim jak usługa Azure AD. Federacja u dostawcy tożsamości umożliwia użytkownikom logowanie się przy istniejących tożsamościach społecznościowych lub korporacyjnych.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `OpenIdConnect`na . Na przykład protokół profilu technicznego **MSA-OIDC** to: `OpenIdConnect`

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

**OutputClaims** element zawiera listę oświadczeń zwracanych przez dostawcę tożsamości OpenID Connect. Może być konieczne mapowanie nazwy oświadczenia zdefiniowanego w zasadach na nazwę zdefiniowaną w dostawcy tożsamości. Można również dołączyć oświadczenia, które nie są zwracane przez dostawcę tożsamości, tak długo, jak ustawić `DefaultValue` atrybut.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

W poniższym przykładzie przedstawiono oświadczenia zwrócone przez dostawcę tożsamości konta Microsoft:

- Sub **sub** oświadczenie, które jest mapowane do **oświadczenia identyfikatora issuerUserId.**
- Oświadczenie **nazwy,** które jest mapowane do **oświadczenia displayName.**
- **Wiadomość e-mail** bez mapowania nazw.

Profil techniczny zwraca również oświadczenia, które nie są zwracane przez dostawcę tożsamości:

- **Oświadczenie identityProvider,** który zawiera nazwę dostawcy tożsamości.
- Oświadczenie **authenticationSource** z domyślną wartością **socialIdpAuthentication**.

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
| client_id | Tak | Identyfikator aplikacji dostawcy tożsamości. |
| IdTokenAudience (IdTokenAudience) | Nie | Publiczność id_token. Jeśli określono, usługi Azure AD `aud` B2C sprawdza, czy oświadczenie w tokenie zwrócone przez dostawcę tożsamości jest równa określonej w metadanych IdTokenAudience.  |
| Metadanych | Tak | Adres URL, który wskazuje dokument konfiguracji dostawcy tożsamości OpenID Connect, który jest również znany jako dobrze znany punkt końcowy konfiguracji OpenID. Adres URL może `{tenant}` zawierać wyrażenie, które jest zastępowane nazwą dzierżawy.  |
| authorization_endpoint | Nie | Adres URL, który wskazuje punkt końcowy autoryzacji konfiguracji dostawcy tożsamości OpenID Connect. Wartość metadanych authorization_endpoint ma pierwszeństwo przed `authorization_endpoint` określonym w punkcie końcowym konfiguracji Dobrze znanej konfiguracji OpenID. Adres URL może `{tenant}` zawierać wyrażenie, które jest zastępowane nazwą dzierżawy. |
| issuer | Nie | Unikatowy identyfikator dostawcy tożsamości OpenID Connect. Wartość metadanych wystawcy ma `issuer` pierwszeństwo przed określonym w openid dobrze znany punkt końcowy konfiguracji.  Jeśli zostanie określony, usługa Azure `iss` AD B2C sprawdza, czy oświadczenie w tokenie zwrócone przez dostawcę tożsamości jest równe temu określonej w metadanych wystawcy. |
| ProviderName | Nie | Nazwa dostawcy tożsamości.  |
| response_types | Nie | Typ odpowiedzi zgodnie ze specyfikacją OpenID Connect Core 1.0. Możliwe `id_token`wartości: `code`, `token`, lub . |
| response_mode | Nie | Metoda używana przez dostawcę tożsamości do wysyłania wyników z powrotem do usługi Azure AD B2C. Możliwe `query`wartości: `form_post` , (domyślnie) lub `fragment`. |
| scope | Nie | Zakres żądania zdefiniowanego zgodnie ze specyfikacją OpenID Connect Core 1.0. Takie `openid`jak `profile`, `email`i . |
| Funkcja HttpBinding | Nie | Oczekiwane powiązanie HTTP do tokenu dostępu i punktów końcowych tokenu oświadczeń. Możliwe `GET` wartości: `POST`lub .  |
| ValidTokenIssuerPrefixes | Nie | Klucz, który może służyć do logowania się do każdej z dzierżaw podczas korzystania z dostawcy tożsamości wielu dzierżawców, takich jak usługa Azure Active Directory. |
| UżyjPolicyInRedirectUri | Nie | Wskazuje, czy podczas konstruowania identyfikatora URI przekierowania ma być używany. Podczas konfigurowania aplikacji w dostawcy tożsamości, należy określić identyfikator URI przekierowania. Przekierowanie identyfikatora URI wskazuje na `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`usługę Azure AD B2C, .  Jeśli określisz `false`, należy dodać identyfikator URI przekierowania dla każdej używanej zasady. Na przykład: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nie | Wskazuje, czy żądanie do usługi zewnętrznej powinno być oznaczone jako błąd, jeśli kod stanu Http znajduje się w zakresie 5xx. Wartość domyślna to `false`. |
| DiscoverMetadataByTokenIssuer | Nie | Wskazuje, czy metadane OIDC powinny zostać wykryte przy użyciu wystawcy w tokenie JWT. |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |

### <a name="ui-elements"></a>Elementy interfejsu użytkownika
 
Do skonfigurowania komunikatu o błędzie wyświetlanego po awarii można użyć następujących ustawień. Metadane powinny być skonfigurowane w profilu technicznym OpenID Connect. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Nie | Komunikat do wyświetlenia do użytkownika, jeśli konto z pod warunkiem nazwy użytkownika nie znaleziono w katalogu. |
| UserMessageIfInvalidPassword | Nie | Komunikat wyświetlany użytkownikowi, jeśli hasło jest nieprawidłowe. |
| UserMessageIfOldPasswordUed| Nie |  Komunikat do wyświetlenia użytkownikowi, jeśli używane jest stare hasło.|

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** zawiera następujący atrybut:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| client_secret | Tak | Klucz tajny klienta aplikacji dostawcy tożsamości. Klucz kryptograficzny jest wymagany tylko wtedy, gdy **metadane response_types** są ustawione na `code`. W takim przypadku usługa Azure AD B2C wywołuje inne wywołanie wymiany kodu autoryzacji dla tokenu dostępu. Jeśli metadane są `id_token` ustawione, można pominąć klucz kryptograficzny.  |

## <a name="redirect-uri"></a>Przekieruj identyfikator Uri

Podczas konfigurowania identyfikatora URI przekierowania `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`dostawcy tożsamości wprowadź program . Pamiętaj, aby `{your-tenant-name}` zastąpić nazwą dzierżawy. Identyfikator URI przekierowania musi być we wszystkich małych literach.

Przykłady:

- [Dodawanie konta Microsoft (MSA) jako dostawcy tożsamości przy użyciu zasad niestandardowych](identity-provider-microsoft-account-custom.md)
- [Logowanie się przy użyciu kont usługi Azure AD](identity-provider-azure-ad-single-tenant-custom.md)
- [Zezwalaj użytkownikom na logowanie się do wielodostępnego dostawcy tożsamości usługi Azure AD przy użyciu zasad niestandardowych](identity-provider-azure-ad-multi-tenant-custom.md)
