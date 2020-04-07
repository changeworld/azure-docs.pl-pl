---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673491"
---
W projekcie Java powiązania są definiowane jako adnotacje wiązania w metodzie funkcji. Plik *function.json* jest następnie automatyczniegenerowany na podstawie tych adnotacji.

Przejdź do lokalizacji kodu funkcji pod _src/main/java_, otwórz plik projektu *Function.java* i `run` dodaj następujący parametr do definicji metody:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

Parametr `msg` jest [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typem, który reprezentuje kolekcję ciągów, które są zapisywane jako komunikaty do powiązania danych wyjściowych po zakończeniu funkcji. W takim przypadku dane wyjściowe `outqueue`to kolejka magazynu o nazwie . Parametry połączenia dla konta magazynu jest `connection` ustawiana przez metodę. Zamiast samego ciągu połączenia należy przekazać ustawienie aplikacji zawierające parametry połączenia konta magazynu.

Definicja `run` metody powinna teraz wyglądać następująco:  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::