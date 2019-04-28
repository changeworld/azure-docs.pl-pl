---
title: Zasady usługi Azure API management przykładowy — Dodawanie funkcji do usługi zaplecza | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — pokazuje, jak dodać funkcje do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody.
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
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062211"
---
# <a name="add-capabilities-to-a-backend-service"></a>Dodawanie funkcji do usługi zaplecza

W tym artykule przedstawiono przykładowy zasady zarządzania interfejsem API usługi Azure, który demonstruje, jak dodać funkcje do usługi zaplecza. Na przykład akceptowania nazwy miejsca zamiast współrzędnych geograficznych w interfejsie API prognozy pogody. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

