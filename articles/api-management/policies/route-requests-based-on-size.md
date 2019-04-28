---
title: Interfejs API zarządzania zasad przykładowy Azure — trasy żądania na podstawie rozmiaru jego treści | Dokumentacja firmy Microsoft
description: Przykład zasad zarządzania Azure interfejsu API — pokazuje, jak kierować żądania na podstawie rozmiaru ich jednostki.
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
ms.openlocfilehash: 7656401115cc370d6eee60fb9bddb9bcd92e4201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860948"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Kieruj żądania na podstawie rozmiaru jej treści

W tym artykule przedstawiono przykładowy zasady zarządzania interfejsem API usługi Azure, który demonstruje sposób kierowania żądań na podstawie rozmiaru ich jednostki. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

