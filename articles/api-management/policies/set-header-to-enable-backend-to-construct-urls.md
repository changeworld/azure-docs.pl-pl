---
title: Przykładowe zasady zarządzania interfejsu API platformy Azure — Dodawanie nagłówka przekazane | Dokumentacja firmy Microsoft
description: Przykładowe zasady zarządzania Azure API - pokazano, jak dodać nagłówek przekazane w żądania przychodzącego, aby umożliwić zaplecza interfejsu API, aby utworzyć odpowiednie adresy URL.
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
ms.openlocfilehash: a9dfcbf4be4b659d761d66d67d2ae4c7b70a245e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284168"
---
# <a name="add-a-forwarded-header"></a>Dodaj nagłówek przekazane

W tym artykule przedstawiono przykładowe zasad zarządzania interfejsu API Azure, który demonstruje sposób dodawania nagłówka przekazane w żądania przychodzącego, aby umożliwić zaplecza interfejsu API, aby utworzyć odpowiednie adresy URL. Aby ustawić lub zmodyfikować kod zasad, wykonaj czynności opisane w [zestawu lub Edytuj zasady](../set-edit-policies.md). Aby wyświetlić inne przykłady, zobacz [Przykłady zasad](../policy-samples.md).

## <a name="code"></a>Kod

Wklej kod do **przychodzących** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o zasadach APIM:

+ [Zasady transformacji](../api-management-transformation-policies.md)
+ [Przykłady zasad](../policy-samples.md)
