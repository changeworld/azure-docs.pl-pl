---
title: Interfejs API zarządzania zasad przykładowy Azure — błędy wysyłania do rozwiązania Stackify rejestrowania | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — przedstawiono sposób dodawania zasad rejestrowania błędów wysyłanie błędów do rozwiązania Stackify rejestrowania...
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860542"
---
# <a name="send-errors-to-stackify-for-logging"></a>Wysyłanie błędów do rozwiązania Stackify rejestrowania

W tym artykule przedstawiono przykład zasady zarządzania interfejsem API usługi Azure, przedstawia sposób dodawania zasad rejestrowania błędów wysłać błędy do rozwiązania Stackify do rejestrowania. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **na błąd** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

