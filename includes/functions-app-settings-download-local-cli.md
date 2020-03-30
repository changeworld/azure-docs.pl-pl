---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329599"
---
Utworzono już aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule piszesz wiadomości do kolejki magazynu na tym samym koncie. Aby połączyć się z kontem magazynu podczas lokalnego uruchamiania funkcji, należy pobrać ustawienia aplikacji do pliku local.settings.json. 

W katalogu głównym projektu uruchom następujące polecenie Narzędzia podstawowe usług Azure Functions Core, `<APP_NAME>` aby pobrać ustawienia do local.settings.json, zastępując nazwą aplikacji funkcyjnej z poprzedniego artykułu:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Może być konieczne zalogowanie się do konta platformy Azure.

> [!IMPORTANT]  
> To polecenie zastępuje wszystkie istniejące ustawienia wartościami z aplikacji funkcji na platformie Azure.  
>
> Ponieważ zawiera wpisy tajne, local.settings.json plik nigdy nie zostanie opublikowany i powinny być wykluczone z kontroli źródła.  
> 

Potrzebna jest `AzureWebJobsStorage`wartość , która jest ciągiem połączenia konta magazynu. To połączenie służy do sprawdzenia, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.
