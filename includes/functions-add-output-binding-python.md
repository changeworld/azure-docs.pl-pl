---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191065"
---
*Zaktualizuj\\\_\_httpexample\_\_init .py,* aby dopasować następujący `if name:` kod, dodając `msg` parametr do definicji funkcji i `msg.set(name)` pod instrukcją.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Parametr `msg` jest wystąpieniem [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)pliku . Jego `set` metoda zapisuje komunikat ciągu do kolejki, w tym przypadku nazwa przekazana do funkcji w ciągu zapytania adresu URL.
