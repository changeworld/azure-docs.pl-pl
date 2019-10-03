---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839128"
---
Aplikacja funkcji została już utworzona na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do pliku Local. Settings. JSON. Uruchom następujące polecenie Azure Functions Core Tools, aby pobrać ustawienia do pliku Local. Settings. JSON, zastępując `<APP_NAME>` nazwą aplikacji funkcji z poprzedniego artykułu:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Może być konieczne zalogowanie się do konta platformy Azure.

> [!IMPORTANT]  
> Ponieważ zawiera wpisy tajne, plik Local. Settings. JSON nigdy nie jest publikowany i powinien być wykluczony z kontroli źródła.

Wymagana jest wartość `AzureWebJobsStorage`, czyli parametry połączenia konta magazynu. To połączenie służy do sprawdzania, czy powiązanie danych wyjściowych działa zgodnie z oczekiwaniami.
