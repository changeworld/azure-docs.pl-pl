---
title: Azure próbce interfejsu API zarządzania zasad - X-CSRF implementacji wzorca | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak zaimplementować wzorzec X CSRF używany przez wiele interfejsów API. W tym przykładzie jest specyficzne dla programu SAP bramy.
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
ms.openlocfilehash: 71e76b234b614f5935775d5c387c9897e1dcb968
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937676"
---
# <a name="implement-x-csrf-pattern"></a>Implementowanie X-CSRF wzorca

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API usługi Azure, który demonstruje sposób wykonania X CSRF wzorzec używany przez wiele interfejsów API. W tym przykładzie jest specyficzne dla programu SAP bramy. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

