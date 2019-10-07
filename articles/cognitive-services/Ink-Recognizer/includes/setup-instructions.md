---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996863"
---
>[!NOTE]
> Punkty końcowe dla zasobów utworzonych po 1 lipca 2019 używają niestandardowego formatu poddomen pokazanego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [niestandardowe nazwy domen poddomen dla Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usługa Azure Cognitive Services jest reprezentowana przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla aparatu rozpoznawania pisma odręcznego przy użyciu [Azure Portal](../../cognitive-services-apis-create-account.md). 

* [Klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services/#decision) można także uzyskać bezpłatnie przez siedem dni. Po zarejestrowaniu się w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/)będzie dostępny punkt końcowy.

Po utworzeniu zasobu Pobierz punkt końcowy i klucz, otwierając zasób na [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), a następnie klikając pozycję **Szybki Start**.

Utwórz dwie [zmienne środowiskowe](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` — punkt końcowy zasobu. Będzie wyglądać następująco: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` — klucz subskrypcji służący do uwierzytelniania żądań.   
