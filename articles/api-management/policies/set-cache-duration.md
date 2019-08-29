---
title: Przykładowy czas trwania pamięci podręcznej odpowiedzi dla zasad usługi Azure API Management Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak ustawić czas trwania pamięci podręcznej odpowiedzi przy użyciu wartości maxAge w nagłówku kontroli pamięci podręcznej wysłanej przez zaplecze.
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
ms.openlocfilehash: 67d2f334e2088d96543fabd3b99cac71d95630eb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071970"
---
# <a name="set-response-cache-duration"></a>Ustaw czas trwania pamięci podręcznej odpowiedzi

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak ustawić czas trwania pamięci podręcznej odpowiedzi przy użyciu wartości maxAge w nagłówku kontroli pamięci podręcznej wysłanej przez zaplecze. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

