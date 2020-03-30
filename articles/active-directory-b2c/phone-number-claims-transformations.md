---
title: Zmiany dotyczące numeru telefonu w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Niestandardowe odwołanie do zasad dla numeru telefonu twierdzi przekształcenia w usłudze Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183939"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definiowanie przekształceń oświadczeń dotyczących numerów telefonów w usłudze Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera odwołania i przykłady dotyczące używania przekształceń oświadczeń o numerach telefonów schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji na temat przekształceń oświadczeń w ogóle, zobacz [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Konwertuje `phoneNumber` typ danych `string` na typ danych.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | phoneNumber | phoneNumber |  ClaimType do konwersji na ciąg. |
| WynikClaim | phoneNumberString | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. |

W tym przykładzie cellPhoneNumber oświadczenie z `phoneNumber` typem wartości jest konwertowane `string`na oświadczenie cellPhone z typem wartości .

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **numer telefonu:**+11234567890 (numer telefonu)
- Oświadczenia wyjściowe:
  - **phoneNumberString**: +11234567890 (ciąg)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Ta transformacja oświadczeń sprawdza poprawność formatu numeru telefonu. Jeśli jest w prawidłowym formacie, zmień go na standardowy format używany przez usługę Azure AD B2C. Jeśli podany numer telefonu nie jest w prawidłowym formacie, zwracany jest komunikat o błędzie.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | phoneNumberString | ciąg |  Oświadczenie o ciągu dla numeru telefonu. Numer telefonu musi być w formacie międzynarodowym, wraz z wiodącym kodem "+" i kodem kraju. Jeśli podane `country` jest oświadczenie wejściowe, numer telefonu jest w formacie lokalnym (bez kodu kraju). |
| InputClaim (własnach wejściowych) | country | ciąg | [Opcjonalnie] Oświadczenie o ciągu dla kodu kraju numeru telefonu w formacie ISO3166 (dwuliterowy kod kraju ISO-3166). |
| WynikClaim | outputClaim | phoneNumber | Wynik tego przekształcania oświadczeń. |

**Transformacja roszczeń ConvertStringToMberNumberClaim** jest zawsze wykonywana z [profilu technicznego sprawdzania poprawności,](validation-technical-profile.md) który jest wywoływany przez [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) lub [formant wyświetlania.](display-controls.md) **UserMessageIfClaimsTransformationInvalidPhoneNumber** samodzielnie potwierdzone metadane profilu technicznego kontroluje komunikat o błędzie, który jest wyświetlany użytkownikowi.

![Diagram ścieżki wykonywania komunikatu o błędzie](./media/phone-authentication/assert-execution.png)

Można użyć tej transformacji oświadczeń, aby upewnić się, że podane oświadczenie ciągu jest prawidłowy numer telefonu. Jeśli nie, zostanie wyświetlony komunikat o błędzie. Poniższy przykład sprawdza, **czy phoneString** ClaimType jest rzeczywiście prawidłowy numer telefonu, a następnie zwraca numer telefonu w standardowym formacie usługi Azure AD B2C. W przeciwnym razie zostanie zgłoszony komunikat o błędzie.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Samodzielnie potwierdzony profil techniczny, który wywołuje profil techniczny sprawdzania poprawności, który zawiera tę transformację oświadczeń, może zdefiniować komunikat o błędzie.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Przykład 1

- Oświadczenia wejściowe:
  - **phoneNumberString**: 033 456-7890
  - **kraj**: DK
- Oświadczenia wyjściowe:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Przykład 2

- Oświadczenia wejściowe:
  - **phoneNumberString**: +1 (123) 456-7890
- Oświadczenia wyjściowe:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Spowoduje to wyodrębnianie kodu kraju i numeru krajowego z oświadczenia wejściowego i opcjonalnie zgłasza wyjątek, jeśli podany numer telefonu jest nieprawidłowy.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | phoneNumber | ciąg | Oświadczenie ciąg numer telefonu. Numer telefonu musi być w formacie międzynarodowym, wraz z wiodącym kodem "+" i kodem kraju. |
| Inputparameter | rzutExceptionOnFailure | wartość logiczna | [Opcjonalnie] Parametr wskazujący, czy wyjątek jest zgłaszany, gdy numer telefonu jest nieprawidłowy. Wartość domyślna jest false. |
| Inputparameter | typ kodu kraju | ciąg | [Opcjonalnie] Parametr wskazujący typ kodu kraju w oświadczeniu wyjściowym. Dostępne wartości to **CallingCode** (kod połączenia międzynarodowego dla kraju, na przykład +1) lub **ISO3166** (dwuliterowy kod kraju ISO-3166). |
| WynikClaim | numer krajowy | ciąg | Oświadczenie o numerze krajowym numeru telefonu. |
| WynikClaim | countryCode | ciąg | Oświadczenie o ciągu dla kodu kraju numeru telefonu. |


Jeśli **GetNationalNumberAndCountryCodeFromPhoneNumberString** roszczeń transformacja jest wykonywana z [sprawdzania poprawności profilu technicznego,](validation-technical-profile.md) który jest wywoływany przez [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) lub akcję kontroli [wyświetlania,](display-controls.md#display-control-actions)a następnie **UserMessageIfPhoneNumberParseFailure** samodzielnie potwierdzone metadane profilu technicznego kontroluje komunikat o błędzie, który jest prezentowany użytkownikowi.

![Diagram ścieżki wykonywania komunikatu o błędzie](./media/phone-authentication/assert-execution.png)

Możesz użyć tej transformacji oświadczeń, aby podzielić pełny numer telefonu na kod kraju i numer krajowy. Jeśli podany numer telefonu jest nieprawidłowy, można wybrać komunikat o błędzie.

W poniższym przykładzie próbuje się podzielić numer telefonu na numer krajowy i kod kraju. Jeśli numer telefonu jest prawidłowy, numer telefonu zostanie zastąpiony przez numer krajowy. Jeśli numer telefonu jest nieprawidłowy, wyjątek nie zostanie zgłoszony, a numer telefonu nadal ma swoją pierwotną wartość.

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

Samodzielnie potwierdzony profil techniczny, który wywołuje profil techniczny sprawdzania poprawności, który zawiera tę transformację oświadczeń, może zdefiniować komunikat o błędzie.

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
  - **numer telefonu:**+49 (123) 456-7890
- Parametry wejściowe:
  - **throwExceptionOnFailure : false throwExceptionOnFailure**: false throw
  - **countryCodeType**: ISO3166
- Oświadczenia wyjściowe:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Przykład 2

- Oświadczenia wejściowe:
  - **numer telefonu:**+49 (123) 456-7890
- Parametry wejściowe
  - **throwExceptionOnFailure : false throwExceptionOnFailure**: false throw
  - **countryCodeType**: Kod wywołania
- Oświadczenia wyjściowe:
  - **nationalNumber**: 1234567890
  - **countryCode**: +49
