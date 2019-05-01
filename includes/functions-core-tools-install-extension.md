---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733267"
---
Pakiety rozszerzeń wprowadzić wszystkie powiązania opublikowane przez zespół usługi Azure Functions, dostępne za pośrednictwem ustawienie w *host.json* pliku. Dla wdrożenia lokalnego, upewnij się, masz najnowszą wersję [podstawowych narzędzi usługi Azure Functions](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Aby użyć pakietów rozszerzeń, należy zaktualizować *host.json* pliku, aby uwzględnić następujący wpis dotyczący `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- `id` Właściwości odwołuje się do przestrzeni nazw pakietów rozszerzeń usługi Microsoft Azure Functions.
- `version` Odwołuje się do wersji pakietu.

Inkrementacja wersje pakietu jako pakiety w zmianach pakietu. Wersja główna zmiany się zdarzyć, tylko wtedy, gdy pakiety w pakiecie przenosi wersję główną. `version` Używa właściwości [notacji interwału do określania zakresów wersji](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Środowisko uruchomieniowe usługi Functions zawsze wybiera maksymalna wersja dopuszczalna definiowane przez zakres wersji lub przy określonym interwale.

Gdy odwołujesz się pakiety rozszerzeń w projekcie, wszystkie powiązania domyślne są dostępne dla funkcji. Powiązania, które są dostępne w [pakietu rozszerzenia](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) są:

|Pakiet  |Wersja  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|