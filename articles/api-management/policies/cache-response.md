---
title: Przykład zasad usługi Azure API Management — Dodawanie możliwości do usługi wewnętrznej bazy danych | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać możliwości do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody.
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
ms.openlocfilehash: 705d7e44f64f8dc3bba669cd80dafdab078fcccc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067772"
---
# <a name="add-capabilities-to-a-backend-service"></a>Dodawanie funkcji do usługi wewnętrznej bazy danych

W tym artykule przedstawiono przykład zasad zarządzania interfejsem API platformy Azure, który pokazuje, jak dodać możliwości do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

