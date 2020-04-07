---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3705f58a37c109ebe0b774603c60e246fc174f25
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673317"
---
Teraz można użyć nowego `msg` parametru do zapisu do powiązania danych wyjściowych z kodu funkcji. Dodaj następujący wiersz kodu przed odpowiedzią na `name` powodzenie, aby dodać wartość do powiązania `msg` danych wyjściowych.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Korzystając z powiązania danych wyjściowych, nie trzeba używać kodu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Powiązania środowiska wykonawczego i danych wyjściowych kolejki funkcji wykonaj te zadania za Ciebie.

Metoda `run` powinna teraz wyglądać następująco:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="17-38":::