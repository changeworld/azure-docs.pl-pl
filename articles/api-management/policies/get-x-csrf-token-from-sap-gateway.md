---
title: Azure próbce interfejsu API zarządzania zasad - X-CSRF implementacji wzorca | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak zaimplementować wzorzec X CSRF używany przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 3a2067836a1488d117dced96f3935f2d1f8b1b48
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946027"
---
# <a name="implement-x-csrf-pattern"></a>Implementowanie X-CSRF wzorca

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API usługi Azure, który demonstruje sposób wykonania X CSRF wzorzec używany przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

