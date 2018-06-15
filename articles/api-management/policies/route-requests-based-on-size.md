---
title: Azure próbce interfejsu API zarządzania zasad - trasy żądania na podstawie rozmiaru jego treści | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak kierować żądania na podstawie rozmiaru ich jednostki.
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
ms.openlocfilehash: da21a7c83a61b618646c4c4d2f422756b04dc300
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934152"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Trasy żądania na podstawie rozmiaru jego treści

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API Azure, który demonstruje sposób kierowania żądań na podstawie rozmiaru ich jednostki. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

