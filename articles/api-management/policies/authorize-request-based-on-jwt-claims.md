---
title: Zasady usługi Azure API management przykładowy — Autoryzowanie dostępu opartego na oświadczeniach JWT | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — pokazuje, jak do autoryzacji dostępu do określonej metody HTTP dla interfejsu API opartego na oświadczeniach JWT.
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
ms.openlocfilehash: d656cf7c7bed1d40bbde654f9c2484efcc5df25d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157925"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoryzowanie dostępu opartego na oświadczeniach JWT

W tym artykule przedstawiono przykładowy zasad zarządzania interfejsu API platformy Azure, który demonstruje sposób autoryzacji dostępu do określonej metody HTTP dla interfejsu API opartego na oświadczeniach JWT. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

