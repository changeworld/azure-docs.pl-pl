---
title: Synchronizowanie zawartości z folderu chmury do usługi Azure App Service
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service za pośrednictwem synchronizacji zawartości z folderu chmury.
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
ms.openlocfilehash: d456ae2ffbd3745ef976ad94219a3f998838066b
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850223"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Synchronizowanie zawartości z folderu chmury do usługi Azure App Service
W tym artykule przedstawiono sposób synchronizacji zawartość do [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) z Dropbox i OneDrive. 

Wdrażanie synchronizacji zawartości na żądanie jest obsługiwany przez usługę App Service [aparat wdrażania Kudu](https://github.com/projectkudu/kudu/wiki). Można pracować z kodu aplikacji i zawartości w folderze wyznaczonych chmury, a następnie synchronizacji w usłudze App Service z kliknięcie przycisku. Synchronizacja zawartości używa serwerze Kudu kompilacji. 

## <a name="enable-content-sync-deployment"></a>Włącz wdrożenie zawartości synchronizacji

Aby włączyć zawartości synchronizacji, przejdź do strony aplikacji usługi App Service w [portalu Azure](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **OneDrive** lub **Dropbox** > **autoryzacji**. Postępuj zgodnie z monitami autoryzacji. 

![](media/app-service-deploy-content-sync/choose-source.png)

Wystarczy raz autoryzacji w usłudze OneDrive lub Dropbox. Jeśli masz już uprawnienia, wystarczy kliknąć **Kontynuuj**. Autoryzowanych kont usługi OneDrive lub Dropbox można zmienić, klikając **Zmień konto**.

![](media/app-service-deploy-content-sync/continue.png)

W **Konfiguruj** stronie, wybierz folder, w którym mają być synchronizowane. Ten folder jest tworzony w następującej ścieżce wyznaczonych zawartości w usłudze OneDrive lub Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Gdy skończysz, kliknij przycisk **Kontynuuj**.

W **Podsumowanie** , sprawdź opcje i kliknij przycisk **Zakończ**.

## <a name="synchronize-content"></a>Synchronizowanie zawartości

Jeśli chcesz synchronizować zawartość w folderze chmury z usługi aplikacji, przejdź wstecz do **Centrum wdrażania** i kliknij przycisk **synchronizacji**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Z powodu podstawowych różnic w interfejsach API **OneDrive dla firm** nie jest obsługiwana w tej chwili. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Wyłącz wdrażania zawartości synchronizacji

Aby wyłączyć zawartości synchronizacji, przejdź do strony aplikacji usługi App Service w [portalu Azure](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **OneDrive** lub **Dropbox** > **rozłączenia**.

![](media/app-service-deploy-content-sync/disable.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie z lokalnego repozytorium Git](app-service-deploy-local-git.md)
