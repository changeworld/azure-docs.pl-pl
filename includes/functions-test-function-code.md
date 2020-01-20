---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279330"
---
## <a name="test"></a>Weryfikowanie funkcji na platformie Azure

Możesz użyć przeglądarki sieci Web do zweryfikowania wdrożonej funkcji.  Skopiuj adres URL, w tym klucz funkcji, na pasku adresu przeglądarki sieci Web. Dołącz ciąg zapytania `&name=<yourname>` do adresu URL przed wykonaniem żądania.

![Używanie przeglądarki sieci Web do wywoływania funkcji.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

Alternatywnie można użyć Zwinięciea, aby zweryfikować wdrożoną funkcję. Przy użyciu adresu URL, w tym klucza funkcji skopiowanego z poprzedniego kroku, dołącz ciąg zapytania `&name=<yourname>` do adresu URL.

![Używanie zwinięcie do wywołania funkcji na platformie Azure.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

