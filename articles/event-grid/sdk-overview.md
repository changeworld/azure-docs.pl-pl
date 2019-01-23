---
title: Usługi Azure Event Grid w zestawy SDK
description: W tym artykule opisano te zestawy SDK dla usługi Azure Event Grid. Te zestawy SDK zapewniają zarządzania, publikowanie i zużycia.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 53a1f50647e05b71afe6accb85f8a90cd8b7e253
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470409"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Do zarządzania i publikowania, zestawy SDK siatki zdarzeń

Usługi Event Grid udostępnia zestawy SDK, które umożliwiają programowe zarządzanie zasobami i Publikuj zdarzenia.

## <a name="management-sdks"></a>Zarządzanie zestawami SDK

Zarządzanie zestawami SDK umożliwia tworzenie, aktualizowanie i usuwanie subskrypcji i tematy usługi event grid. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Zestawy SDK płaszczyzny danych

Płaszczyzna danych SDK umożliwia wpis zdarzenia do tematów, zwracając szczególną uwagę uwierzytelniania, stanowiące zdarzenia i asynchronicznie wysłanie postu do określonego punktu końcowego. Umożliwiają one również korzystanie ze zdarzeń usługi pierwszej strony. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Kolejne kroki

* Na przykład, zobacz [przykładów kodu usługi Event Grid](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Usługa Event Grid poleceń w interfejsie wiersza polecenia platformy Azure można znaleźć [wiersza polecenia platformy Azure](/cli/azure/eventgrid).
* Usługa Event Grid poleceń w programie PowerShell można znaleźć [PowerShell](/powershell/module/azurerm.eventgrid).
