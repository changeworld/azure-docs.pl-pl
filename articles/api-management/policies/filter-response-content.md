---
title: Azure próbce interfejsu API zarządzania zasad - zawartości odpowiedzi | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak filtrować elementy danych z ładunku odpowiedzi oparte na produktu skojarzonego z żądaniem.
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
ms.openlocfilehash: f6475b272239e9352211a80985e1b46da9c6f8e0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="filter-response-content"></a>Filtrowanie zawartości odpowiedzi

W tym artykule przedstawiono przykład zasad interfejsu API usługi Azure zarządzania, który demonstruje sposób filtrowania elementów danych z ładunku odpowiedzi oparte na produktu skojarzonego z żądaniem. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="policy"></a>Zasady

Wklej kod do **wychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)

