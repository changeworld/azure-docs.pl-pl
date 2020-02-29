---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: be3c746146012195757ab06de0c424dbc8297e9a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190924"
---
Dodaj kod, który używa obiektu powiązania danych wyjściowych `msg` do utworzenia komunikatu w kolejce. Dodaj ten kod przed zwróceniem metody.

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

W tym momencie funkcja powinna wyglądać w następujący sposób:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::
