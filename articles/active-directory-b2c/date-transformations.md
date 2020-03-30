---
title: Przykłady transformacji oświadczeń daty dla zasad niestandardowych
description: Przykłady transformacji oświadczeń daty dla schematu struktury środowiska tożsamości (IEF) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476692"
---
# <a name="date-claims-transformations"></a>Przekształcenia oświadczeń daty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń daty schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Sprawdza, czy jedno oświadczenie daty i godziny (typ danych ciągu) jest późniejsze niż drugie oświadczenie daty i godziny (typ danych ciągu) i zgłasza wyjątek.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | lewicaOperand | ciąg | Typ pierwszego roszczenia, który powinien być późniejszy niż drugie roszczenie. |
| InputClaim (własnach wejściowych) | prawoOperand | ciąg | Typ drugiego roszczenia, który powinien być wcześniejszy niż pierwsze roszczenie. |
| Inputparameter | AssertIfEqualTo | wartość logiczna | Określa, czy to twierdzenie powinno zostać przemijane, jeśli lewy operand jest równy prawemu operandowi. |
| Inputparameter | AssertIfRightOperandIsNotPresent | wartość logiczna | Określa, czy to twierdzenie powinno zostać przemijane, jeśli brakuje odpowiedniego operandu. |
| Inputparameter | TreatAsEqualIfwithinMillseconds | int | Określa liczbę milisekund, aby umożliwić między dwoma godzinami daty, aby uznać czasy równe (na przykład, aby uwzględnić pochylenie zegara). |

**AssertDateTimeIsGreaterThan** roszczeń transformacja jest zawsze wykonywane z [sprawdzania poprawności profilu technicznego,](validation-technical-profile.md) który jest wywoływany przez [samodzielnie potwierdzone profilu technicznego.](self-asserted-technical-profile.md) **DateTimeGreaterThan** samodzielnie potwierdzone metadane profilu technicznego kontroluje komunikat o błędzie, który profil techniczny przedstawia użytkownikowi. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual wykonanie](./media/date-transformations/assert-execution.png)

Poniższy przykład porównuje `currentDateTime` roszczenie `approvedDateTime` z roszczeniem. Błąd jest generowany, `currentDateTime` jeśli `approvedDateTime`jest późniejsza niż . Transformacja traktuje wartości jako równe, jeśli są one w ciągu 5 minut (30000 milisekund) różnica.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Profil `login-NonInteractive` techniczny sprawdzania `AssertApprovedDateTimeLaterThanCurrentDateTime` poprawności wywołuje transformację oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samodzielnie potwierdzony profil techniczny wywołuje sprawdzanie poprawności **login-NonInteractive** profil techniczny.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **leftOperand**: 2020-03-01T15:00:00.0000000Z
    - **rightOperand**: 2020-03-01T14:00:00.0000000Z
- Wynik: Błąd rzucony

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konwertuje typ oświadczenia **daty** na typ oświadczenia **DateTime.** Transformacja oświadczeń konwertuje format czasu i dodaje 12:00:00 DO daty.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | date | Typ oświadczeń do konwersji. |
| WynikClaim | outputClaim | Data i godzina | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

W poniższym przykładzie pokazano `dateOfBirth` konwersję oświadczenia (typ `dateOfBirthWithTime` danych daty) na inne oświadczenie (typ danych dateTime).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputClaim**: 2020-15-03
- Oświadczenia wyjściowe:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Konwertuje **datetime** claimtype do **daty** ClaimType. Transformacja oświadczeń usuwa format godziny z daty.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | Data i godzina | Typ oświadczeń do konwersji. |
| WynikClaim | outputClaim | date | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

W poniższym przykładzie pokazano `systemDateTime` konwersję oświadczenia (typ danych `systemDate` dateTime) na inne oświadczenie (typ danych daty).

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **inputClaim**: 2020-15-03T11:34:22.0000000Z
- Oświadczenia wyjściowe:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Pobierz bieżącą datę i godzinę UTC i dodaj wartość do ClaimType.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| WynikClaim | currentDateTime | Data i godzina | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

* Oświadczenia wyjściowe:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000Z

## <a name="datetimecomparison"></a>DateTimeComparison (DateTimeComparison)

Określ, czy jedna dataTime jest późniejsza, wcześniejsza czy równa innemu. Rezultatem jest nowy logiczny wartość logiczna `true` ClaimType o wartości lub `false`.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | firstDateTime (Czas 1.00) | Data i godzina | Pierwszy dateTime, aby porównać, czy jest wcześniej lub później niż drugi dateTime. Null wartość zgłasza wyjątek. |
| InputClaim (własnach wejściowych) | secondDateTime | Data i godzina | Druga dataTime, aby porównać, czy jest wcześniej lub później niż pierwszy dateTime. Wartość null jest traktowana jako bieżąca datetTime. |
| Inputparameter | operator | ciąg | Jedna z następujących wartości: taka sama, później niż lub wcześniej. |
| Inputparameter | timeSpanInSekundy | int | Dodaj rozpiętość czasu do pierwszego datatime. |
| WynikClaim | wynik | wartość logiczna | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

Ta transformacja oświadczeń służy do określenia, czy dwa ClaimTypes są równe, później lub wcześniej od siebie. Na przykład użytkownik może przechowywać czas, w której użytkownik po raz ostatni zaakceptował warunki korzystania z usług.For example, you may store the last time a user accepted your terms of services (TOS). Po 3 miesiącach możesz poprosić użytkownika o ponowny dostęp do Tos.
Aby uruchomić transformację oświadczeń, najpierw musisz uzyskać bieżącą datęTime, a także ostatni raz użytkownik akceptuje tos.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- Parametry wejściowe:
    - **operator:** później niż
    - **timeSpanInSekundy:** 7776000 (90 dni)
- Oświadczenia wyjściowe:
    - **wynik**: prawda
