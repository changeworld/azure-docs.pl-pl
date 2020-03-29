---
title: Przykładowe zasady zarządzania interfejsami API — wysyłanie błędów do stackify do rejestrowania
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak dodać zasady rejestrowania błędów, aby wysłać błędy do Stackify do rejestrowania..
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442440"
---
# <a name="send-errors-to-stackify-for-logging"></a>Wysyłanie błędów do Stackify do rejestrowania

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak dodać zasady rejestrowania błędów, aby wysłać błędy do Stackify do rejestrowania. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **on-error.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

