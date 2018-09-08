---
title: Koncentratory zadań w funkcje trwałe - Azure
description: Dowiedz się, jakie Centrum zadanie trwa rozszerzenia funkcji trwałych dla usługi Azure Functions. Dowiedz się, jak skonfigurować konfigurowanie koncentratory zadań.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 53c70d4407222a80a9bc948db51294cd3e4e1bb4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092839"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Koncentratory zadań w funkcje trwałe (usługi Azure Functions)

A *Centrum zadań* w [funkcje trwałe](durable-functions-overview.md) to kontener logiczny dla zasobów usługi Azure Storage, które są używane do aranżacji. Funkcje programu orchestrator i działanie tylko można ze sobą współdziałać, jeśli należą do tego samego Centrum zadania.

Każda aplikacja funkcji ma Centrum osobne zadanie. Jeśli wiele aplikacji funkcyjnych udostępniać konta magazynu, konto magazynu zawiera wiele centrów zadania. Na poniższym diagramie przedstawiono koncentratorze zadania na aplikację funkcji w ramach kont magazynu udostępnione i dedykowane.

![Diagram przedstawiający udostępnione i dedykowane konta magazynu.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Zasoby usługi Azure Storage

Koncentrator zadanie składa się z następującymi zasobami magazynu: 

* Jedną lub więcej kolejek kontroli.
* Jedna kolejka elementu roboczego.
* Historia jednej tabeli.
* Jedna tabela wystąpień.
* Jeden kontener magazynu zawierające co najmniej jednego obiektu blob dzierżawy.

Wszystkie te zasoby są tworzone automatycznie w domyślne konto usługi Azure Storage uruchamiania lub zaplanowane do uruchomienia programu orchestrator lub działanie funkcji. [Wydajności i skali](durable-functions-perf-and-scale.md) artykule wyjaśniono, jak te zasoby są używane.

## <a name="task-hub-names"></a>Nazwy Centrum zadań

Koncentratory zadań są identyfikowane przez nazwę, która jest zadeklarowana w *host.json* pliku, jak pokazano w poniższym przykładzie:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Nazwy Centrum zadań musi rozpoczynać się literą i składać się wyłącznie z liter i cyfr. Jeśli nie zostanie określony, domyślną nazwą jest **DurableFunctionsHub**.

> [!NOTE]
> Nazwa to, co odróżnia koncentratorze zadania z innego gdy wiele centrów zadań znajduje się na koncie magazynu udostępnionego. Jeśli masz wiele aplikacji funkcji udostępniania na koncie magazynu udostępnionego, należy skonfigurować różne nazwy dla każdego zadania Centrum *host.json* plików.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie obsługi przechowywania wersji](durable-functions-versioning.md)
