---
title: Przykładowe zasady zarządzania interfejsami API — autoryzowanie żądania przy użyciu zewnętrznego autoryzatora
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak autoryzować żądania przy użyciu zewnętrznego autoryzującego hermetyzując niestandardową lub starszą logikę uwierzytelniania/autoryzacji.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442495"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzacja żądań za pomocą zewnętrznego obiektu autoryzującego

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak zabezpieczyć dostęp do interfejsu API przy użyciu zewnętrznego autoryzującego hermetyzujący niestandardowe uwierzytelniania/autoryzacji logiki. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)
