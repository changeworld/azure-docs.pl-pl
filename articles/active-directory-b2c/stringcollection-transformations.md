---
title: Przykłady transformacji oświadczeń w usłudze StringCollection dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń w usłudze StringCollection dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f91db91eff3320691a5979d9453bf515ccd59a2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982300"
---
# <a name="stringcollection-claims-transformations"></a>Przekształcenia oświadczeń w programie StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń kolekcji ciągów oświadczeń w schemacie platformy Identity Experience w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Dodaje do nowego żądania StringCollection wyrażenie.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | elementów | string | Wartość oświadczenia, która ma zostać dodana do oświadczenia wyjściowego. |
| Oświadczenie inputclaim | zbiera | stringCollection | Obowiązkowe Jeśli ta wartość jest określona, transformacja oświadczeń Kopiuje elementy z tej kolekcji i dodaje element na końcu oświadczenia kolekcji wyjściowej. |
| Oświadczenie outputclaim | zbiera | stringCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. |

Ta transformacja oświadczeń służy do dodawania ciągu do nowego lub istniejącego ciągu. Jest on często używany w profilu technicznym usługi **AAD-UserWriteUsingAlternativeSecurityId** . Przed utworzeniem nowego konta społecznościowego transformacja oświadczeń **CreateOtherMailsFromEmail** odczytuje element ClaimType i dodaje wartość do **otherMails** ClaimType.

Następujące przekształcenie oświadczeń dodaje **adres E-mail** ClaimType do **otherMails** oświadczenia.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **Kolekcja**: ["someone@outlook.com"]
  - **element**: "admin@contoso.com"
- Oświadczenia wyjściowe:
  - **Kolekcja**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Dodaje parametr ciągu do nowego żądania ciągucollection.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | zbiera | stringCollection | Obowiązkowe Jeśli ta wartość jest określona, transformacja oświadczeń Kopiuje elementy z tej kolekcji i dodaje element na końcu oświadczenia kolekcji wyjściowej. |
| InputParameter | elementów | string | Wartość, która ma zostać dodana do żądania danych wyjściowych. |
| Oświadczenie outputclaim | zbiera | stringCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. |

Użyj tej transformacji oświadczeń, aby dodać wartość ciągu do nowej lub istniejącej wartości StringCollection. Poniższy przykład dodaje stały adres e-mail (admin@contoso.com) do żądania **otherMails** .

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **Kolekcja**: ["someone@outlook.com"]
- Parametry wejściowe
  - **element**: "admin@contoso.com"
- Oświadczenia wyjściowe:
  - **Kolekcja**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Pobiera pierwszy element z dostarczonej kolekcji ciągów.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | zbiera | stringCollection | Elementy Claims, które są używane przez transformację oświadczeń do pobrania elementu. |
| Oświadczenie outputclaim | extractedItem | string | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. Pierwszy element w kolekcji. |

Poniższy przykład odczytuje **otherMails** i zwraca pierwszy element do roszczeń **e-mail** .

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
  - **Kolekcja**: ["someone@outlook.com", "someone@contoso.com"]
- Oświadczenia wyjściowe:
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Sprawdza, czy typ żądania StringCollection zawiera element

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | stringCollection | Typ zgłoszenia, który ma być przeszukiwany. |
|InputParameter|elementów|string|Wartość do wyszukania.|
|InputParameter|ignoreCase|string|Określa, czy to porównanie ma ignorować wielkość liter podczas porównywania ciągów.|
| Oświadczenie outputclaim | Oświadczenie outputclaim | wartość logiczna | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. Wskaźnik wartości logicznej, jeśli kolekcja zawiera taki ciąg |

Poniższy przykład sprawdza, czy typ żądania `roles` StringCollection zawiera wartość **administratora**.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: ["Reader", "author", "admin"]
- Parametry wejściowe:
    - **element**: "Administrator"
    - **IgnoreCase**: "true"
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: "true"


