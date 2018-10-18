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
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: c462334597b41b914b6a0a0e3c8a67ad97e2bd7a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295584"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Szablony usługi Azure Resource Manager dla usługi Event Grid

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Event Grid.

| | |
|-|-|
|**Subskrypcje usługi Event Grid**||
| [Niestandardowe tematy i subskrypcje z punktem końcowym elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Wdraża niestandardowy temat usługi Event Grid. Tworzy subskrypcję tego niestandardowego tematu, który używa punktu końcowego elementu WebHook. |
| [Subskrypcja niestandardowego tematu z punktem końcowym EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Tworzy subskrypcję niestandardowego tematu dla usługi Event Grid. Subskrypcja używa Centrum zdarzeń dla punktu końcowego. |
| [Subskrypcja platformy Azure lub subskrypcja grupy zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Subskrybuje zdarzenia dla grupy zasobów lub subskrypcji platformy Azure. Grupa zasobów, która został określona jako element docelowy podczas wdrażania, jest źródłem zdarzeń. Subskrypcja używa elementu WebHook dla punktu końcowego. |
| [Konto magazynu obiektów blob i subskrypcja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. |
| | |
