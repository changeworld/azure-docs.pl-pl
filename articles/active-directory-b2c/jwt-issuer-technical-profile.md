---
title: Zdefiniuj profil techniczny dla wystawcy JWT w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny dla wystawców tokenów sieci Web JSON (JWT) w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 41661277d89c45baf2350282c6e4b500fae63662
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949827"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny dla wystawcy token JWT w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania poszczególnych przepływów uwierzytelniania. Profil techniczny wystawcy token JWT emituje token JWT zwracanego z powrotem do aplikacji jednostki uzależnionej. Zazwyczaj ten profil techniczny to ostatni krok aranżacji w podróży użytkownika.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **name** elementu **Protocol** musi mieć wartość `None`. Ustaw element **OutputTokenFormat** na `JWT`.

W poniższym przykładzie przedstawiono profil techniczny dla `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Oświadczenia danych wejściowych, wyjściowych i utrwalania

Elementy **InputClaims**, **OutputClaims**i **PersistClaims** są puste lub nie istnieją. Obecne są również elementy **InutputClaimsTransformations** i **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Tak | Żądanie, które ma być używane jako żądanie tożsamości użytkownika w ramach kodów autoryzacji OAuth2 i odświeżanie tokenów. Domyślnie należy ustawić ją na `objectId`, chyba że zostanie określony inny typ "SubjectNamingInfo". |
| SendTokenResponseBodyWithJsonNumbers | Nie | Zawsze ustawiaj na `true`. W przypadku starszego formatu, gdzie wartości liczbowe są podawane jako ciągi zamiast liczb JSON, ustaw wartość `false`. Ten atrybut jest wymagany w przypadku klientów, którzy korzystali z zależności od wcześniejszej implementacji, która zwróciła takie właściwości jako ciągi. |
| token_lifetime_secs | Nie | Okresy istnienia tokenu dostępu. Okres istnienia tokenu okaziciela OAuth 2,0, który służy do uzyskiwania dostępu do chronionego zasobu. Wartość domyślna to 3 600 sekund (1 godzina). Minimalna (włącznie) to 300 sekund (5 minut). Wartość maksymalna (włącznie) to 86 400 sekund (24 godziny). |
| id_token_lifetime_secs | Nie | Okresy istnienia tokenów identyfikatorów. Wartość domyślna to 3 600 sekund (1 godzina). Minimalna (włącznie) to 300 sekund (5 minut). Wartość maksymalna (włącznie) jest sekund 86 400 (24 godziny). |
| refresh_token_lifetime_secs | Nie | Odświeżanie okresów istnienia tokenu. Maksymalny przedział czasu, przez który token odświeżania może zostać użyty do uzyskania nowego tokenu dostępu, jeśli Twoja aplikacja uzyskała zakres offline_access. Wartość domyślna to 120, 9600 sekund (14 dni). Minimalna (włącznie) to 86 400 sekund (24 godziny). Wartość maksymalna (włącznie) to 7 776 000 sekund (90 dni). |
| rolling_refresh_token_lifetime_secs | Nie | Zaodświeżaj okres istnienia okna przewijania tokenu. Po upływie tego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia, niezależnie od okresu ważności ostatniego tokenu odświeżania nabytego przez aplikację. Jeśli nie chcesz wymusić okresu istnienia ruchomego okna, ustaw wartość allow_infinite_rolling_refresh_token na `true`. Wartość domyślna to 7 776 000 sekund (90 dni). Minimalna (włącznie) to 86 400 sekund (24 godziny). Wartość maksymalna (włącznie) to 31 536 000 sekund (365 dni). |
| allow_infinite_rolling_refresh_token | Nie | W przypadku wybrania wartości `true`okres istnienia okna przewijania tokenu odświeżania nigdy nie wygasa. |
| IssuanceClaimPattern | Tak | Kontroluje wierzytelność wystawcy (ISS). Jedna z wartości:<ul><li>AuthorityAndTenantGuid — zgłoszenie ISS obejmuje nazwę domeny, taką jak `login.microsoftonline` lub `tenant-name.b2clogin.com`, i identyfikator dzierżawy https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp — zgłoszenie ISS obejmuje nazwę domeny, taką jak `login.microsoftonline` lub `tenant-name.b2clogin.com`, identyfikator dzierżawy i Nazwa zasad jednostki uzależnionej. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> |
| AuthenticationContextReferenceClaimPattern | Nie | Kontroluje `acr` wartość żądania.<ul><li>Brak — Azure AD B2C nie wystawia żądania ACR</li><li>PolicyId — `acr` zawiera nazwę zasad</li></ul>Opcje ustawiania tej wartości to TFP (zasady struktury zaufania) i ACR (odwołanie kontekstu uwierzytelniania). Zaleca się ustawienie tej wartości na TFP, aby ustawić wartość, upewnij się, że `<Item>` z `Key="AuthenticationContextReferenceClaimPattern"` istnieje, a wartość jest `None`. W zasadach jednostki uzależnionej Dodaj `<OutputClaims>` element, Dodaj `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`tego elementu. Upewnij się również, że zasady zawierają typ `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element CryptographicKeys zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| issuer_secret | Tak | Certyfikat x509 (zestaw kluczy RSA) służący do podpisywania tokenu JWT. Jest to klucz `B2C_1A_TokenSigningKeyContainer`, który został rozfigurować w temacie [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). |
| issuer_refresh_token_key | Tak | Certyfikat x509 (zestaw kluczy RSA) służący do szyfrowania tokenu odświeżania. Klucz `B2C_1A_TokenEncryptionKeyContainer` został skonfigurowany w temacie [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md) |














