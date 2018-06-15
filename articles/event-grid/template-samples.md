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
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246274"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Szablony usługi Azure Resource Manager dla usługi Event Grid

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Event Grid.

| | |
|-|-|
|**Subskrypcje usługi Event Grid**||
| [Niestandardowe tematy i subskrypcje z punktem końcowym elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Wdraża niestandardowy temat usługi Event Grid. Tworzy subskrypcję tego niestandardowego tematu, który używa punktu końcowego elementu WebHook. |
| [Subskrypcja niestandardowego tematu z punktem końcowym EventHub](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Tworzy dla usługi Event Grid subskrypcję niestandardowego tematu, który już istnieje. Subskrypcja używa Centrum zdarzeń dla punktu końcowego. |
| [Subskrypcja grupy zasobów](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Subskrybuje zdarzenia dla grupy zasobów. Grupa zasobów, która został określona jako element docelowy podczas wdrażania, jest źródłem zdarzeń. Subskrypcja używa Centrum zdarzeń dla punktu końcowego. |
| [Konto magazynu obiektów blob i subskrypcja](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. |
| | |
