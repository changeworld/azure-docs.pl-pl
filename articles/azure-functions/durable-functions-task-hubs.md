---
title: Koncentratory zadania w funkcje trwałe - Azure
description: Dowiedz się z Centrum zadań znajduje się w funkcji trwałe rozszerzenie dla usługi Azure Functions. Dowiedz się, jak skonfigurować skonfigurować zadanie koncentratorów.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Koncentratory zadań w funkcji trwałe (funkcje platformy Azure)

A *Centrum zadań* w [trwałe funkcji](durable-functions-overview.md) jest kontenerem logicznym dla zasobów usługi Azure Storage, które są używane dla orchestrations. Funkcje programu orchestrator i działania tylko mogą współdziałać ze sobą, jeśli należą do tego samego Centrum zadań.

Każda aplikacja funkcji ma koncentratora osobnym zadaniem. Wiele aplikacji funkcji udostępniania konta magazynu, konto magazynu zawiera wiele zadań koncentratorów. Na poniższym diagramie przedstawiono zadania koncentratorze każdej funkcji aplikacji na kontach magazynu udostępnionego i dedykowanych.

![Diagram przedstawiający udostępnionego i kont magazynu w wersji dedykowanej.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Zasoby usługi Azure Storage

Koncentrator zadań składa się z następującymi zasobami magazynu: 

* Jeden lub więcej kolejek formantu.
* Jedna kolejka elementu roboczego.
* Historia jednej tabeli.
* Jedna tabela wystąpień.
* Jeden kontener magazynu zawierające jeden lub więcej obiektów blob dzierżawy.

Wszystkie te zasoby są tworzone automatycznie w domyślne konto magazynu Azure Uruchom lub zaplanowane do uruchomienia programu orchestrator lub działania funkcji. [Wydajności i skalowania](durable-functions-perf-and-scale.md) artykule wyjaśniono, jak te zasoby są używane.

## <a name="task-hub-names"></a>Nazwy Centrum dla zadań

Koncentratory zadania są identyfikowane przez nazwę, która jest zadeklarowana w *host.json* plików, jak pokazano w poniższym przykładzie:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Nazwy Centrum dla zadań musi zaczynać się literą i zawierać tylko litery i cyfry. Jeśli nie zostanie określony, nazwa domyślna to **DurableFunctionsHub**.

> [!NOTE]
> Nazwa jest, co odróżnia koncentratorze zadań z innej w przypadku wielu centrów zadań w ramach konta magazynu udostępnionego. Jeśli masz wiele aplikacji funkcji udostępniania konta magazynu udostępnionego, należy skonfigurować różne nazwy dla każdego zadania Centrum w *host.json* plików.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie obsługi przechowywania wersji](durable-functions-versioning.md)
