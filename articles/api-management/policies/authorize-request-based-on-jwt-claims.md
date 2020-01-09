---
title: Przykładowe zasady zarządzania interfejsem API — Autoryzuj dostęp oparty na oświadczeniach JWT
titleSuffix: Azure API Management
description: Przykład zasad usługi Azure API Management — pokazuje, jak autoryzować dostęp do określonych metod HTTP w interfejsie API w oparciu o oświadczenia JWT.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422177"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autoryzuj dostęp oparty na oświadczeniach JWT

W tym artykule przedstawiono przykład zasad usługi Azure API Management, który pokazuje, jak autoryzować dostęp do określonych metod HTTP w interfejsie API opartym na oświadczeniach JWT. Aby ustawić lub edytować kod zasad, wykonaj kroki opisane w sekcji [Ustawianie lub edytowanie zasad](../set-edit-policies.md). Aby zapoznać się z innymi przykładami, zobacz [przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do bloku **przychodzącego** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zasad APIMymi:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

