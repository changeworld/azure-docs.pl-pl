---
title: Włącz weryfikację hasła jednorazowego (OTP)
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować scenariusz jednorazowego hasła (OTP) za pomocą zasad niestandardowych Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9becb91cfffd4553b2b8aa1a2d616963eae92ab0
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114058"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Zdefiniuj profil techniczny hasła jednorazowego w zasadach niestandardowych Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę zarządzania generowaniem i weryfikacją hasła jednorazowego. Użyj profilu technicznego, aby wygenerować kod, a następnie sprawdź, czy kod jest późniejszy.

Profil techniczny hasła jednorazowego może również zwrócić komunikat o błędzie podczas weryfikacji kodu. Zaprojektuj integrację z hasłem jednorazowym przy użyciu **profilu technicznego weryfikacji**. Profil techniczny weryfikacji jest wywoływany przy użyciu profilu technicznego hasła jednorazowego w celu zweryfikowania kodu. Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed kontynuowaniem podróży użytkownika. W profilu technicznym weryfikacji na stronie z potwierdzeniem zostanie wyświetlony komunikat o błędzie.

## <a name="protocol"></a>Protokół

Atrybut **name** elementu **Protocol** musi mieć wartość `Proprietary`. Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Poniższy przykład przedstawia profil techniczny hasła jednorazowego:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generowanie kodu

Pierwszy tryb tego profilu technicznego to generowanie kodu. Poniżej przedstawiono opcje, które można skonfigurować dla tego trybu.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń wymaganych do wysłania do dostawcy protokołu hasła jednorazowego. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator identyfikujący użytkownika, który musi weryfikować kod później. Jest on często używany jako identyfikator miejsca docelowego, do którego został dostarczony kod, na przykład adres e-mail lub numer telefonu. |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do dostawcy protokołu hasła jednorazowego.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń wygenerowanych przez dostawcę protokołu hasła jednorazowego. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagany | Opis |
| --------- | -------- | ----------- |
| otpGenerated | Yes | Wygenerowany kod, którego sesja jest zarządzana przez Azure AD B2C. |

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

Następujące ustawienia mogą służyć do konfigurowania generowania i obsługi kodu:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Nie | Czas (w sekundach) do wygaśnięcia kodu. Minimum: `60`; Wartość maksymalna: `1200`; Wartość domyślna: `600`. |
| CodeLength | Nie | Długość kodu. Wartością domyślną jest `6`. |
| CharacterSet | Nie | Zestaw znaków dla kodu sformatowany do użycia w wyrażeniu regularnym. Na przykład `a-z0-9A-Z`. Wartością domyślną jest `0-9`. Zestaw znaków musi zawierać co najmniej 10 różnych znaków w określonym zestawie. |
| NumRetryAttempts | Nie | Liczba prób weryfikacji przed kodem jest uznawana za nieprawidłową. Wartością domyślną jest `5`. |
| Operacja | Yes | Operacja do wykonania. Możliwe wartości: `GenerateCode`lub `VerifyCode`. |
| ReuseSameCode | Nie | Czy należy określić zduplikowany kod zamiast generować nowy kod, gdy dany kod nie wygasł i jest nadal ważny. Wartością domyślną jest `false`. |

### <a name="returning-error-message"></a>Zwracany komunikat o błędzie

Nie jest zwracany komunikat o błędzie dla trybu generowania kodu.

### <a name="example"></a>Przykład

Poniższy przykład `TechnicalProfile` jest używany do generowania kodu:

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

## <a name="verify-code"></a>Weryfikuj kod

Drugi tryb tego profilu technicznego polega na sprawdzeniu kodu. Poniżej przedstawiono opcje, które można skonfigurować dla tego trybu.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń wymaganych do wysłania do dostawcy protokołu hasła jednorazowego. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną poniżej.

| ClaimReferenceId | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Yes | Identyfikator identyfikujący użytkownika, który wcześniej wygenerował kod. Jest on często używany jako identyfikator miejsca docelowego, do którego został dostarczony kod, na przykład adres e-mail lub numer telefonu. |
| otpToVerify | Yes | Kod weryfikacyjny dostarczony przez użytkownika. |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do dostawcy protokołu hasła jednorazowego.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Brak oświadczeń wyjściowych dostarczonych podczas weryfikacji kodu tego dostawcy protokołu.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

Poniższe ustawienia mogą służyć do konfigurowania komunikatu o błędzie wyświetlanego podczas niepowodzenia weryfikacji kodu:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nie | Komunikat, który ma być wyświetlany użytkownikowi, jeśli sesja weryfikacji kodu wygasła. To albo kod wygasł albo kod nigdy nie został wygenerowany dla danego identyfikatora. |
| UserMessageIfMaxRetryAttempted | Nie | Komunikat, który ma być wyświetlany użytkownikowi w przypadku przekroczenia maksymalnej dozwolonej liczby prób weryfikacji. |
| UserMessageIfInvalidCode | Nie | Komunikat, który ma być wyświetlany użytkownikowi, jeśli podał nieprawidłowy kod. |

### <a name="returning-error-message"></a>Zwracany komunikat o błędzie

Zgodnie z opisem w temacie [Metadata](#metadata)można dostosować komunikat o błędzie wyświetlany użytkownikowi dla różnych przypadków błędów. Możesz również lokalizować te komunikaty, wprowadzając prefiksy ustawień regionalnych, na przykład:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Przykład

Poniższy przykład `TechnicalProfile` służy do sprawdzania kodu:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby uzyskać przykład użycia jednorazowego profilu technial haseł z weryfikacją niestandardowej wiadomości e-mail:

- [Niestandardowa Weryfikacja poczty e-mail w Azure Active Directory B2C](custom-email.md)

