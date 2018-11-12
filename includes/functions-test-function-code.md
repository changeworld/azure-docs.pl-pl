---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262611"
---
## <a name="test"></a>Testowanie funkcji

Przetestuj wdrożoną funkcję za pomocą programu cURL na komputerze Mac lub komputerze z systemem Linux bądź za pomocą powłoki Bash na komputerze z systemem Windows. Uruchom następujące polecenie programu cURL, wstawiając w miejsce symbolu zastępczego `<app_name>` nazwę aplikacji funkcji. Dołącz ciąg zapytania `&name=<yourname>` do adresu URL.

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Jeśli w wierszu polecenia nie jest dostępny program cURL, wprowadź ten sam adres URL w pasku adresu przeglądarki. W miejsce symbolu zastępczego `<app_name>` wstaw nazwę aplikacji funkcji i dołącz ciąg zapytania `&name=<yourname>` do adresu URL, a następnie wykonaj żądanie.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
