---
title: Definiowanie profilu technicznego dla wystawcy JWT w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny wystawcy tokenu sieci Web JSON (JWT) w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671810"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego dla wystawcy tokenu JWT w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania każdego przepływu uwierzytelniania. Profil techniczny wystawcy tokenu JWT emituje token JWT, który jest zwracany z powrotem do aplikacji jednostki uzależniającej. Zazwyczaj ten profil techniczny jest ostatnim krokiem aranżacji w podróży użytkownika.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `None`na . Ustaw element **OutputTokenFormat** na `JWT`.

W poniższym przykładzie `JwtIssuer`przedstawiono profil techniczny dla:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Oświadczenia dotyczące danych wejściowych, wyjściowych i utrzymywać

**InputClaims**, **OutputClaims**i **PersistClaims** elementy są puste lub nieobecne. **InutputClaimsTransformations** i **OutputClaimsTransformations** elementy są również nieobecne.

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Tak | Oświadczenie, które powinno być używane jako oświadczenie tożsamości użytkownika w kodach autoryzacji OAuth2 i tokenach odświeżania. Domyślnie należy ustawić go `objectId`na , chyba że określisz inny typ oświadczenia SubjectNamingInfo. |
| SendTokenResponseBodyWithJsonNumbers | Nie | Zawsze ustawiaj na `true`. W przypadku starszego formatu, w którym wartości liczbowe są `false`podane jako ciągi znaków zamiast liczb JSON, ustawiono na . Ten atrybut jest potrzebny dla klientów, którzy wzięli zależność od wcześniejszej implementacji, która zwróciła takie właściwości jak ciągi. |
| token_lifetime_secs | Nie | Dostęp do okresów istnienia tokenu. Okres istnienia tokenu nośnika OAuth 2.0 używanego do uzyskiwania dostępu do chronionego zasobu. Wartość domyślna to 3600 sekund (1 godzina). Minimalna wartość (włącznie) to 300 sekund (5 minut). Maksymalna wartość (włącznie) wynosi 86 400 sekund (24 godziny). |
| id_token_lifetime_secs | Nie | Okresy istnienia tokenu identyfikatora. Wartość domyślna to 3600 sekund (1 godzina). Minimalna wartość (włącznie) to 300 sekund (5 minut). Maksymalna wartość (włącznie) to sekundy 86 400 (24 godziny). |
| refresh_token_lifetime_secs | Nie | Odśwież okresy istnienia tokenu. Maksymalny okres, przed którym token odświeżania może służyć do uzyskania nowego tokenu dostępu, jeśli aplikacja została przyznana offline_access zakresie. Wartość domyślna to 120 9600 sekund (14 dni). Minimalna wartość (włącznie) wynosi 86 400 sekund (24 godziny). Maksymalna wartość (włącznie) wynosi 7 776 000 sekund (90 dni). |
| rolling_refresh_token_lifetime_secs | Nie | Odśwież okres istnienia okna przesuwnego tokenu. Po upływie tego okresu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego tokenu odświeżania nabytego przez aplikację. Jeśli nie chcesz wymuszać okresu istnienia okna przesuwnego, `true`ustaw wartość allow_infinite_rolling_refresh_token na . Wartość domyślna to 7 776 000 sekund (90 dni). Minimalna wartość (włącznie) wynosi 86 400 sekund (24 godziny). Maksymalna wartość (włącznie) wynosi 31 536 000 sekund (365 dni). |
| allow_infinite_rolling_refresh_token | Nie | Jeśli ustawiona na `true`, okres istnienia okna przesuwnego tokenu odświeżania nigdy nie wygasa. |
| IssuanceClaimPattern | Nie | Kontroluje reklamację Emitenta (iss). Jedna z wartości:<ul><li>AuthorityAndTenantGuid — oświadczenie iss zawiera nazwę `login.microsoftonline` domeny, takie jak lub `tenant-name.b2clogin.com`,\/i identyfikator dzierżawy https: /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp — oświadczenie iss zawiera nazwę `login.microsoftonline` domeny, takich jak lub `tenant-name.b2clogin.com`, identyfikator dzierżawy i nazwę zasad jednostki uzależniającej. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Wartość domyślna: AuthorityAndTenantGuid |
| UwierzytelnianieContekReferatorClaimPattern | Nie | Steruje `acr` wartością oświadczenia.<ul><li>Brak — usługa Azure AD B2C nie wystawia oświadczenia acr</li><li>PolicyId — `acr` oświadczenie zawiera nazwę zasad</li></ul>Opcje ustawiania tej wartości to TFP (zasady struktury zaufania) i ACR (odwołanie do kontekstu uwierzytelniania). Zaleca się ustawienie tej wartości na TFP, aby `<Item>` ustawić `Key="AuthenticationContextReferenceClaimPattern"` wartość, upewnić `None`się, że z istnieje, a wartość jest . W zasadach jednostki `<OutputClaims>` uzależniającej dodaj `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`element, dodaj ten element . Upewnij się również, że zasady zawierają typ oświadczenia`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|Identyfikator RefreshTokenUserJourneyId| Nie | Identyfikator podróży użytkownika, który powinien zostać wykonany podczas odświeżania żądania [post tokenu dostępu](authorization-code-flow.md#4-refresh-the-token) do punktu końcowego. `/token` |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

CryptographicKey element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| issuer_secret | Tak | Certyfikat X509 (zestaw kluczy RSA) używany do podpisywania tokenu JWT. Jest to `B2C_1A_TokenSigningKeyContainer` klucz współfigurowany w [wprowadzenie do niestandardowych zasad](custom-policy-get-started.md). |
| issuer_refresh_token_key | Tak | Certyfikat X509 (zestaw kluczy RSA) używany do szyfrowania tokenu odświeżania. Klucz został `B2C_1A_TokenEncryptionKeyContainer` skonfigurowany w obszarze [Wprowadzenie do zasad niestandardowych](custom-policy-get-started.md) |














