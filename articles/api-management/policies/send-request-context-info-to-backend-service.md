---
title: Azure próbce interfejsu API zarządzania zasad — informacje o kontekście żądania wysłania do usługi zaplecza | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazuje, jak wysłać informacje o kontekście żądania do usługi zaplecza.
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
ms.openlocfilehash: 932555a20ae89581b557d9d9f0a73992205d1e18
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="send-request-context-information-to-the-backend-service"></a>Wyślij informacje o kontekście żądania do usługi zaplecza

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API usługi Azure, który demonstruje sposób wysłać informacje o kontekście żądania do usługi zaplecza. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

