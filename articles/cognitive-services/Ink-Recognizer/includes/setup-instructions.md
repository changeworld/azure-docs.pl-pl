---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996863"
---
>[!NOTE]
> Punkty końcowe dla zasobów utworzonych po 1 lipca 2019 r. używają niestandardowego formatu poddomeny przedstawionego poniżej. Aby uzyskać więcej informacji i pełną listę regionalnych punktów końcowych, zobacz [Niestandardowe nazwy poddomen dla usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usługi Azure Cognitive Services są reprezentowane przez zasoby platformy Azure, które subskrybujesz. Utwórz zasób dla aparatu rozpoznawania pisma odurzacza przy użyciu [portalu Azure](../../cognitive-services-apis-create-account.md). 

* Możesz również otrzymać [klucz testowy](https://azure.microsoft.com/try/cognitive-services/#decision) ważny przez siedem dni za darmo. Po zarejestrowaniu się, to i punkt końcowy będą dostępne w [witrynie sieci Web platformy Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).

Po utworzeniu zasobu pobierz punkt końcowy i klucz, otwierając zasób w [witrynie Azure portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)i klikając **pozycję Szybki start**.

Utwórz dwie [zmienne środowiskowe:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- Punkt końcowy zasobu. Będzie to wyglądać tak: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- Klucz subskrypcji do uwierzytelniania żądań.   
