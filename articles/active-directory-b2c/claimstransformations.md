---
title: ClaimsTransformations — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Definicja claimsTransformations elementu w schematu struktury środowiska tożsamości usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a919996d00f8ef3fa00109944b60d53b63d95ff
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529127"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Element ClaimsTransformations** zawiera listę funkcji przekształcania oświadczeń, które mogą być używane w procesach pozyskiwania użytkowników jako część [zasad niestandardowych.](custom-policy-overview.md) Transformacja oświadczeń konwertuje danego oświadczenia na inny. W transformacji oświadczeń określić metodę przekształcania, na przykład dodanie elementu do kolekcji ciągów lub zmiana przypadku ciągu.

Aby uwzględnić listę funkcji przekształcania oświadczeń, które mogą być używane w procesach pozyskiwania użytkowników, element XML claimstransformations musi być zadeklarowany w sekcji BuildingBlocks zasad.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**Element ClaimsTransformation** zawiera następujące atrybuty:

| Atrybut |Wymagany | Opis |
| --------- |-------- | ----------- |
| Identyfikator |Tak | Identyfikator, który jest używany do jednoznacznej identyfikacji transformacji oświadczeń. Identyfikator odwołuje się z innych elementów XML w zasadach. |
| TransformationMethod (Metodyka transformacji) | Tak | Metoda przekształcania do użycia w transformacji oświadczeń. Każda transformacja oświadczeń ma swoje własne wartości. Zobacz [odwołanie do przekształcenia oświadczeń](#claims-transformations-reference) dla pełnej listy dostępnych wartości. |

## <a name="claimstransformation"></a>ReklamacjeTransformacja roszczeń

**Element ClaimsTransformation** zawiera następujące elementy:

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


| Element | Wystąpień | Opis |
| ------- | -------- | ----------- |
| WejściaClaims | 0:1 | Lista **InputClaim** elementów, które określają typy oświadczeń, które są traktowane jako dane wejściowe do przekształcenia oświadczeń. Każdy z tych elementów zawiera odwołanie do ClaimType już zdefiniowane w ClaimsSchema sekcji w zasadach. |
| Inputparameters | 0:1 | Lista **InputParameter** elementów, które są dostarczane jako dane wejściowe do transformacji oświadczeń.
| OutputClaims (Roszczenia wyjściowe) | 0:1 | Lista **OutputClaim** elementów, które określają typy oświadczeń, które są tworzone po ClaimsTransformation został wywołany. Każdy z tych elementów zawiera odwołanie do ClaimType już zdefiniowane w ClaimsSchema sekcji. |

### <a name="inputclaims"></a>WejściaClaims

Element **InputClaims** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| InputClaim (własnach wejściowych) | 1:n | Typ oczekiwanego oświadczenia wejściowego. |

#### <a name="inputclaim"></a>InputClaim (własnach wejściowych)

**InputClaim** Element zawiera następujące atrybuty:

| Atrybut |Wymagany | Opis |
| --------- | ----------- | ----------- |
| Zastrzeżenie rodzaju żądań |Tak | Odwołanie do claimtype już zdefiniowane w ClaimsSchema sekcji w zasadach. |
| TransformClaimType (Typ transformacji) |Tak | Identyfikator do odwoływania się do typu oświadczenia transformacji. Każda transformacja oświadczeń ma swoje własne wartości. Zobacz [odwołanie do przekształcenia oświadczeń](#claims-transformations-reference) dla pełnej listy dostępnych wartości. |

### <a name="inputparameters"></a>Inputparameters

**InputParameters** Element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Inputparameter | 1:n | Oczekiwany parametr wejściowy. |

#### <a name="inputparameter"></a>Inputparameter

| Atrybut | Wymagany |Opis |
| --------- | ----------- |----------- |
| Identyfikator | Tak | Identyfikator, który jest odwołaniem do parametru metody przekształcania oświadczeń. Każda metoda przekształcania oświadczeń ma swoje własne wartości. Zobacz tabelę przekształcania oświadczeń, aby uzyskać pełną listę dostępnych wartości. |
| typ_danych | Tak | Typ danych parametru, takich jak String, Boolean, Int lub DateTime zgodnie z wyliczeniem DataType w schemacie XML zasad niestandardowych. Ten typ jest używany do wykonywania operacji arytmetycznych poprawnie. Każda transformacja oświadczeń ma swoje własne wartości. Zobacz [odwołanie do przekształcenia oświadczeń](#claims-transformations-reference) dla pełnej listy dostępnych wartości. |
| Wartość | Tak | Wartość, która jest przekazywana dosłownie do transformacji. Niektóre wartości są dowolne, niektóre z nich można wybrać z metody przekształcania oświadczeń. |

### <a name="outputclaims"></a>OutputClaims (Roszczenia wyjściowe)

**OutputClaims** element zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| WynikClaim | 0:n | Typ oczekiwanego oświadczenia wyjściowego. |

#### <a name="outputclaim"></a>WynikClaim

**OutputClaim** element zawiera następujące atrybuty:

| Atrybut |Wymagany | Opis |
| --------- | ----------- |----------- |
| Zastrzeżenie rodzaju żądań | Tak | Odwołanie do claimtype już zdefiniowane w ClaimsSchema sekcji w zasadach.
| TransformClaimType (Typ transformacji) | Tak | Identyfikator do odwoływania się do typu oświadczenia transformacji. Każda transformacja oświadczeń ma swoje własne wartości. Zobacz [odwołanie do przekształcenia oświadczeń](#claims-transformations-reference) dla pełnej listy dostępnych wartości. |

Jeśli oświadczenie wejściowe i oświadczenie wyjściowe są tego samego typu (ciąg lub wartość logiczna), można użyć tego samego oświadczenia wejściowego jako oświadczenia wyjściowego. W takim przypadku transformacja oświadczeń zmienia oświadczenie wejściowe z wartością danych wyjściowych.

## <a name="example"></a>Przykład

Na przykład możesz przechowywać ostatnią wersję swoich warunków świadczenia usług, którą użytkownik zaakceptował. Po zaktualizowaniu warunków świadczenia usług można poprosić użytkownika o zaakceptowanie nowej wersji. W poniższym przykładzie **HasTOSVersionChanged** roszczeń transformacji porównuje wartość **oświadczenia TOSVersion** z wartością **LastTOSAcceptedVersion** oświadczenia, a następnie zwraca wartość logiczną **TOSVersionChanged** oświadczenia.

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

## <a name="claims-transformations-reference"></a>Odwołanie do przekształceń oświadczeń

Przykłady przekształceń oświadczeń można znaleźć na następujących stronach odwołania:

- [Wartość logiczna](boolean-transformations.md)
- [Data](date-transformations.md)
- [Liczba całkowita](integer-transformations.md)
- [JSON](json-transformations.md)
- [Numer telefonu](phone-number-claims-transformations.md)
- [Ogólne](general-transformations.md)
- [Konto społecznościowe](social-transformations.md)
- [Ciąg](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

