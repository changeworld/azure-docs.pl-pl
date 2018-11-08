---
title: Synchronizuj zawartość z folderu w chmurze w usłudze Azure App Service
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service za pośrednictwem synchronizacja zawartości z folderu w chmurze.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: 3796f5c8956b633a4789baaf31a439746dc96b96
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233766"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizuj zawartość z folderu w chmurze w usłudze Azure App Service
W tym artykule dowiesz się, jak zsynchronizować zawartość do [usługi Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z usługą Dropbox i OneDrive. 

Wdrażanie synchronizacji zawartości na żądanie jest obsługiwane przez usługę App [aparat wdrażania Kudu](https://github.com/projectkudu/kudu/wiki). Można pracować z kodu aplikacji oraz zawartość folderu wyznaczonym chmury, a są synchronizowane z usługi App Service za pomocą kliknięcia przycisku. Synchronizacja zawartości używa serwer kompilacji Kudu. 

## <a name="enable-content-sync-deployment"></a>Włącz wdrożenie synchronizacja zawartości

Aby włączyć synchronizację zawartości, przejdź do strony aplikacji usługi App Service w [witryny Azure portal](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **OneDrive** lub **Dropbox** > **Autoryzuj**. Postępuj zgodnie z monitami autoryzacji. 

![](media/app-service-deploy-content-sync/choose-source.png)

Należy przeprowadzić autoryzację w usłudze OneDrive lub Dropbox, jeden raz. Jeśli masz już uprawnienia, po prostu kliknij **Kontynuuj**. Autoryzowanych konta w usłudze OneDrive lub Dropbox można zmienić, klikając **zmienić konto**.

![](media/app-service-deploy-content-sync/continue.png)

W **Konfiguruj** wybierz folder, którego chcesz synchronizować. Ten folder jest tworzony w następującej ścieżce zawartości wyznaczonym w usłudze OneDrive lub Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Po zakończeniu kliknij przycisk **Kontynuuj**.

W **Podsumowanie** strony, sprawdź opcje i kliknij przycisk **Zakończ**.

## <a name="synchronize-content"></a>Synchronizuj zawartość

Gdy chcesz zsynchronizować zawartość folderu w chmurze z usługą App Service, wróć do **Centrum wdrażania** strony, a następnie kliknij przycisk **synchronizacji**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Z powodu podstawowych różnic w interfejsach API **usługi OneDrive dla firm** nie jest obsługiwana w tej chwili. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Wyłącz Wdrażanie synchronizacji zawartości

Aby wyłączyć funkcję synchronizacji zawartości, przejdź do strony aplikacji usługi App Service w [witryny Azure portal](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **OneDrive** lub **Dropbox** > **rozłączenia**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie z lokalnego repozytorium Git](app-service-deploy-local-git.md)
