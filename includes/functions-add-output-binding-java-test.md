---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673495"
---
## <a name="update-the-tests"></a>Aktualizowanie testów

Ponieważ archetyp tworzy również zestaw testów, należy zaktualizować te testy `msg` do `run` obsługi nowego parametru w podpisie metody.  

Przejdź do lokalizacji kodu testowego pod _src/test/java_, otwórz plik projektu *Function.java* `//Invoke` i zastąp wiersz kodu pod następującym kodem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
