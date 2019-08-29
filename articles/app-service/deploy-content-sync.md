---
title: Synchronizuj zawartość z folderu w chmurze — Azure App Service
description: Dowiedz się, jak wdrożyć aplikację w celu Azure App Service za pośrednictwem synchronizacji zawartości z folderu w chmurze.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 033a9adfd81dc52858ab58e3170e69af6f56c323
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070627"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizuj zawartość z folderu w chmurze w celu Azure App Service
W tym artykule pokazano, jak zsynchronizować zawartość do [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z usługi Dropbox i OneDrive. 

Wdrożenie synchronizacji zawartości na żądanie jest obsługiwane przez [Aparat wdrażania App Service kudu](https://github.com/projectkudu/kudu/wiki). Możesz korzystać z kodu aplikacji i zawartości w wydzielonym folderze w chmurze, a następnie synchronizować się z App Service przy użyciu przycisku. Synchronizacja zawartości używa serwera kompilacji kudu. 

## <a name="enable-content-sync-deployment"></a>Włącz wdrożenie synchronizacji zawartości

Aby włączyć synchronizację zawartości, przejdź do strony aplikacji App Service w [Azure Portal](https://portal.azure.com).

W menu po lewej stronie kliknij pozycję **Deployment Center** > **OneDrive** lub **Dropbox** > **Autoryzuj**. Postępuj zgodnie z monitami o autoryzację. 

![](media/app-service-deploy-content-sync/choose-source.png)

Wystarczy tylko autoryzować usługę OneDrive lub Dropbox. Jeśli masz już autoryzację, po prostu kliknij pozycję **Kontynuuj**. Możesz zmienić autoryzowane konto usługi OneDrive lub Dropbox, klikając pozycję **Zmień konto**.

![](media/app-service-deploy-content-sync/continue.png)

Na stronie **Konfigurowanie** wybierz folder, który chcesz synchronizować. Ten folder zostanie utworzony w usłudze OneDrive lub Dropbox za pomocą następującej wskazanej ścieżki zawartości. 
   
* **Usługa OneDrive**:`Apps\Azure Web Apps`
* Usługa **Dropbox**:`Apps\Azure`

Po zakończeniu kliknij przycisk **Kontynuuj**.

Na stronie **Podsumowanie** Sprawdź opcje i kliknij przycisk **Zakończ**.

## <a name="synchronize-content"></a>Synchronizuj zawartość

Jeśli chcesz synchronizować zawartość w folderze w chmurze za pomocą App Service, Wróć do strony **centrum wdrażania** i kliknij pozycję **Synchronizuj**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Ze względu na różnice podstawowe w interfejsach API **Usługa OneDrive dla firm** nie jest w tej chwili obsługiwana. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Wyłącz wdrożenie synchronizacji zawartości

Aby wyłączyć synchronizację zawartości, przejdź do strony aplikacji App Service w [Azure Portal](https://portal.azure.com).

W menu po lewej stronie kliknij pozycję **centrum** > wdrażania**Odłącz**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdróż z lokalnego repozytorium git](deploy-local-git.md)
