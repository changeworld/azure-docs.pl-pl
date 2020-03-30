---
title: Przykłady transformacji oświadczeń logicznych dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń logicznych dla schematu struktury ief (Identity Experience Framework) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea3b273070702144d5296d07cb8712da044819a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471969"
---
# <a name="boolean-claims-transformations"></a>Przekształcenia oświadczeń logicznych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń logicznych schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims ( AndClaims )

Wykonuje i operacji dwóch danych wejściowych logicznychKlaims i ustawia outputClaim z wynikiem operacji.

| Element  | TransformClaimType (Typ transformacji)  | Typ danych  | Uwagi |
|-------| ------------------------ | ---------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim1 | wartość logiczna | Pierwszy ClaimType do oceny. |
| InputClaim (własnach wejściowych) | inputClaim2  | wartość logiczna | Drugi ClaimType do oceny. |
|WynikClaim | outputClaim | wartość logiczna | ClaimTypes, które zostaną wyprodukowane po tej transformacji oświadczeń został wywołany (true lub false). |

Następująca transformacja oświadczeń pokazuje, jak i dwa `isEmailNotExist`wartości `isSocialAccount`logiczne ClaimTypes: , i . Oświadczenie `presentEmailSelfAsserted` wyjściowe jest `true` ustawione na wartość, `true`jeśli wartość obu oświadczeń wejściowych są . W kroku aranżacji można użyć warunku wstępnego, aby wstępnie skonfigurować stronę z własnym potwierdzeniem, tylko wtedy, gdy adres e-mail konta społecznościowego jest pusty.

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

- Oświadczenia wejściowe:
    - **inputClaim1**: prawda
    - **inputClaim2**: false
- Oświadczenia wyjściowe:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Sprawdza, czy wartości logiczne dwóch oświadczeń są równe i zgłasza wyjątek, jeśli nie są.

| Element | TransformClaimType (Typ transformacji)  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | wartość logiczna | ClaimType do potwierdzenia. |
| Inputparameter |wartośćToCompareTo | wartość logiczna | Wartość do porównania (true lub false). |

**AssertBooleanClaimIsEqualToValue** roszczeń transformacja jest zawsze wykonywane z [sprawdzania poprawności profilu technicznego,](validation-technical-profile.md) który jest [wywoływany](self-asserted-technical-profile.md)przez samodzielnie twierdził profil techniczny . **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** samodzielnie potwierdzone metadane profilu technicznego steruje komunikatem o błędzie, który profil techniczny przedstawia użytkownikowi. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual wykonanie](./media/boolean-transformations/assert-execution.png)

Następująca transformacja oświadczeń pokazuje, jak sprawdzić wartość wartości `true` typu oświadczeń logicznych z wartością. Jeśli wartość `accountEnabled` ClaimType jest false, komunikat o błędzie jest generowany.

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


Profil `login-NonInteractive` techniczny sprawdzania `AssertAccountEnabledIsTrue` poprawności wywołuje transformację oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samodzielnie potwierdzony profil techniczny wywołuje sprawdzanie poprawności **login-NonInteractive** profil techniczny.

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

- Oświadczenia wejściowe:
    - **inputClaim**: false
    - **valueToCompareTo**: prawda
- Wynik: Błąd rzucony

## <a name="comparebooleanclaimtovalue"></a>PorównajBooleanClaimToValue

Sprawdza, czy wartość logiczna oświadczenia `true` `false`jest równa lub , i zwraca wynik kompresji.

| Element | TransformClaimType (Typ transformacji)  | Typ danych  | Uwagi |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | wartość logiczna | ClaimType do potwierdzenia. |
| Inputparameter |wartośćToCompareTo | wartość logiczna | Wartość do porównania (true lub false). |
| WynikClaim | porównajWysoki | wartość logiczna | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |


Następująca transformacja oświadczeń pokazuje, jak sprawdzić wartość wartości `true` typu oświadczeń logicznych z wartością. Jeśli wartość `IsAgeOver21Years` claimtype jest równa `true`, transformacja `true`oświadczeń `false`zwraca , w przeciwnym razie .

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputClaim**: false
- Parametry wejściowe:
    - **valueToCompareTo**: prawda
- Oświadczenia wyjściowe:
    - **compareResult**: false



## <a name="notclaims"></a>NotClaims ( NotClaims )

Wykonuje operację Nie danych wejściowychClaim i ustawia outputClaim z wynikiem operacji.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | wartość logiczna | Roszczenie do eksploatacji. |
| WynikClaim | outputClaim | wartość logiczna | ClaimTypes, które są tworzone po tym ClaimsTransformation został wywołany (true lub false). |

Ta transformacja oświadczeń służy do wykonywania logicznego negacji oświadczenia.

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

- Oświadczenia wejściowe:
    - **inputClaim**: false
- Oświadczenia wyjściowe:
    - **outputClaim**: prawda

## <a name="orclaims"></a>Okręg wyborczy OrClaims

Oblicza lub z dwóch danych wejściowych logicznychKlaims i ustawia outputClaim z wynikiem operacji.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim1 | wartość logiczna | Pierwszy ClaimType do oceny. |
| InputClaim (własnach wejściowych) | inputClaim2 | wartość logiczna | Drugi ClaimType do oceny. |
| WynikClaim | outputClaim | wartość logiczna | ClaimTypes, które zostaną wyprodukowane po tym ClaimsTransformation został wywołany (true lub false). |

Następujące przekształcenia oświadczeń `Or` pokazuje, jak dwa wartości logiczne ClaimTypes. W kroku aranżacji można użyć warunku wstępnego, aby wstępnie skonfigurować stronę z własnym `true`potwierdzeniem, jeśli wartością jednego z oświadczeń jest .

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

- Oświadczenia wejściowe:
    - **inputClaim1**: prawda
    - **inputClaim2**: false
- Oświadczenia wyjściowe:
    - **outputClaim**: prawda
