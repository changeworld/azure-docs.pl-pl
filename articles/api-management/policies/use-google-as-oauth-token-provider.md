---
title: Przykładowe zasady zarządzania interfejsami API — autoryzowanie dostępu przy użyciu tokenu Google OAuth
titleSuffix: Azure API Management
description: Przykład zasad zarządzania interfejsami API platformy Azure — pokazuje, jak autoryzować dostęp do punktów końcowych przy użyciu Google jako dostawcy tokenu OAuth.
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
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442370"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autoryzacja dostępu za pomocą tokenu OAuth usługi Google

W tym artykule przedstawiono przykład zasad zarządzania interfejsami API platformy Azure, który pokazuje, jak autoryzować dostęp do punktów końcowych przy użyciu Google jako dostawcy tokenu OAuth. Aby ustawić lub edytować kod zasad, wykonaj czynności opisane w [załączniku Do konfigurowania lub edytowania zasad](../set-edit-policies.md). Aby zobaczyć inne przykłady, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

