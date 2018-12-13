---
title: Zasady usługi Azure API management przykładowy — autoryzować dostęp za pomocą protokołu Google OAuth token | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — pokazuje, jak do autoryzowania dostępu do punktów końcowych przy użyciu Google jako dostawcę tokenu OAuth.
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
ms.openlocfilehash: 430f9e57df163ad345f0740e5bd5beca6e892a4c
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869309"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autoryzowanie dostępu za pomocą protokołu Google OAuth token

W tym artykule przedstawiono przykładowy zasad zarządzania interfejsem API usługi Azure, który demonstruje sposób autoryzacji dostępu do punktów końcowych przy użyciu Google jako dostawcę tokenu OAuth. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

