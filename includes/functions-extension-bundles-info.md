---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201941"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Podczas uruchamiania host pobiera i instaluje [rozszerzenie powiązania magazynu](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) i inne rozszerzenia powiązania firmy Microsoft. Ta instalacja odbywa się, ponieważ rozszerzenia powiązania są domyślnie włączone w pliku *host.json* z następującymi właściwościami:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Jeśli wystąpią jakiekolwiek błędy związane z rozszerzeniami wiązania, sprawdź, czy powyższe właściwości są obecne w *pliku host.json*.
::: zone-end  