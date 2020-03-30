---
title: Zestaw SDK usługi Azure Event Grid
description: W tym artykule opisano zestawów SDK dla usługi Azure Event Grid. Te pakiety SDK zapewniają zarządzanie, publikowanie i konsumpcję.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60822845"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK siatki zdarzeń do zarządzania i publikowania

Usługa Event Grid udostępnia skomponowanie, które umożliwiają programowe zarządzanie zasobami i publikowanie zdarzeń.

## <a name="management-sdks"></a>Pakiety SDK zarządzania

Pakiety SDK zarządzania umożliwiają tworzenie, aktualizowanie i usuwanie tematów i subskrypcji siatki zdarzeń. Obecnie dostępne są następujące skuszki SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Przejdź](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Zestawy SDK płaszczyzny danych

Zestawy SDK płaszczyzny danych umożliwiają księgowanie zdarzeń w tematach, dbając o uwierzytelnianie, tworzenie zdarzenia i asynchronicznie księgowanie w określonym punkcie końcowym. Umożliwiają one również korzystanie z zdarzeń pierwszej strony. Obecnie dostępne są następujące skuszki SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Przejdź](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Następne kroki

* Na przykład aplikacje można znaleźć w [przykładach kodu siatki zdarzeń](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Aby uzyskać wprowadzenie do siatki zdarzeń, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby zapoznać się z poleceniami siatki zdarzeń w usłudze Azure CLI, zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure/eventgrid).
* Aby zapoznać się z poleceniami siatki zdarzeń w programie PowerShell, zobacz [Program PowerShell](/powershell/module/az.eventgrid).
