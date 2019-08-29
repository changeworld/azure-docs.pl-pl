---
title: Przykład zasad usługi Azure API Management — wysyłanie błędów do rozwiązania Stackify na potrzeby rejestrowania | Microsoft Docs
description: Przykład zasad usługi Azure API Management — pokazuje, jak dodać zasady rejestrowania błędów w celu wysłania błędów do rozwiązania Stackify na potrzeby rejestrowania.
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
ms.openlocfilehash: 82aab34a9815f080fe8abb8c1d8b6de66866806e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067590"
---
# <a name="send-errors-to-stackify-for-logging"></a>Wysyłaj błędy do rozwiązania Stackify na potrzeby rejestrowania

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak dodać zasady rejestrowania błędów w celu wysłania błędów do rozwiązania Stackify na potrzeby rejestrowania. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **on-Error** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

