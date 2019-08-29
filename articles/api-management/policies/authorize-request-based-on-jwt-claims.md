---
title: Przykład zasad usługi Azure API Management — Autoryzuj dostęp oparty na oświadczeniach JWT | Microsoft Docs
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
ms.openlocfilehash: dd99d9ed3eebe6ada60511b3f16c53b0d57a65d6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067808"
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

