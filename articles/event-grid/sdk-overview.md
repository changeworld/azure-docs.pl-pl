---
title: Zestawy SDK siatki zdarzeń platformy Azure
description: W tym artykule opisano zestawy SDK dla usługi Azure Event siatki. Te zestawy SDK zapewniają zarządzania, publikowanie i zużycia.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: d9bb4b3b161060f20fca34760872a24cbfcabf30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Zestawy SDK siatki zdarzeń zarządzania i publikowanie

Zdarzenie siatki udostępnia zestawów SDK, które umożliwiają programowego zarządzania zasobami i publikowania zdarzeń.

## <a name="management-sdks"></a>Zestawy SDK zarządzania

Zarządzanie zestawów SDK Włącz tworzenie, aktualizowanie i usuwanie zdarzeń siatki tematów i subskrypcji. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Zestawy SDK płaszczyzna danych

Płaszczyzna danych zestawów SDK umożliwiają post zdarzenia do tematów przez obsługę uwierzytelniania, tworzące zdarzenia i asynchronicznego ogłaszania określony punkt końcowy. Umożliwiają one również korzystać pierwsze zdarzenia strony. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [co to jest zdarzenie siatki?](overview.md)
* Zdarzenie siatki poleceń w wiersza polecenia platformy Azure, zobacz [interfejsu wiersza polecenia Azure](/cli/azure/eventgrid).
* Dla polecenia siatki zdarzeń w programie PowerShell, zobacz [PowerShell](/powershell/module/azurerm.eventgrid).