---
title: Synchronizowanie zawartości z folderu w chmurze
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service za pomocą synchronizacji zawartości z folderu w chmurze, w tym usługi OneDrive lub Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482976"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizowanie zawartości z folderu w chmurze z usługą Azure App Service
W tym artykule pokazano, jak synchronizować zawartość z [usługą Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) z Dropbox i OneDrive. 

Wdrożenie synchronizacji zawartości na żądanie jest obsługiwane przez aparat wdrażania usługi App Service [Kudu](https://github.com/projectkudu/kudu/wiki). Możesz pracować z kodem aplikacji i zawartością w wyznaczonym folderze w chmurze, a następnie synchronizować z usługą App Service za pomocą kliknięcia przycisku. Synchronizacja zawartości używa serwera kompilacji Kudu. 

## <a name="enable-content-sync-deployment"></a>Włączanie wdrażania synchronizacji zawartości

Aby włączyć synchronizację zawartości, przejdź do strony aplikacji usługi App Service w [witrynie Azure Portal](https://portal.azure.com).

W menu po lewej stronie kliknij pozycję **Centrum** > wdrażania**OneDrive** lub **Dropbox** > **Authorize**. Postępuj zgodnie z instrukcjami autoryzacji. 

![](media/app-service-deploy-content-sync/choose-source.png)

Wystarczy autoryzować je za pomocą usługi OneDrive lub Dropbox tylko raz. Jeśli masz już autoryzację, po prostu kliknij przycisk **Kontynuuj**. Możesz zmienić autoryzowane konto Usługi OneDrive lub Dropbox, klikając **pozycję Zmień konto**.

![](media/app-service-deploy-content-sync/continue.png)

Na stronie **Konfigurowanie** wybierz folder, który chcesz zsynchronizować. Ten folder jest tworzony w poniższej wyznaczonej ścieżce zawartości w usłudze OneDrive lub Dropbox. 
   
* **Usługa OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Po zakończeniu kliknij przycisk **Kontynuuj**.

Na stronie **Podsumowanie** sprawdź swoje opcje i kliknij przycisk **Zakończ**.

## <a name="synchronize-content"></a>Synchronizowanie zawartości

Aby zsynchronizować zawartość w folderze w chmurze z usługą App Service, wróć do strony **Centrum wdrażania** i kliknij pozycję **Synchronizuj**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Ze względu na podstawowe różnice w interfejsach API **usługa OneDrive dla Firm** nie jest obecnie obsługiwana. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Wyłączanie wdrażania synchronizacji zawartości

Aby wyłączyć synchronizację zawartości, przejdź do strony aplikacji usługi App Service w [witrynie Azure Portal](https://portal.azure.com).

W menu po lewej stronie kliknij pozycję **Centrum** > wdrażania**Rozłącz**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie z lokalnego repozytorium Git](deploy-local-git.md)
