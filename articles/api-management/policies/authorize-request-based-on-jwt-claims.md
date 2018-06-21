---
title: Zasady zarządzania interfejsu API Azure przykładowe — autoryzować dostęp na podstawie oświadczeń JWT | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazuje, jak się autoryzacja dostępu do określonej metody HTTP na interfejs API na podstawie oświadczeń JWT.
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
ms.openlocfilehash: 78ea1b51dea0a65aede0e75e3a8c1d424689854e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285377"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoryzacja dostępu na podstawie oświadczeń JWT

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API platformy Azure, który demonstruje sposób autoryzowania dostępu do określonej metody HTTP na interfejs API na podstawie oświadczeń JWT. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

