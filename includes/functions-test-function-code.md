---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183013"
---
## <a name="test"></a>Testowanie funkcji na platformie Azure

Korzystanie z programu cURL, przetestuj wdrożoną funkcję. Przy użyciu adresu URL, który został skopiowany w poprzednim kroku, Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Używając programu cURL, aby wywołać funkcję platformy Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Można także Wklej skopiowany adres URL w adresu przeglądarki sieci web. Ponownie Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, przed wykonaniem żądania.

![W przeglądarce sieci web w wywołaniu funkcji.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
