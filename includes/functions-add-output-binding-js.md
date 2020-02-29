---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 098aaba0a357c13a059d3c4042a19715c6b5ad42
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191069"
---
Dodaj kod, który używa `msg` obiektu powiązania danych wyjściowych na `context.bindings`, aby utworzyć komunikat w kolejce. Dodaj ten kod przed instrukcją `context.res`.

:::code language="javascript" range="7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

W tym momencie funkcja powinna wyglądać w następujący sposób:

:::code language="javascript" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::