---
title: Atrybut typu wartość logiczna przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Atrybut typu wartość logiczna oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a08849340d19055a03f85ca401757a81cd2c95d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511738"
---
# <a name="boolean-claims-transformations"></a>Przekształcenia oświadczeń logiczna

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształceń oświadczeń logiczna schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Wykonuje operację And z dwóch inputClaims boolean i ustawia oświadczenie outputClaim z wyniku operacji.

| Element  | TransformationClaimType  | Typ danych  | Uwagi |
|-------| ------------------------ | ---------- | ----- |
| Oświadczenie InputClaim | inputClaim1 | wartość logiczna | Pierwszy typ oświadczenia do oceny. |
| Oświadczenie InputClaim | inputClaim2  | wartość logiczna | Drugi typ oświadczenia do oceny. |
|oświadczenie outputClaim | oświadczenie outputClaim | wartość logiczna | ClaimTypes, które zostaną wygenerowane po tym przekształcania oświadczeń zostało wywołane (true lub false). |

Pokazuje następujące przekształcania oświadczeń jak i dwa ClaimTypes logiczna: `isEmailNotExist`, i `isSocialAccount`. Oświadczeń wychodzących `presentEmailSelfAsserted` ustawiono `true` Jeśli wartość obu oświadczeń wejściowych `true`. W kroku aranżacji umożliwia warunku wstępnego ustawienie wstępne samodzielnie stronie tylko wtedy, gdy wiadomość e-mail z konta społecznościowego jest pusty.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Sprawdza, wartościami logicznymi dwóch oświadczeń są takie same i zgłasza wyjątek, jeśli nie są one.

| Element | TransformationClaimType  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim | wartość logiczna | Element ClaimType oceniane pod. |
| InputParameter |valueToCompareTo | wartość logiczna | Wartość do porównania (true lub false). |

**AssertBooleanClaimIsEqualToValue** przekształcania oświadczeń jest zawsze wykonywana z [profilu technicznego weryfikacji](validation-technical-profile.md) który jest wywoływany [własnym potwierdzone profilu technicznego](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** samodzielnie profilu technicznego określa profil techniczny wyświetlane dla użytkownika komunikat o błędzie.

![AssertStringClaimsAreEqual execution](./media/boolean-transformations/assert-execution.png)

Następujące przekształcania oświadczeń pokazuje, jak sprawdzić wartość logiczna typu oświadczenia, za pomocą `true` wartości. Jeśli wartość `accountEnabled` oświadczenia ma wartość false, generowany jest komunikat o błędzie.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive` Wywołania profilu technicznego weryfikacji `AssertAccountEnabledIsTrue` przekształcania oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samodzielnie profilu technicznego wywołuje weryfikacji **logowania nieinterakcyjnego** profilu technicznego.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **oświadczenie inputClaim**: false
    - **valueToCompareTo**: true
- Wynik: Zgłoszony błąd

## <a name="notclaims"></a>NotClaims

Wykonuje operację nie w logiczne oświadczenie inputClaim i ustawia oświadczenie outputClaim z wyniku operacji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim | wartość logiczna | Oświadczenie, które działały. |
| oświadczenie outputClaim | oświadczenie outputClaim | wartość logiczna | ClaimTypes, które są generowane po tym ClaimsTransformation zostało wywołane (true lub false). |

Użyj tego przekształcania oświadczeń do prowadzenia Negacja logiczna oświadczenia.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **oświadczenie inputClaim**: false
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: true

## <a name="orclaims"></a>OrClaims 

Oblicza lub dwóch inputClaims boolean i ustawia oświadczenie outputClaim z wyniku operacji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputClaim1 | wartość logiczna | Pierwszy typ oświadczenia do oceny. |
| Oświadczenie InputClaim | inputClaim2 | wartość logiczna | Drugi typ oświadczenia do oceny. |
| oświadczenie outputClaim | oświadczenie outputClaim | wartość logiczna | ClaimTypes, które zostaną wygenerowane po tym ClaimsTransformation zostało wywołane (true lub false). |

Pokazuje następujące przekształcania oświadczeń jak `Or` dwóch ClaimTypes boolean. W kroku aranżacji umożliwia warunku wstępnego ustawienie wstępne samodzielnie strony, jeśli wartość oświadczenia jest `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: true

