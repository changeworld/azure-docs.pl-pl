---
title: Przykłady transformacji oświadczeń całkowitych dla schematu struktury środowiska tożsamości Azure Active Directory B2C | Microsoft Docs
description: Przykłady transformacji oświadczeń całkowitych dla schematu struktury środowiska tożsamości Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064214"
---
# <a name="integer-claims-transformations"></a>Przekształcenia oświadczeń całkowitych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń całkowitej liczby oświadczeń w schemacie platformy Identity Experience w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konwertuje typ danych long na ciąg.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | long | Element ClaimType do przekonwertowania na ciąg. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

W tym przykładzie, `numericUserId` element Claim z typem wartości Long jest konwertowany na element `UserId` Claim z typem wartości String.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: 12334 (Long)
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: "12334" (ciąg)

