---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f7e023bcfeaa07a4ee9a80ccf4ec17120605c1ba
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133908"
---
## <a name="test"></a>Testowanie funkcji

Przetestuj wdrożoną funkcję za pomocą programu cURL na komputerze Mac lub komputerze z systemem Linux bądź za pomocą powłoki Bash na komputerze z systemem Windows. Uruchom następujące polecenie programu cURL, wstawiając w miejsce symbolu zastępczego `<app_name>` nazwę aplikacji funkcji. Dołącz ciąg zapytania `&name=<yourname>` do adresu URL.

```bash
curl https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
```  

![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Jeśli w wierszu polecenia nie jest dostępny program cURL, wprowadź ten sam adres URL w pasku adresu przeglądarki. W miejsce symbolu zastępczego `<app_name>` wstaw nazwę aplikacji funkcji i dołącz ciąg zapytania `&name=<yourname>` do adresu URL, a następnie wykonaj żądanie.

    https://<app_name>.azurewebsites.net/api/HttpTrigger?name=<yourname>
   
![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
