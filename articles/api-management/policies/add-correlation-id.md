---
title: Przykładowe zasady zarządzania interfejsu API platformy Azure — Dodaj nagłówek zawierający identyfikator korelacji | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak dodać nagłówek zawierający identyfikator korelacji żądania przychodzącego.
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
ms.openlocfilehash: 68f42124369194124ae1f8ebb93834a5be4e0128
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287378"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Dodaj nagłówek zawierający identyfikator korelacji

W tym artykule przedstawiono przykład zasad interfejsu API Azure zarządzania, który pokazuje, jak dodać nagłówek zawierający identyfikator korelacji żądania przychodzącego. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

