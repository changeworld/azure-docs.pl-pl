---
title: Interfejs API zarządzania zasad przykładowy Azure — wzorzec implementacji X-CSRF | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — demonstruje sposób implementacji wzorca X CSRF używany przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP.
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
ms.openlocfilehash: 2f4d26702443ef3113dad98cde1d13b292fe657a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306703"
---
# <a name="implement-x-csrf-pattern"></a>Wzorzec implementacji X-CSRF

W tym artykule przedstawiono przykładowy zasady zarządzania interfejsem API usługi Azure, który demonstruje sposób implementacji wzorca X CSRF używany przez wiele interfejsów API. Ten przykład jest specyficzny dla bramy SAP. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

