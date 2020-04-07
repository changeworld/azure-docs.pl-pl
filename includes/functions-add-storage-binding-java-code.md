---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: f4f109a6da0cb75555f9aadfa2fff31b8aa3107d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673459"
---
Teraz można użyć nowego `msg` parametru do zapisu do powiązania danych wyjściowych z kodu funkcji. Dodaj następujący wiersz kodu przed odpowiedzią na `name` powodzenie, aby dodać wartość do powiązania `msg` danych wyjściowych.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33":::

Korzystając z powiązania danych wyjściowych, nie trzeba używać kodu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Powiązania środowiska wykonawczego i danych wyjściowych kolejki funkcji wykonaj te zadania za Ciebie.

Metoda `run` powinna teraz wyglądać następująco:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-37":::