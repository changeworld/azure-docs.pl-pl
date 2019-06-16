---
title: Właściwości StringCollection przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Właściwości StringCollection oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98453daeb34d093b49cdcc636f68c3d7ae017126
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512436"
---
# <a name="stringcollection-claims-transformations"></a>Właściwości StringCollection oświadczeń przekształcenia

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształcenia oświadczeń kolekcji ciągów schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Dodaje oświadczenie ciąg do nowego oświadczenia stringCollection. 

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Element | string | Oświadczenia, które mają zostać dodane do oświadczeń wychodzących. |
| Oświadczenie InputClaim | Kolekcja | właściwości stringCollection | [Opcjonalnie] Jeśli zostanie określony, przekształcania oświadczeń kopiuje elementy z tej kolekcji, a następnie dodaje element do końca kolekcji oświadczeń wychodzących. |
| oświadczenie outputClaim | Kolekcja | właściwości stringCollection | ClaimTypes, które są generowane po wywołaniu tego ClaimsTransformation. |

Użyj tego przekształcania, aby dodać parametry do nowej lub istniejącej właściwości stringCollection oświadczeń. Często są one wykorzystywane w **AAD UserWriteUsingAlternativeSecurityId** profilu technicznego. Przed utworzeniem nowego konta społecznościowego **CreateOtherMailsFromEmail** przekształcania oświadczeń odczytuje oświadczenia i dodaje wartość do **otherMails** typu oświadczenia. 

Dodaje następujące przekształcania oświadczeń **e-mail** oświadczenia do **otherMails** typu oświadczenia.

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

- Oświadczeń wejściowych:
  - **Kolekcja**: ["someone@outlook.com"]
  - **element**: "admin@contoso.com"
- Oświadczeń danych wyjściowych: 
  - **Kolekcja**: ["someone@outlook.com","admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Dodaje nowe oświadczenie właściwości stringCollection jako parametr ciągu. 

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Kolekcja | właściwości stringCollection | [Opcjonalnie] Jeśli zostanie określony, przekształcania oświadczeń kopiuje elementy z tej kolekcji, a następnie dodaje element do końca kolekcji oświadczeń wychodzących. |
| InputParameter | Element | string | Wartość do dodania do oświadczeń wychodzących. |
| oświadczenie outputClaim | Kolekcja | właściwości stringCollection | ClaimTypes, które zostaną wygenerowane po wywołaniu tego ClaimsTransformation. |

Użyj oświadczeń to przekształcenie, aby dodać do nowej lub istniejącej właściwości stringCollection wartość ciągu. W poniższym przykładzie dodano adres e-mail stałych (admin@contoso.com) do **otherMails** oświadczenia. 

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

- Oświadczeń wejściowych:
  - **Kolekcja**: ["someone@outlook.com"]
- Parametry wejściowe 
  - **element**: "admin@contoso.com"
- Oświadczeń danych wyjściowych:
  - **Kolekcja**: ["someone@outlook.com","admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Pobiera pierwszy element z kolekcji podanego ciągu. 

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Kolekcja | właściwości stringCollection | ClaimTypes, używane przez Przekształcanie oświadczeń można pobrać elementu. |
| oświadczenie outputClaim | extractedItem | string | ClaimTypes, które są generowane po wywołaniu tego ClaimsTransformation. Pierwszy element w kolekcji. |

Poniższy przykład odczytuje **otherMails** oświadczeń i zwraca pierwszy element do **e-mail** oświadczenia. 

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

- Oświadczeń wejściowych:
  - **Kolekcja**: ["someone@outlook.com","someone@contoso.com"]
- Oświadczeń danych wyjściowych: 
  - **extractedItem**: "someone@outlook.com"

