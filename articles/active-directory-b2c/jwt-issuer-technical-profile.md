---
title: Definiowanie profilu technicznego wystawcy tokenów JWT, w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definiowanie profilu technicznego wystawcy tokenów JWT, w przypadku zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 573463d91fc7a4119bd1bc30182588ff9dfdecb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510706"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego wystawcy tokenów JWT, w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory (Azure AD) B2C emituje kilka rodzajów tokenów zabezpieczających, w czasie przetwarzania każdego przepływu uwierzytelniania. Profilu technicznego wystawcy tokenów JWT emituje token JWT, która jest zwracana do aplikacji jednostki uzależnionej. Zazwyczaj ten profil techniczny jest ostatnim kroku aranżacji w podróży użytkownika.

## <a name="protocol"></a>Protocol

**Nazwa** atrybutu **protokołu** element musi być równa `None`. Ustaw **OutputTokenFormat** elementu `JWT`.

W poniższym przykładzie pokazano profilu technicznego dla `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Dane wejściowe, danych wyjściowych i zachować oświadczeń

**InputClaims**, **OutputClaims**, i **PersistClaims** elementy są puste lub nieobecne. **InutputClaimsTransformations** i **OutputClaimsTransformations** elementy są również nieobecne.

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Yes | Oświadczenie, które mają być używane jako tożsamość użytkownika oświadczenia w kodach autoryzacji OAuth2 i tokenów odświeżania. Domyślnie, należy ustawić go `objectId`, chyba że określisz inną SubjectNamingInfo typ oświadczenia. | 
| SendTokenResponseBodyWithJsonNumbers | Nie | Zawsze ustawiony na wartość `true`. Dla formatu starszej wersji, gdy wartości numeryczne są podane jako ciągi, a nie liczby JSON, ustaw `false`. Ten atrybut jest wymagany dla klientów, które miały zależności na wcześniejszych implementacji, które zwracane takie właściwości, jak ciągi. | 
| token_lifetime_secs | Nie | Okresy istnienia tokenu dostępu. Czas życia tokenu elementu nośnego OAuth 2.0, które są używane do uzyskiwania dostępu do chronionego zasobu. Wartość domyślna to 3600 sekund (1 godzina). Minimum (włącznie) to 300 sekund (5 minut). Maksymalna (włącznie) jest 86 400 sekund (24 godziny). | 
| id_token_lifetime_secs | Nie | Okresy istnienia tokenu Identyfikatora. Wartość domyślna to 3600 sekund (1 godzina). Minimum (włącznie) to 300 sekund (5 minut). Maksymalna (włącznie) to sekund 86,400 (24 godziny). | 
| refresh_token_lifetime_secs | Nie | Odśwież okresów istnienia tokenu. Maksymalny okres, przed którym token odświeżania można uzyskać nowy token dostępu, jeśli aplikacja została udzielona zakresu offline_access. Wartość domyślna to 120,9600 sekund (14 dni). Minimum (włącznie) jest 86 400 sekund (24 godziny). Maksymalna (włącznie) to 7,776,000 sekund (90 dni). | 
| rolling_refresh_token_lifetime_secs | Nie | Odśwież okres istnienia okna przewijania tokenu. Po upłynięciu tego okresu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od ostatniego okresu ważności tokenu odświeżania uzyskanego przez aplikację. Jeśli nie chcesz wymusić okres istnienia okna przewijania, ustaw wartość allow_infinite_rolling_refresh_token do `true`. Wartość domyślna to 7,776,000 sekund (90 dni). Minimum (włącznie) jest 86 400 sekund (24 godziny). Maksymalna (włącznie) to 31,536,000 sekund (365 dni). | 
| allow_infinite_rolling_refresh_token | Nie | Jeśli ustawiono `true`, token odświeżania oknem kroczącym okres istnienia nigdy nie wygasa. |
| IssuanceClaimPattern | Tak | Określa, czy oświadczenie wystawcy (iss) Jedna z wartości:<ul><li>AuthorityAndTenantGuid - oświadczenia iss obejmuje nazwę domeny, takie jak `login.microsoftonline` lub `tenant-name.b2clogin.com`oraz że identyfikator dzierżawy https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - oświadczenia iss obejmuje nazwę domeny, takie jak `login.microsoftonline` lub `tenant-name.b2clogin.com`, dzierżawy, identyfikator i nazwę jednostki uzależnionej zasad firmy. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Nie | Formanty `acr` wartości oświadczenia.<ul><li>Brak — usługi Azure AD B2C nie wystawiać oświadczeń acr</li><li>PolicyId - `acr` oświadczeń zawiera nazwę zasad</li></ul>Opcje ustawienie tej wartości są TFP (zasady zaufania framework) i ACR (odwołanie w kontekście uwierzytelniania). Zaleca się ustawienie tej wartości na TFP, aby ustawić wartość, upewnij się, `<Item>` z `Key="AuthenticationContextReferenceClaimPattern"` istnieje, a wartość to `None`. W zasadach strony jednostki uzależnionej, należy dodać `<OutputClaims>` , należy dodać ten element `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Ponadto upewnij się, że Twoje zasady zawiera typ oświadczenia `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

CryptographicKeys element zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| issuer_secret | Yes | X509 certyfikatu (zestawu kluczy RSA) używany do podpisywania tokenu JWT. Jest to `B2C_1A_TokenSigningKeyContainer` klucza skonfigurowane w [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Tak | X509 certyfikat (zestawu kluczy RSA) ma być używany do szyfrowania tokenu odświeżania. Możesz skonfigurować `B2C_1A_TokenEncryptionKeyContainer` w [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) |














