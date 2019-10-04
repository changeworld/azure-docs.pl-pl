---
title: Przykładowe przekształcenie oświadczeń dla schematu struktury obsługi tożsamości Azure Active Directory B2C | Microsoft Docs
description: Date przykłady transformacji oświadczeń dla schematu struktury obsługi tożsamości Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 13c3f3aaf54bc3fb8ef656b5c1ce227fa70cee0b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936790"
---
# <a name="date-claims-transformations"></a>Przekształceń oświadczeń dat

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady zastosowania przekształceń oświadczeń dla schematu programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Sprawdza, czy jedno wystąpienie daty i czasu (typu danych String) jest późniejsze niż drugie zdarzenie daty i godziny (typ danych String) i zgłasza wyjątek.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | leftOperand | string | Typ pierwszego wystąpienia, który powinien być późniejszy od drugiego żądania. |
| Oświadczenie inputclaim | rightOperand | string | Typ drugiego zgłoszenia, który powinien być wcześniejszy niż pierwszy z nich. |
| InputParameter | AssertIfEqualTo | wartość logiczna | Określa, czy potwierdzenie powinno być przekazywane, jeśli lewy operand jest równy operandowi z prawej strony. |
| InputParameter | AssertIfRightOperandIsNotPresent | wartość logiczna | Określa, czy potwierdzenie ma być przekazywane, jeśli brakuje prawego operandu. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Określa liczbę milisekund, które mają być dozwolone między dwiema datami czasu, aby uwzględnić czasy równe (na przykład w przypadku pochylenia zegara). |

Przekształcenie oświadczeń **AssertDateTimeIsGreaterThan** jest zawsze wykonywane z poziomu [weryfikacji profilu technicznego](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md). **DateTimeGreaterThan** z własnym profilem technicznym metadane są kontrolowane komunikat o błędzie, który zostanie wyświetlony przez profil techniczny.

![AssertStringClaimsAreEqual wykonywanie](./media/date-transformations/assert-execution.png)

Poniższy przykład porównuje `currentDateTime` z wnioskiem o `approvedDateTime`. Błąd jest zgłaszany, jeśli `currentDateTime` jest późniejsza niż `approvedDateTime`. Transformacja traktuje wartości jako równe, jeśli są one w ciągu 5 minut (30000 milisekund).

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

Profil techniczny weryfikacji `login-NonInteractive` wywołuje transformację `AssertApprovedDateTimeLaterThanCurrentDateTime` oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Profil techniczny z własnym potwierdzeniem wywołuje profil techniczny **logowania weryfikacji — nieinteraktywny** .

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
    - **leftOperand**: 2018 r-10-01T15:00:00.0000000 z
    - **rightOperand**: 2018 r-10-01T14:00:00.0000000 z
- Wynik: zgłoszono błąd

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konwertuje **datę oświadczenia daty** na element ClaimType **DateTime** . Transformacja oświadczeń konwertuje format czasu i dodaje 12:00:00 AM do daty.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | date | Wartość oświadczenia do przekonwertowania. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | Datę | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

Poniższy przykład ilustruje konwersję `dateOfBirth` (Data typ danych) do innego żądania `dateOfBirthWithTime` (typ danych dateTime).

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
    - **oświadczenie inputclaim**: 2019-06-01
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: 1559347200 (1 czerwca 2019 12:00:00 am)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Pobierz bieżącą datę i godzinę UTC i Dodaj wartość do elementu ClaimType.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie outputclaim | currentDateTime | Datę | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

* Oświadczenia wyjściowe:
    * **currentDateTime**: 1534418820 (16 sierpnia 2018 11:27:00 am)

## <a name="datetimecomparison"></a>DateTimeComparison

Ustal, czy jedna wartość daty i godziny jest późniejsza, wcześniejsza lub równa innej. Wynikiem jest nowa wartość logiczna typu Boolean, z wartością `true` lub `false`.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | firstDateTime | Datę | Pierwszy element dateTime, aby porównać, czy jest on wcześniejszy, czy późniejszy niż drugi dateTime. Wartość null zgłasza wyjątek. |
| Oświadczenie inputclaim | secondDateTime | Datę | Druga data/godzina do porównania, czy jest wcześniejsza lub późniejsza niż pierwsza wartość daty i godziny. Wartość zerowa jest traktowana jako bieżąca datetTime. |
| InputParameter | Zakład | string | Jedna z następujących wartości: taka sama, późniejsza niż lub wcześniejsza niż. |
| InputParameter | timeSpanInSeconds | int | Dodaj przedział czasu do pierwszej wartości daty i godziny. |
| Oświadczenie outputclaim | Wynika | wartość logiczna | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

Użyj tej transformacji oświadczeń, aby określić, czy dwa oświadczenia są równe, nowsze czy starsze. Na przykład użytkownik może przechowywać ostatnio zaakceptowane warunki użytkowania usług (TOS) przez użytkownika. Po 3 miesiącach możesz polecić użytkownikowi ponowne uzyskanie dostępu do tych organizacji.
Aby uruchomić transformację roszczeń, najpierw musisz uzyskać bieżącą wartość dateTime, a także godzinę, w której użytkownik ostatnio akceptuje te organizacje.

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
    - **firstDateTime**: 2018 r-01-01T00:00:00.100000 z
    - **secondDateTime**: 2018 r-04-01T00:00:00.100000 z
- Parametry wejściowe:
    - **operator**: nowszy niż
    - **timeSpanInSeconds**: 7776000 (90 dni)
- Oświadczenia wyjściowe:
    - **wynik**: prawda
