---
title: Zasady zarządzania interfejsu API Azure przykładowe — Dodawanie funkcji do usługi zaplecza | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak dodawanie funkcji do usługi zaplecza. Na przykład zaakceptuj nazwę miejsca zamiast współrzędne geograficzne w prognozie pogody interfejsu API.
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
ms.openlocfilehash: 3ae168473b4fa603afaf3dcf42516687b4dde33e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="add-capabilities-to-a-backend-service"></a>Dodawanie funkcji do usługi zaplecza

W tym artykule przedstawiono przykład zasady zarządzania interfejsu API usługi Azure, który demonstruje sposób dodawania funkcji do usługi zaplecza. Na przykład zaakceptuj nazwę miejsca zamiast współrzędne geograficzne w prognozie pogody interfejsu API. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

