---
title: Zestawy SDK siatki zdarzeń platformy Azure
description: W tym artykule opisano zestawy SDK dla usługi Azure Event siatki. Te zestawy SDK zapewniają zarządzania, publikowanie i zużycia.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: ebba28e72b3b27729318956bad07b6b6df3f14ac
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823550"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Zestawy SDK siatki zdarzeń zarządzania i publikowanie

Zdarzenie siatki udostępnia zestawów SDK, które umożliwiają programowego zarządzania zasobami i publikowania zdarzeń.

## <a name="management-sdks"></a>Zestawy SDK zarządzania

Zarządzanie zestawów SDK Włącz tworzenie, aktualizowanie i usuwanie zdarzeń siatki tematów i subskrypcji. Obecnie dostępne są następujące zestawy SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
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
