---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132104"
---
## <a name="test"></a>Testowanie funkcji na platformie Azure

Korzystanie z programu cURL, przetestuj wdrożoną funkcję. Przy użyciu adresu URL, który został skopiowany w poprzednim kroku, Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, jak w poniższym przykładzie:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![Używając programu cURL, aby wywołać funkcję platformy Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

Można także Wklej skopiowany adres URL w adresu przeglądarki sieci web. Ponownie Dołącz ciąg zapytania `&name=<yourname>` do adresu URL, przed wykonaniem żądania.

![W przeglądarce sieci web w wywołaniu funkcji.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
