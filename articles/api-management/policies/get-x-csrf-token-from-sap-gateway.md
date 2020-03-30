---
title: Przykład zasad zarządzania interfejsami API platformy Azure — implementowanie wzorca X-CSRF | Dokumenty firmy Microsoft
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak zaimplementować wzorzec X-CSRF używany przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP.
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
ms.openlocfilehash: 14ea9113bf5712d6ffce356d02abb7224c21771a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067725"
---
# <a name="implement-x-csrf-pattern"></a>Implementowanie wzorca X-CSRF

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak zaimplementować wzorzec X-CSRF używany przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

