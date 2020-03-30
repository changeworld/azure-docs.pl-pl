---
title: Włącz weryfikację hasła jednorazowego (OTP)
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować scenariusz jednorazowego hasła (OTP) przy użyciu zasad niestandardowych usługi Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332784"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiowanie jednorazowego profilu technicznego hasła w zasadach niestandardowych usługi Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę zarządzania generowaniem i weryfikacją hasła jednorazowego. Użyj profilu technicznego, aby wygenerować kod, a następnie sprawdź ten kod później.

Jednorazowy profil techniczny hasła może również zwrócić komunikat o błędzie podczas weryfikacji kodu. Zaprojektuj integrację z jednorazowym hasłem przy użyciu **profilu technicznego sprawdzania poprawności**. Profil techniczny sprawdzania poprawności wywołuje profil techniczny jednorazowego hasła, aby zweryfikować kod. Profil techniczny sprawdzania poprawności sprawdza poprawność danych dostarczonych przez użytkownika przed kontynuowaniem podróży użytkownika. W profilu technicznym sprawdzania poprawności komunikat o błędzie jest wyświetlany na stronie z własnym potwierdzeniem.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

W poniższym przykładzie pokazano profil techniczny jednorazowego hasła:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generowanie kodu

Pierwszym trybem tego profilu technicznego jest wygenerowanie kodu. Poniżej znajdują się opcje, które można skonfigurować dla tego trybu.

### <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** element zawiera listę oświadczeń wymaganych do wysłania do dostawcy protokołu hasła jednorazowego. Możesz również zamapować nazwę roszczenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId (ida) | Wymagany | Opis |
| --------- | -------- | ----------- |
| identyfikator | Tak | Identyfikator do identyfikowania użytkownika, który musi zweryfikować kod później. Jest on powszechnie używany jako identyfikator miejsca docelowego, do którego kod jest dostarczany, na przykład adres e-mail lub numer telefonu. |

**InputClaimsTransformations** element może zawierać kolekcję **InputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wysłaniem do dostawcy protokołu hasła jednorazowe.

### <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń generowanych przez dostawcę protokołu hasła jednorazowego. Możesz również zamapować nazwę roszczenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId (ida) | Wymagany | Opis |
| --------- | -------- | ----------- |
| otpGenerowane | Tak | Wygenerowany kod, którego sesja jest zarządzana przez usługę Azure AD B2C. |

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

Do skonfigurowania trybu generowania kodu można użyć następujących ustawień:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| KodExpirationInSeconds | Nie | Czas w sekundach do wygaśnięcia kodu. Minimum: `60`; Maksymalna: `1200`; Domyślnie: `600`. |
| KodLength | Nie | Długość kodu. Wartością domyślną jest `6`. |
| Characterset | Nie | Zestaw znaków dla kodu, sformatowany do użycia w wyrażeniu regularnym. Na przykład `a-z0-9A-Z`. Wartością domyślną jest `0-9`. Zestaw znaków musi zawierać co najmniej 10 różnych znaków w określonym zestawie. |
| NumRetryAttempts | Nie | Liczba prób weryfikacji, zanim kod zostanie uznany za nieprawidłowy. Wartością domyślną jest `5`. |
| Operacja | Tak | Operacja do wykonania. Możliwa `GenerateCode`wartość: . |
| Ponowne użycieSamecode | Nie | Czy duplikat kodu powinny być podane, a nie generowanie nowego kodu, gdy dany kod nie wygasł i jest nadal prawidłowy. Wartością domyślną jest `false`. |

### <a name="example"></a>Przykład

Poniższy `TechnicalProfile` przykład jest używany do generowania kodu:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Weryfikowanie kodu

Drugim trybem tego profilu technicznego jest zweryfikowanie kodu. Poniżej znajdują się opcje, które można skonfigurować dla tego trybu.

### <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** element zawiera listę oświadczeń wymaganych do wysłania do dostawcy protokołu hasła jednorazowego. Możesz również zamapować nazwę roszczenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId (ida) | Wymagany | Opis |
| --------- | -------- | ----------- |
| identyfikator | Tak | Identyfikator do identyfikowania użytkownika, który wcześniej wygenerował kod. Jest on powszechnie używany jako identyfikator miejsca docelowego, do którego kod jest dostarczany, na przykład adres e-mail lub numer telefonu. |
| otpToVerify | Tak | Kod weryfikacyjny podany przez użytkownika. |

**InputClaimsTransformations** element może zawierać kolekcję **InputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wysłaniem do dostawcy protokołu hasła jednorazowe.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Podczas weryfikacji kodu tego dostawcy protokołu nie ma żadnych oświadczeń dotyczących danych wyjściowych.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

Do trybu weryfikacji kodu można użyć następujących ustawień:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Operacja do wykonania. Możliwa `VerifyCode`wartość: . |


### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Następujące metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych po niepowodzeniu weryfikacji kodu. Metadane powinny być skonfigurowane w [samodzielnie potwierdzonym](self-asserted-technical-profile.md) profilu technicznym. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#one-time-password-error-messages).

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nie | Komunikat do wyświetlenia do użytkownika, jeśli sesja weryfikacji kodu wygasła. Jest to kod wygasł lub kod nigdy nie został wygenerowany dla danego identyfikatora. |
| UserMessageIfMaxRetryPróbował | Nie | Komunikat do wyświetlenia do użytkownika, jeśli przekroczył maksymalną dozwoloną próbę weryfikacji. |
| Kod użytkownikaMessageIfInvalid | Nie | Komunikat do wyświetlenia użytkownikowi, jeśli podał nieprawidłowy kod. |
|UserMessageIfSessionConflict|Nie| Komunikat do wyświetlenia do użytkownika, jeśli nie można zweryfikować kodu.|

### <a name="example"></a>Przykład

Poniższy `TechnicalProfile` przykład służy do weryfikacji kodu:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujący artykuł, na przykład przy użyciu jednorazowego profilu technicznego hasła z niestandardową weryfikacją wiadomości e-mail:

- [Niestandardowa weryfikacja poczty e-mail w usłudze Azure Active Directory B2C](custom-email.md)

