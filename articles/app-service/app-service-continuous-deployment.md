---
title: Ciągłe wdrażanie w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć ciągłe wdrażanie w usłudze Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: e587edeef1cfa080a81f523f63678a645b514c57
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849495"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie w usłudze Azure App Service
W tym artykule przedstawiono sposób skonfigurować ciągłego wdrażania [usłudze Azure App Service](app-service-web-overview.md). Usługa aplikacji umożliwia ciągłego wdrażania od BitBucket, GitHub, i [programu Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) przez ściąganie w najnowszych aktualizacji z repozytorium istniejącej w jednej z tych usług.

Aby dowiedzieć się, jak skonfigurować ciągłe wdrażanie ręcznie z repozytorium chmury nie są wyświetlane w portalu Azure (takich jak [GitLab](https://gitlab.com/)), zobacz [Konfigurowanie ciągłego wdrażania przy użyciu ręczne](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Publikuj repozytorium przygotowane do jednego z obsługiwanych usług. Aby uzyskać więcej informacji dotyczących publikowania projektu w tych usługach, zobacz [Tworzenie repozytorium (GitHub)], [Tworzenie repozytorium (BitBucket)], i [Wprowadzenie do usług VSTS].

## <a name="deploy-continuously-from-github"></a>Wdrażanie stale z usługi GitHub

Aby włączyć ciągłego wdrażania z usługi GitHub, przejdź do strony aplikacji usługi App Service w [portalu Azure](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **GitHub** > **autoryzacji**. Postępuj zgodnie z monitami autoryzacji. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Wystarczy raz autoryzacji w usłudze GitHub. Jeśli masz już uprawnienia, wystarczy kliknąć **Kontynuuj**. Autoryzowany konto GitHub można zmienić, klikając **Zmień konto**.

![](media/app-service-continuous-deployment/github-continue.png)

W **dostawcy kompilacji** , wybierz dostawcę kompilacji i kliknij przycisk > **Kontynuuj**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opcja 1: użycie serwera kompilacji Kudu usługi aplikacji

W **Konfiguruj** wybierz organizacji, repozytorium i gałęzi, z których chcesz wdrożyć w sposób ciągły. Gdy skończysz, kliknij przycisk **Kontynuuj**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Opcja 2: użycie programu VSTS ciągłego dostarczania

> [!NOTE]
> Usługi aplikacji Utwórz niezbędne kompilację i wersji definicji w ramach konta usługi VSTS konta platformy Azure musi mieć rolę **właściciela** w Twojej subskrypcji platformy Azure.
>

W **Konfiguruj** strony w **kod** wybierz organizacji, repozytorium i gałęzi, z których chcesz wdrożyć w sposób ciągły. Gdy skończysz, kliknij przycisk **Kontynuuj**.

W **Konfiguruj** strony w **kompilacji** sekcji, konfigurowanie nowego konta usługi VSTS lub określenie istniejącego konta. Gdy skończysz, kliknij przycisk **Kontynuuj**.

> [!NOTE]
> Jeśli chcesz użyć istniejącego konta usługi VSTS nie ma na liście, należy [połączenia konta usługi VSTS do subskrypcji platformy Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

W **testu** wybierz, czy włączyć testów obciążenia, a następnie kliknij pozycję **Kontynuuj**.

W zależności od [warstwy cenowej](/pricing/details/app-service/plans/) planu usługi aplikacji może również zostać wyświetlony **wdrażanie na przemieszczania** strony. Wybierz opcję [włączyć miejsc wdrożenia](web-sites-staged-publishing.md), następnie kliknij przycisk **Kontynuuj**.

### <a name="finish-configuration"></a>Kończenie konfiguracji

W **Podsumowanie** , sprawdź opcje i kliknij przycisk **Zakończ**.

Po zakończeniu konfiguracji nowe zatwierdzenia w repozytorium wybrane są wdrażane stale w aplikację usługi aplikacji.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Wdrażanie stale z BitBucket

Aby włączyć ciągłego wdrażania z BitBucket, przejdź do strony aplikacji usługi App Service w [portalu Azure](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **BitBucket** > **autoryzacji**. Postępuj zgodnie z monitami autoryzacji. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Wystarczy raz autoryzacja BitBucket. Jeśli masz już uprawnienia, wystarczy kliknąć **Kontynuuj**. Autoryzowanych kont BitBucket można zmienić, klikając **Zmień konto**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

W **Konfiguruj** wybierz repozytorium i gałęzi, z których chcesz wdrożyć w sposób ciągły. Gdy skończysz, kliknij przycisk **Kontynuuj**.

W **Podsumowanie** , sprawdź opcje i kliknij przycisk **Zakończ**.

Po zakończeniu konfiguracji nowe zatwierdzenia w repozytorium wybrane są wdrażane stale w aplikację usługi aplikacji.

## <a name="deploy-continuously-from-vsts"></a>Wdrażanie stale z programu VSTS

Aby włączyć ciągłe wdrażanie za pomocą programu VSTS, przejdź do strony aplikacji usługi aplikacji w usłudze [portalu Azure](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **VSTS** > **Kontynuuj**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

W **dostawcy kompilacji** , wybierz dostawcę kompilacji i kliknij przycisk > **Kontynuuj**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opcja 1: użycie serwera kompilacji Kudu usługi aplikacji

W **Konfiguruj** wybierz konta usługi VSTS, projektu, repozytorium i gałęzi, z której chcesz wdrożyć stale. Gdy skończysz, kliknij przycisk **Kontynuuj**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Opcja 2: użycie programu VSTS ciągłego dostarczania

> [!NOTE]
> Usługi aplikacji Utwórz niezbędne kompilację i wersji definicji w ramach konta usługi VSTS konta platformy Azure musi mieć rolę **właściciela** w Twojej subskrypcji platformy Azure.
>

W **Konfiguruj** strony w **kod** wybierz konta usługi VSTS, projektu, repozytorium i gałęzi, z której chcesz wdrożyć stale. Gdy skończysz, kliknij przycisk **Kontynuuj**.

> [!NOTE]
> Jeśli chcesz użyć istniejącego konta usługi VSTS nie ma na liście, należy [połączenia konta usługi VSTS do subskrypcji platformy Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

W **Konfiguruj** strony w **kompilacji** Określ struktury języka, które VSTS powinny być używane do uruchamiania zadań kompilacji dla wybranej repozytorium. Gdy skończysz, kliknij przycisk **Kontynuuj**.

W **testu** wybierz, czy włączyć testów obciążenia, a następnie kliknij pozycję **Kontynuuj**.

W zależności od [warstwy cenowej](/pricing/details/app-service/plans/) planu usługi aplikacji może również zostać wyświetlony **wdrażanie na przemieszczania** strony. Wybierz opcję [włączyć miejsc wdrożenia](web-sites-staged-publishing.md), następnie kliknij przycisk **Kontynuuj**. 

### <a name="finish-configuration"></a>Kończenie konfiguracji

W **Podsumowanie** , sprawdź opcje i kliknij przycisk **Zakończ**.

Po zakończeniu konfiguracji nowe zatwierdzenia w repozytorium wybrane są wdrażane stale w aplikację usługi aplikacji.

## <a name="disable-continuous-deployment"></a>Wyłącz ciągłe wdrażanie

Aby wyłączyć ciągłego wdrażania, przejdź do strony aplikacji usługi App Service w [portalu Azure](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **GitHub** lub **VSTS** lub **BitBucket**  >  **Rozłączyć**.

![](media/app-service-continuous-deployment/disable.png)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Jak badać typowe problemy z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Jak korzystać z programu PowerShell dla platformy Azure]
* [Dokumentacja usługi Git]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* [Umożliwia automatyczne generowanie potoku CI/CD, aby wdrożyć aplikację ASP.NET 4 Azure](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Jak korzystać z programu PowerShell dla platformy Azure]: /powershell/azureps-cmdlets-docs
[Dokumentacja usługi Git]: http://git-scm.com/documentation

[Tworzenie repozytorium (GitHub)]: https://help.github.com/articles/create-a-repo
[Tworzenie repozytorium (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Wprowadzenie do usług VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
