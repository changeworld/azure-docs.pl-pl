---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148015"
---
## <a name="test"></a>Testowanie funkcji

Przetestuj wdrożoną funkcję za pomocą programu cURL na komputerze Mac lub komputerze z systemem Linux bądź za pomocą programu Powershell w systemie Windows. Uruchom następujące polecenie programu cURL, wstawiając w miejsce symbolu zastępczego `<app_name>` nazwę aplikacji funkcji. Dołącz ciąg zapytania `&name=<yourname>` do adresu URL.

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Jeśli w wierszu polecenia nie jest dostępny program `cURL` ani `Invoke-WebRequest`, wprowadź ten sam adres URL w pasku adresu przeglądarki. W miejsce symbolu zastępczego `<app_name>` wstaw nazwę aplikacji funkcji i dołącz ciąg zapytania `&name=<yourname>` do adresu URL, a następnie wykonaj żądanie.

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
