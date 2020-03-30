---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190923"
---
W projekcie biblioteki klas języka C# powiązania są definiowane jako atrybuty powiązania w metodzie funkcji. Plik *function.json* wymagany przez funkcje jest następnie generowany automatycznie na podstawie tych atrybutów.

Otwórz plik *projektu HttpExample.cs* i dodaj następujący parametr `Run` do definicji metody:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Parametr `msg` jest `ICollector<T>` typem, który reprezentuje zbiór komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu funkcji. W takim przypadku dane wyjściowe `outqueue`to kolejka magazynu o nazwie . Parametry połączenia dla konta Magazyn jest `StorageAccountAttribute`ustawiany przez plik . Ten atrybut wskazuje ustawienie, które zawiera parametry połączenia konta magazynu i może być stosowane na poziomie klasy, metody lub parametru. W takim przypadku można `StorageAccountAttribute` pominąć, ponieważ używasz już domyślnego konta magazynu.

Definicja metody Uruchom powinna teraz wyglądać następująco:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
