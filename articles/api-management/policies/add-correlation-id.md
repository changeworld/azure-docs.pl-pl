---
title: Przykładowe zasady usługi Azure API management — Dodaj nagłówek zawierający identyfikator korelacji | Dokumentacja firmy Microsoft
description: Przykład zasady zarządzania Azure interfejsu API — pokazuje, jak dodać nagłówek zawierający identyfikator korelacji żądania przychodzącego.
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
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306787"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Dodaj nagłówek zawierający identyfikator korelacji

W tym artykule przedstawiono przykładowy zasad zarządzania interfejsu API platformy Azure, który demonstruje, jak dodać nagłówek zawierający identyfikator korelacji żądania przychodzącego. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

