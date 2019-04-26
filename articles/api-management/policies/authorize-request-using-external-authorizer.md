---
title: Zasady usługi Azure API management przykładowy — autoryzować żądania przy użyciu zewnętrznego autoryzujący | Dokumentacja firmy Microsoft
description: Przykład zasady zarządzania platformy Azure interfejsu API — pokazuje, jak autoryzować żądania przy użyciu zewnętrznego obiekt autoryzujący, zawieranie logiki niestandardowymi lub starszymi uwierzytelniania/autoryzacji.
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
ms.openlocfilehash: 65ea8622187d0665e4680f4162ddff0bc01e6eb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306770"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autoryzacji żądania za pomocą zewnętrznego autoryzatora

W tym artykule przedstawiono przykładowy zasady zarządzania interfejsem API usługi Azure, który demonstruje, jak zabezpieczyć dostęp do interfejsu API za pomocą zewnętrznego autoryzujący enkapsulacji logiki niestandardowej uwierzytelniania/autoryzacji. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady ograniczeń dostępu](../api-management-access-restriction-policies.md)
+ [Przykłady zasad](../policy-samples.md)
