---
title: Przykładowe zasady zarządzania Azure API - ustawić czas buforowania odpowiedzi | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak ustawić czas buforowania odpowiedzi przy użyciu wartości parametru maxAge nagłówka Cache-Control wysyłane przez zaplecze...
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
ms.openlocfilehash: 8640668ae51c113cc467501b44dbd03b257325c3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="set-response-cache-duration"></a>Ustaw czas buforowania odpowiedzi

W tym artykule przedstawiono przykładowe zasad zarządzania interfejsu API usługi Azure, który demonstruje sposób ustawić czas buforowania odpowiedzi przy użyciu wartości parametru maxAge wysyłane przez zaplecze nagłówka Cache-Control. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

