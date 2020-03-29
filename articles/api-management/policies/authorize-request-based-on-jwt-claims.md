---
title: Przykładowe zasady zarządzania interfejsami API — autoryzowanie dostępu na podstawie oświadczeń JWT
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak autoryzować dostęp do określonych metod HTTP w interfejsie API na podstawie oświadczeń JWT.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422177"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoryzacja dostępu na podstawie oświadczeń JWT

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który pokazuje, jak autoryzować dostęp do określonych metod HTTP w interfejsie API na podstawie oświadczeń JWT. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

