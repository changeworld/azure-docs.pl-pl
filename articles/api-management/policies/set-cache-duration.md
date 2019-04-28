---
title: Przykład zasad zarządzania Azure interfejsu API — ustaw czas odpowiedzi w pamięci podręcznej | Dokumentacja firmy Microsoft
description: Przykład zasady zarządzania Azure interfejsu API — pokazuje, jak ustawić czas trwania pamięci podręcznej odpowiedź przy użyciu wartości maxAge w nagłówku Cache-Control wysłane przez wewnętrznej bazy danych...
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
ms.openlocfilehash: 042fab72da2d4b890314b6ee9c7237241b492fba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859162"
---
# <a name="set-response-cache-duration"></a>Ustaw czas buforowania odpowiedzi

W tym artykule przedstawiono przykładowy zasady zarządzania interfejsem API usługi Azure, który demonstruje, jak ustawić czas trwania pamięci podręcznej odpowiedź przy użyciu wartości maxAge w nagłówku Cache-Control wysłane przez zaplecze. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

