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
ms.openlocfilehash: 077915705c242805d3709b5d52d445288fa5336a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064349"
---
# <a name="date-claims-transformations"></a>Przekształceń oświadczeń dat

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady zastosowania przekształceń oświadczeń dla schematu programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Sprawdza, czy jedno wystąpienie daty i czasu (typu danych String) jest późniejsze niż drugie zdarzenie daty i godziny (typ danych String) i zgłasza wyjątek.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | leftOperand | ciąg | Typ pierwszego wystąpienia, który powinien być późniejszy od drugiego żądania. |
| Oświadczenie inputclaim | rightOperand | ciąg | Typ drugiego zgłoszenia, który powinien być wcześniejszy niż pierwszy z nich. |
| InputParameter | AssertIfEqualTo | boolean | Określa, czy potwierdzenie powinno być przekazywane, jeśli lewy operand jest równy operandowi z prawej strony. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Określa, czy potwierdzenie ma być przekazywane, jeśli brakuje prawego operandu. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Określa liczbę milisekund, które mają być dozwolone między dwiema datami czasu, aby uwzględnić czasy równe (na przykład w przypadku pochylenia zegara). |

Przekształcenie oświadczeń **AssertDateTimeIsGreaterThan** jest zawsze wykonywane z poziomu [weryfikacji profilu technicznego](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md). **DateTimeGreaterThan** z własnym profilem technicznym metadane są kontrolowane komunikat o błędzie, który zostanie wyświetlony przez profil techniczny.

![AssertStringClaimsAreEqual wykonywanie](./media/date-transformations/assert-execution.png)

Poniższy przykład porównuje `currentDateTime` `approvedDateTime` z tym roszczeń. Błąd jest zgłaszany, `currentDateTime` jeśli jest późniejszy `approvedDateTime`niż. Transformacja traktuje wartości jako równe, jeśli są one w ciągu 5 minut (30000 milisekund).

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

`AssertApprovedDateTimeLaterThanCurrentDateTime` Profil techniczny `login-NonInteractive` weryfikacji wywołuje transformację oświadczeń.
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
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Wynika Zgłoszono błąd

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konwertuje **datę oświadczenia daty** na element ClaimType **DateTime** . Transformacja oświadczeń konwertuje format czasu i dodaje 12:00:00 AM do daty.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | date | Wartość oświadczenia do przekonwertowania. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | Data i godzina | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

Poniższy przykład ilustruje konwersję (typ danych daty `dateOfBirth` ) żądania na inne zastrzeżenie `dateOfBirthWithTime` (typ danych DateTime).

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
    - **oświadczenie outputclaim**: 1559347200 (1 czerwca 2019 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Pobierz bieżącą datę i godzinę UTC i Dodaj wartość do elementu ClaimType.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie outputclaim | currentDateTime | Data i godzina | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

* Oświadczenia wyjściowe:
    * **currentDateTime**: 1534418820 (16 sierpnia 2018 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Ustal, czy jedna wartość daty i godziny jest późniejsza, wcześniejsza lub równa innej. Wynik jest nową wartością logiczną typu Boolean, z wartością `true` lub. `false`

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | firstDateTime | Data i godzina | Pierwszy element dateTime, aby porównać, czy jest on wcześniejszy, czy późniejszy niż drugi dateTime. Wartość null zgłasza wyjątek. |
| Oświadczenie inputclaim | secondDateTime | Data i godzina | Druga data/godzina do porównania, czy jest wcześniejsza lub późniejsza niż pierwsza wartość daty i godziny. Wartość zerowa jest traktowana jako bieżąca datetTime. |
| InputParameter | Zakład | ciąg | Jedna z następujących wartości: taka sama, późniejsza niż lub wcześniejsza niż. |
| InputParameter | timeSpanInSeconds | int | Dodaj przedział czasu do pierwszej wartości daty i godziny. |
| Oświadczenie outputclaim | wynik | boolean | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

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
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Parametry wejściowe:
    - **operator**: nowszy niż
    - **timeSpanInSeconds**: 7776000 (90 dni)
- Oświadczenia wyjściowe:
    - **wynik**: prawda
