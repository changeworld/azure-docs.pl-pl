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
ms.openlocfilehash: cce2376c440c233d81198d903709867c5fdca7ec
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839869"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzować żądania przy użyciu zewnętrznego autoryzujący

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API platformy Azure, który demonstruje sposób bezpieczny dostęp do interfejsu API za pomocą zewnętrznego autoryzujący hermetyzując uwierzytelniania/autoryzacji niestandardowej logiki. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)