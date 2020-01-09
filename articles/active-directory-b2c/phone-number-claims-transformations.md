---
title: Przekształcenia oświadczeń numeru telefonu w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Niestandardowe informacje o zasadach dla przekształceń oświadczeń numeru telefonu w Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c43e3386886456eed0c58fefd0fb1212795db66c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480166"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definiowanie przekształceń oświadczeń numeru telefonu w Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera informacje i przykłady dotyczące korzystania z funkcji obsługi oświadczeń przez numery telefonów w schemacie platformy Identity Experience w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji na temat ogólnie przekształceń oświadczeń, zobacz [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

To stwierdzenie weryfikuje format numeru telefonu. Jeśli jest w prawidłowym formacie, zmień go na format standardowy używany przez Azure AD B2C. Jeśli podany numer telefonu ma nieprawidłowy format, zostanie zwrócony komunikat o błędzie.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | string | Wniosek typu ciąg przekonwertowany z. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | string | Wynik tej transformacji oświadczeń. |

Transformacja oświadczeń **ConvertStringToPhoneNumberClaim** jest zawsze wykonywana z poziomu [profilu technicznego weryfikacji](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md) lub [kontrolkę wyświetlania](display-controls.md). Metadane profilu technicznego **UserMessageIfClaimsTransformationInvalidPhoneNumber** z własnym potwierdzeń są kontrolowane przez komunikat o błędzie wyświetlany użytkownikowi.

![Diagram ścieżki wykonywania komunikatu o błędzie](./media/phone-authentication/assert-execution.png)

Możesz użyć tej transformacji oświadczeń, aby upewnić się, że podane oświadczenie ciągu jest prawidłowym numerem telefonu. Jeśli nie, zostanie zgłoszony komunikat o błędzie. Poniższy przykład sprawdza, czy element ClaimType **phoneString** jest prawidłowym numerem telefonu, a następnie zwraca numer telefonu w standardowym formacie Azure AD B2C. W przeciwnym razie zostanie zgłoszony komunikat o błędzie.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Profil techniczny z własnym potwierdzeniem, który wywołuje profil techniczny weryfikacji, który zawiera tę transformację oświadczeń, może zdefiniować komunikat o błędzie.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **oświadczenie inputclaim**: + 1 (123) 456-7890
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**: + 11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Spowoduje to wyodrębnienie kodu kraju i numeru Narodowego z roszczeń wejściowych i opcjonalnie zgłasza wyjątek, jeśli podany numer telefonu jest nieprawidłowy.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | phoneNumber | string | Ciąg określający numer telefonu. Numer telefonu musi znajdować się w formacie międzynarodowym i kończyć się wiodącym kodem "+" i państwowym. |
| InputParameter | throwExceptionOnFailure | wartość logiczna | Obowiązkowe Parametr wskazujący, czy wyjątek jest zgłaszany, gdy numer telefonu jest nieprawidłowy. Wartość domyślna to false. |
| InputParameter | countryCodeType | string | Obowiązkowe Parametr wskazujący typ kodu kraju w poroście danych wyjściowych. Dostępne wartości to **CallingCode** (międzynarodowy kod wywołujący dla kraju, na przykład + 1) lub **ISO3166** (dwuliterowy kod kraju ISO-3166). |
| Oświadczenie outputclaim | nationalNumber | string | Ciąg określający numer Narodowy numeru telefonu. |
| Oświadczenie outputclaim | countryCode | string | Ciąg jako numer kierunkowy kraju numeru telefonu. |


Jeśli przekształcenie oświadczeń **GetNationalNumberAndCountryCodeFromPhoneNumberString** jest wykonywane z poziomu [profilu technicznego sprawdzania poprawności](validation-technical-profile.md) , który jest wywoływany przez [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) lub [Akcja kontrolki wyświetlania](display-controls.md#display-control-actions), wówczas metadane profilu technicznego **UserMessageIfPhoneNumberParseFailure** z własnym potwierdzeniem są kontrolowane przez komunikat o błędzie wyświetlany użytkownikowi.

![Diagram ścieżki wykonywania komunikatu o błędzie](./media/phone-authentication/assert-execution.png)

Możesz użyć tej transformacji oświadczeń, aby podzielić pełen numer telefonu na kod kraju i numer Narodowy. Jeśli podany numer telefonu jest nieprawidłowy, możesz zgłosić komunikat o błędzie.

Poniższy przykład próbuje podzielić numer telefonu na numer Narodowy i kod kraju. Jeśli numer telefonu jest prawidłowy, numer telefonu zostanie przesłonięty przez numer Narodowy. Jeśli numer telefonu jest nieprawidłowy, wyjątek nie zostanie wygenerowany, a numer telefonu nadal ma oryginalną wartość.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Profil techniczny z własnym potwierdzeniem, który wywołuje profil techniczny weryfikacji, który zawiera tę transformację oświadczeń, może zdefiniować komunikat o błędzie.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Przykład 1

- Oświadczenia wejściowe:
  - Numer **telefonu**: + 49 (123) 456-7890
- Parametry wejściowe:
  - **throwExceptionOnFailure**: FAŁSZ
  - **countryCodeType**: ISO3166
- Oświadczenia wyjściowe:
  - **nationalNumber**: 1234567890
  - **CountryCode**: de

### <a name="example-2"></a>Przykład 2

- Oświadczenia wejściowe:
  - Numer **telefonu**: + 49 (123) 456-7890
- Parametry wejściowe
  - **throwExceptionOnFailure**: FAŁSZ
  - **countryCodeType**: CallingCode
- Oświadczenia wyjściowe:
  - **nationalNumber**: 1234567890
  - **CountryCode**: + 49
