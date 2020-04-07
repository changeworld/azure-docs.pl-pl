---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673362"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Pobieranie ciągu połączenia usługi Azure Storage

Wcześniej utworzono konto usługi Azure Storage do użycia przez aplikację funkcji. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. Pobierając to ustawienie do pliku *local.settings.json,* można użyć tego zapisu połączenia do kolejki magazynu na tym samym koncie podczas lokalnego uruchamiania funkcji. 

1. W katalogu głównym projektu uruchom następujące polecenie, zastępując `<app_name>` nazwą aplikacji funkcji z poprzedniego przewodnika Szybki start. To polecenie zastąpi wszystkie istniejące wartości w pliku.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otwórz *local.settings.json* i znajdź `AzureWebJobsStorage`wartość o nazwie , która jest ciągiem połączenia konta magazynu. Nazwy `AzureWebJobsStorage` i ciągu połączenia należy użyć w innych sekcjach tego artykułu.

> [!IMPORTANT]
> Ponieważ *local.settings.json* zawiera wpisy tajne pobrane z platformy Azure, zawsze wyklucz ten plik z kontroli źródła. Plik *.gitignore* utworzony za pomocą projektu funkcji lokalnych domyślnie wyklucza plik.