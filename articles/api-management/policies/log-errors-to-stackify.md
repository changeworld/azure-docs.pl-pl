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
ms.openlocfilehash: 46b6d391d6a1ee569dc27c31a0718b23a120c632
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286722"
---
# <a name="send-errors-to-stackify-for-logging"></a>Wysyłanie błędów do Stackify logowania

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API platformy Azure, który demonstruje sposób dodania zasad rejestrowania błędów wysłać błędy do Stackify do rejestrowania. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **na błąd** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

