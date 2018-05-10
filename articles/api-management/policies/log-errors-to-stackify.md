---
title: Azure próbce interfejsu API zarządzania zasad — błędy wysyłania do Stackify rejestrowania | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - przedstawiono sposób dodawania zasady rejestrowania błędów wysłać błędy do Stackify do rejestrowania.
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
ms.openlocfilehash: e47a3736e814229e881a314c7cb05054369db1f0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="send-errors-to-stackify-for-logging"></a>Wysyłanie błędów do Stackify logowania

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API platformy Azure, który demonstruje sposób dodania zasad rejestrowania błędów wysłać błędy do Stackify do rejestrowania. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **na błąd** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

