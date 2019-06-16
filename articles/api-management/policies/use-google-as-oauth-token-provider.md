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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859149"
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

