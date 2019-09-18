---
title: Przykłady transformacji oświadczeń w usłudze StringCollection dla schematu struktury obsługi tożsamości Azure Active Directory B2C | Microsoft Docs
description: Przykłady transformacji oświadczeń w usłudze StringCollection dla schematu struktury obsługi tożsamości Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9add75b8922fe958fc348fb2a6dd48a7b300eade
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063308"
---
# <a name="stringcollection-claims-transformations"></a>Przekształcenia oświadczeń w programie StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń kolekcji ciągów oświadczeń w schemacie platformy Identity Experience w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Dodaje do nowego żądania StringCollection wyrażenie.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | elementów | ciąg | Wartość oświadczenia, która ma zostać dodana do oświadczenia wyjściowego. |
| Oświadczenie inputclaim | kolekcja | stringCollection | Obowiązkowe Jeśli ta wartość jest określona, transformacja oświadczeń Kopiuje elementy z tej kolekcji i dodaje element na końcu oświadczenia kolekcji wyjściowej. |
| Oświadczenie outputclaim | kolekcja | stringCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. |

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
| Oświadczenie inputclaim | kolekcja | stringCollection | Obowiązkowe Jeśli ta wartość jest określona, transformacja oświadczeń Kopiuje elementy z tej kolekcji i dodaje element na końcu oświadczenia kolekcji wyjściowej. |
| InputParameter | elementów | ciąg | Wartość, która ma zostać dodana do żądania danych wyjściowych. |
| Oświadczenie outputclaim | kolekcja | stringCollection | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. |

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
| Oświadczenie inputclaim | kolekcja | stringCollection | Elementy Claims, które są używane przez transformację oświadczeń do pobrania elementu. |
| Oświadczenie outputclaim | extractedItem | ciąg | Oświadczenia są tworzone po wywołaniu tego ClaimsTransformation. Pierwszy element w kolekcji. |

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

