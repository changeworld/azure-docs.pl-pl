---
title: Zasady zarządzania interfejsu API Azure przykładowe — autoryzować dostęp za pomocą tokenu Google OAuth | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazuje, jak się autoryzacja dostępu do punktów końcowych przy użyciu usługi Google jako dostawcy tokenu OAuth.
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
ms.openlocfilehash: 22aed976ef69288aa0e49215a739174786843527
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284593"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autoryzacja dostępu za pomocą tokenu Google OAuth

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API Azure, który demonstruje sposób autoryzowania dostępu do punktów końcowych przy użyciu usługi Google jako dostawcy tokenu OAuth. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

