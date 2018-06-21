---
title: Zasady zarządzania interfejsu API Azure przykładowe — autoryzować żądania przy użyciu zewnętrznego autoryzujący | Dokumentacja firmy Microsoft
description: Pokazuje przykładowe zasady zarządzania Azure API — jak autoryzować żądania przy użyciu zewnętrznego autoryzujący hermetyzując logiki uwierzytelniania/autoryzacji niestandardowej lub starszej wersji.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 7d172a40f2aad65b595026fc656634060a1f7193
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284876"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzować żądania przy użyciu zewnętrznego autoryzujący

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API platformy Azure, który demonstruje sposób bezpieczny dostęp do interfejsu API za pomocą zewnętrznego autoryzujący hermetyzując uwierzytelniania/autoryzacji niestandardowej logiki. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)