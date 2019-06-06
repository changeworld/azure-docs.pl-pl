---
title: ClaimsTransformations — usługa Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definicja elementu ClaimsTransformations w tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c904ac9d4c59a467dd8402ec44682c3cbd03fd8d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511528"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** element zawiera listę funkcji transformacji roszczenia, używane w podróży użytkownika w ramach [zasad niestandardowych](active-directory-b2c-overview-custom.md). Przekształcanie oświadczeń konwertuje danego oświadczenia na inny. W przekształcania oświadczeń należy określić metody transformacji, na przykład dodanie elementu do kolekcji ciągów lub zmienianie wielkości liter w ciągu.

Aby dołączyć listę funkcji transformacji roszczenia, które mogą być używane w podróży użytkownika, — element ClaimsTransformations XML musi być zadeklarowana w sekcji BuildingBlocks zasad.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation** element zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- |-------- | ----------- |
| Identyfikator |Yes | Identyfikator, który jest używany do jednoznacznego identyfikowania przekształcania oświadczeń. Identyfikator jest wywoływany przez inne elementy XML w zasadach. |
| TransformationMethod | Tak | Metodę przekształcenia do przekształcania oświadczeń. Każde przekształcenie oświadczeń ma własne wartości. Zobacz [odwołanie do przekształcania oświadczeń](#claims-transformations-reference) pełną listę dostępnych wartości. |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** element zawiera następujące elementy:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>                
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Wystąpienia | Opis |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Lista **oświadczenie InputClaim** elementy, które określają typy oświadczeń, które są wykonywane jako dane wejściowe do przekształcania oświadczeń. Każdy z tych elementów zawiera odwołanie do typu oświadczenia już zdefiniowane w sekcji ClaimsSchema w zasadach. |
| InputParameters | 0:1 | Lista **InputParameter** elementy, które są dostarczane jako dane wejściowe do przekształcania oświadczeń.  
| OutputClaims | 0:1 | Lista **oświadczenie OutputClaim** elementy, które określają typy oświadczeń, które są tworzone po wywołaniu ClaimsTransformation. Każdy z tych elementów zawiera odwołanie do typu oświadczenia już zdefiniowane w sekcji ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

**InputClaims** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| Oświadczenie InputClaim | 1: n | Typ oświadczenia oczekiwanych danych wejściowych. |

#### <a name="inputclaim"></a>Oświadczenie InputClaim

**Oświadczenie InputClaim** element zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Yes | Odwołanie do typu oświadczenia już zdefiniowane w sekcji ClaimsSchema w zasadach. |
| TransformationClaimType |Yes | Typ oświadczenia identyfikator, aby odwoływać się do przekształcenia. Każde przekształcenie oświadczeń ma własne wartości. Zobacz [odwołanie do przekształcania oświadczeń](#claims-transformations-reference) pełną listę dostępnych wartości. |

### <a name="inputparameters"></a>InputParameters

**InputParameters** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | Oczekiwanym parametrem wejściowym. |

#### <a name="inputparameter"></a>InputParameter

| Atrybut | Wymagane |Opis |
| --------- | ----------- |----------- |
| Identyfikator | Yes | Identyfikator, który jest odwołaniem do parametru metody przekształcania oświadczeń. Każda metoda przekształcania oświadczeń ma własne wartości. Znajdują się w tabeli przekształcania oświadczeń, aby uzyskać pełną listę dostępnych wartości. |
| DataType | Tak | Typ danych parametru, takie jak ciąg, wartość logiczna, Int lub daty/godziny zgodnie z wyliczenia typu danych w schemacie XML zasad niestandardowych. Ten typ służy do wykonywania operacji arytmetycznych poprawnie. Każde przekształcenie oświadczeń ma własne wartości. Zobacz [odwołanie do przekształcania oświadczeń](#claims-transformations-reference) pełną listę dostępnych wartości. |
| Wartość | Yes | Wartość, która jest przekazywana verbatim do przekształcania. Niektóre wartości są dowolne, niektóre z nich, możesz wybrać spośród metoda przekształcania oświadczeń. |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** element zawiera następującego elementu:

| Element | Wystąpienia | Opis |
| ------- | ----------- | ----------- |
| oświadczenie outputClaim | 0: n | Typ oświadczenia oczekiwanych danych wyjściowych. |

#### <a name="outputclaim"></a>oświadczenie outputClaim 

**Oświadczenie OutputClaim** element zawiera następujące atrybuty:

| Atrybut |Wymagane | Opis |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Tak | Odwołanie do typu oświadczenia już zdefiniowane w sekcji ClaimsSchema w zasadach.
| TransformationClaimType | Tak | Typ oświadczenia identyfikator, aby odwoływać się do przekształcenia. Każde przekształcenie oświadczeń ma własne wartości. Zobacz [odwołanie do przekształcania oświadczeń](#claims-transformations-reference) pełną listę dostępnych wartości. |
 
W przypadku danych wejściowych oświadczeń i oświadczeń danych wyjściowych tego samego typu (ciąg lub atrybut typu wartość logiczna), to samo oświadczenie wejściowe można użyć jako oświadczeń wychodzących. W tym przypadku przekształcania oświadczeń zmienia oświadczeń przychodzących z wartością danych wyjściowych.

## <a name="example"></a>Przykład

Na przykład mogą być przechowywane ostatnią wersję warunków użytkowania usługi, które są akceptowane przez użytkownika. Po zaktualizowaniu warunki usług możesz poprosić użytkowników o zaakceptowanie nowej wersji. W poniższym przykładzie **HasTOSVersionChanged** przekształcania oświadczeń porównuje wartość **TOSVersion** oświadczenie z wartością **LastTOSAcceptedVersion**oświadczenia, a następnie zwraca wartość logiczna **TOSVersionChanged** oświadczenia.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Odwołanie do przekształcenia oświadczeń

Aby zapoznać się z przykładami przekształcenia oświadczeń zobacz następujące strony odwołania:

- [Wartość logiczna](boolean-transformations.md)
- [Data](date-transformations.md)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [Ogólne](general-transformations.md)
- [Konta społecznościowego](social-transformations.md)
- [Ciąg](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

