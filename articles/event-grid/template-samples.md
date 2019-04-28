---
title: Przykłady szablonów usługi Azure Resource Manager — usługa Event Grid | Microsoft Docs
description: Przykłady szablonów usługi Azure Resource Manager dla usługi Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822705"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Szablony usługi Azure Resource Manager dla usługi Event Grid

Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonie, zobacz [Typy zasobów Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Event Grid.

| | |
|-|-|
|**Subskrypcje usługi Event Grid**||
| [Niestandardowe tematy i subskrypcje z punktem końcowym elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Wdraża niestandardowy temat usługi Event Grid. Tworzy subskrypcję tego niestandardowego tematu, który używa punktu końcowego elementu WebHook. |
| [Subskrypcja niestandardowego tematu z punktem końcowym EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Tworzy subskrypcję niestandardowego tematu dla usługi Event Grid. Subskrypcja używa Centrum zdarzeń dla punktu końcowego. |
| [Subskrypcja platformy Azure lub subskrypcja grupy zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Subskrybuje zdarzenia dla grupy zasobów lub subskrypcji platformy Azure. Grupa zasobów, która został określona jako element docelowy podczas wdrażania, jest źródłem zdarzeń. Subskrypcja używa elementu WebHook dla punktu końcowego. |
| [Konto magazynu obiektów blob i subskrypcja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. |
| | |
