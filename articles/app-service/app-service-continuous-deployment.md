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
ms.openlocfilehash: 4d3f1c66c6403720bf02c80af1d6833dc3cee3f1
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055522"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie w usłudze Azure App Service
W tym artykule dowiesz się, jak skonfigurować ciągłe wdrażanie dla [usługi Azure App Service](app-service-web-overview.md). Usługa App Service umożliwia ciągłe wdrażanie z usług BitBucket, GitHub i [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) przez pobieranie najnowszych aktualizacji z istniejącym repozytorium w jednej z tych usług.

Aby dowiedzieć się, jak skonfigurować ciągłe wdrażanie ręcznie z repozytorium chmury nie są wyświetlane w portalu Azure (takie jak [GitLab](https://gitlab.com/)), zobacz [Konfigurowanie ciągłego wdrażania za pomocą wymagane ręczne wykonanie czynności](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

Opublikuj repozytorium przygotowany do jednego z obsługiwanych usług. Aby uzyskać więcej informacji dotyczących publikowania projektu w tych usługach, zobacz [Tworzenie repozytorium (GitHub)], [Tworzenie repozytorium (BitBucket)], i [Wprowadzenie do usług VSTS].

## <a name="deploy-continuously-from-github"></a>Ciągłe wdrażanie z repozytorium GitHub

Aby włączyć ciągłe wdrażanie za pomocą usługi GitHub, przejdź do strony aplikacji usługi App Service w [witryny Azure portal](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **GitHub** > **Autoryzuj**. Postępuj zgodnie z monitami autoryzacji. 

![](media/app-service-continuous-deployment/github-choose-source.png)

Należy przeprowadzić autoryzację w usłudze GitHub, jeden raz. Jeśli masz już uprawnienia, po prostu kliknij **Kontynuuj**. Autoryzowane konta usługi GitHub można zmienić, klikając **zmienić konto**.

![](media/app-service-continuous-deployment/github-continue.png)

W **dostawcę konstrukcji** stronie, wybierz dostawcę kompilacji i kliknij pozycję > **Kontynuuj**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opcja 1: użycie serwer kompilacji Kudu usługi App Service

W **Konfiguruj** Wybierz organizację, repozytorium i gałąź, z których chcesz wdrażać w sposób ciągły. Po zakończeniu kliknij przycisk **Kontynuuj**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Opcja 2: Użyj ciągłego dostarczania usługi VSTS

> [!NOTE]
> Dla usługi App Service do tworzenia niezbędne kompilacji i wydania definicje na Twoim koncie usług VSTS, Twoje konto platformy Azure musi mieć rolę **właściciela** w subskrypcji platformy Azure.
>

W **Konfiguruj** stronie **kodu** Wybierz organizację, repozytorium i gałąź, z których chcesz wdrażać w sposób ciągły. Po zakończeniu kliknij przycisk **Kontynuuj**.

W **Konfiguruj** stronie **kompilacji** sekcji, skonfigurować nowe konto usługi VSTS lub wybrać istniejące konto. Po zakończeniu kliknij przycisk **Kontynuuj**.

> [!NOTE]
> Jeśli chcesz użyć istniejącego konta usługi VSTS, którego nie ma na liście, musisz [łączenia konta usługi VSTS ze swoją subskrypcją platformy Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

W **testu** stronie, wybierz, czy włączyć testów obciążenia, a następnie kliknij przycisk **Kontynuuj**.

W zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/) planu usługi App Service może również zostać wyświetlony **Deploy do wdrażania przejściowego** strony. Wybierz opcję [Włącz miejsca wdrożenia](web-sites-staged-publishing.md), następnie kliknij przycisk **Kontynuuj**.

### <a name="finish-configuration"></a>Zakończ konfigurację

W **Podsumowanie** strony, sprawdź opcje i kliknij przycisk **Zakończ**.

Po ukończeniu konfiguracji nowego zatwierdzenia w wybranym repozytorium są wdrażane stale w aplikacji usługi app Service.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>Ciągłe wdrażanie z BitBucket

Aby włączyć ciągłe wdrażanie za pomocą usługi BitBucket, przejdź do strony aplikacji usługi App Service w [witryny Azure portal](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **BitBucket** > **Autoryzuj**. Postępuj zgodnie z monitami autoryzacji. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

Należy przeprowadzić autoryzację w usłudze BitBucket, jeden raz. Jeśli masz już uprawnienia, po prostu kliknij **Kontynuuj**. Można zmienić autoryzowanych kont BitBucket, klikając **zmienić konto**.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

W **Konfiguruj** wybierz repozytorium i gałąź, z których chcesz wdrażać w sposób ciągły. Po zakończeniu kliknij przycisk **Kontynuuj**.

W **Podsumowanie** strony, sprawdź opcje i kliknij przycisk **Zakończ**.

Po ukończeniu konfiguracji nowego zatwierdzenia w wybranym repozytorium są wdrażane stale w aplikacji usługi app Service.

## <a name="deploy-continuously-from-vsts"></a>Ciągłe wdrażanie z usługi VSTS

Aby włączyć ciągłe wdrażanie za pomocą usługi VSTS, przejdź do strony aplikacji usługi App Service w [witryny Azure portal](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **VSTS** > **Kontynuuj**. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

W **dostawcę konstrukcji** stronie, wybierz dostawcę kompilacji i kliknij pozycję > **Kontynuuj**.

### <a name="option-1-use-app-service-kudu-build-server"></a>Opcja 1: użycie serwer kompilacji Kudu usługi App Service

W **Konfiguruj** wybierz konto usługi VSTS, projektów, repozytorium i gałąź, z którego chcesz przeprowadzać ciągłe wdrażanie. Po zakończeniu kliknij przycisk **Kontynuuj**.

### <a name="option-2-use-vsts-continuous-delivery"></a>Opcja 2: Użyj ciągłego dostarczania usługi VSTS

> [!NOTE]
> Dla usługi App Service do tworzenia niezbędne kompilacji i wydania definicje na Twoim koncie usług VSTS, Twoje konto platformy Azure musi mieć rolę **właściciela** w subskrypcji platformy Azure.
>

W **Konfiguruj** stronie **kodu** wybierz konto usługi VSTS, projektów, repozytorium i gałąź, z którego chcesz przeprowadzać ciągłe wdrażanie. Po zakończeniu kliknij przycisk **Kontynuuj**.

> [!NOTE]
> Jeśli chcesz użyć istniejącego konta usługi VSTS, którego nie ma na liście, musisz [łączenia konta usługi VSTS ze swoją subskrypcją platformy Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

W **Konfiguruj** stronie **kompilacji** sekcji, określ struktury języka, którego usługi VSTS, powinna używać do uruchamiania zadań kompilacji dla wybranego repozytorium. Po zakończeniu kliknij przycisk **Kontynuuj**.

W **testu** stronie, wybierz, czy włączyć testów obciążenia, a następnie kliknij przycisk **Kontynuuj**.

W zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/) planu usługi App Service może również zostać wyświetlony **Deploy do wdrażania przejściowego** strony. Wybierz opcję [Włącz miejsca wdrożenia](web-sites-staged-publishing.md), następnie kliknij przycisk **Kontynuuj**. 

### <a name="finish-configuration"></a>Zakończ konfigurację

W **Podsumowanie** strony, sprawdź opcje i kliknij przycisk **Zakończ**.

Po ukończeniu konfiguracji nowego zatwierdzenia w wybranym repozytorium są wdrażane stale w aplikacji usługi app Service.

## <a name="disable-continuous-deployment"></a>Wyłączanie ciągłego wdrażania

Aby wyłączyć ciągłe wdrażanie, przejdź do strony aplikacji usługi App Service w [witryny Azure portal](https://portal.azure.com).

W menu po lewej stronie kliknij **Centrum wdrażania** > **GitHub** lub **VSTS** lub **BitBucket**  >  **Rozłączyć**.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Jak badać typowe problemy z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Jak korzystać z programu PowerShell dla platformy Azure]
* [Dokumentacja usługi Git]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* [Umożliwia automatyczne generowanie potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć aplikację platformy ASP.NET 4 platformy Azure](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Jak korzystać z programu PowerShell dla platformy Azure]: /powershell/azureps-cmdlets-docs
[Dokumentacja usługi Git]: http://git-scm.com/documentation

[Tworzenie repozytorium (GitHub)]: https://help.github.com/articles/create-a-repo
[Tworzenie repozytorium (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Wprowadzenie do usług VSTS]: https://www.visualstudio.com/docs/vsts-tfs-overview
