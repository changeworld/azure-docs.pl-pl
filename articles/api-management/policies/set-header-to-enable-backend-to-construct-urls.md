---
title: Przykładowe zasady usługi Azure API management — Dodaj nagłówek przesłanym | Dokumentacja firmy Microsoft
description: Przykład zasady zarządzania Azure interfejsu API — pokazuje, jak dodać nagłówek przekazane w żądanie przychodzące, aby umożliwić zaplecza interfejsu API, aby utworzyć odpowiednie adresy URL.
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859498"
---
# <a name="add-a-forwarded-header"></a>Dodaj nagłówek przekazane

W tym artykule przedstawiono przykładowy zasad zarządzania interfejsem API usługi Azure, który demonstruje, jak dodać nagłówek przekazane w żądanie przychodzące, aby umożliwić zaplecza interfejsu API, aby utworzyć odpowiednie adresy URL. Można ustawiać lub edytować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="code"></a>Kod

Wklej kod do **dla ruchu przychodzącego** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat usługi APIM zasad:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)
