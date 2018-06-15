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
ms.openlocfilehash: 46ffe21b31597f889b411a40a9f3e1f2dd76f1ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933466"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autoryzacja dostępu za pomocą tokenu Google OAuth

W tym artykule przedstawiono przykładowe zasady zarządzania interfejsu API Azure, który demonstruje sposób autoryzowania dostępu do punktów końcowych przy użyciu usługi Google jako dostawcy tokenu OAuth. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

