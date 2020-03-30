---
title: Przykłady przekształcania oświadczeń całkowitych dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji oświadczeń o żądaniu dla schematu struktury ief (Identity Experience Framework) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187682"
---
# <a name="integer-claims-transformations"></a>Przekształcenia oświadczeń całkowitych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń całkowitych schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konwertuje długi typ danych na typ danych ciągu.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | long | ClaimType do konwersji na ciąg. |
| WynikClaim | outputClaim | ciąg | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

W tym przykładzie `numericUserId` oświadczenie z typem wartości long `UserId` jest konwertowane na oświadczenie z typem wartości ciągu.

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
    - **inputClaim**: 12334 (długi)
- Oświadczenia wyjściowe:
    - **outputClaim**: "12334" (ciąg)

