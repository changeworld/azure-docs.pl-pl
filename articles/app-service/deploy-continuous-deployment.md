---
title: Ciągłe wdrażanie — usłudze Azure App Service | Dokumentacja firmy Microsoft
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
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836801"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie w usłudze Azure App Service

[Usługa Azure App Service](overview.md) umożliwia ciągłe wdrażanie z repozytorium GitHub, BitBucket, oraz [repozytoriów Azure](https://azure.microsoft.com/services/devops/repos/) repozytoriów przez pobieranie najnowszych aktualizacji. W tym artykule dowiesz się, jak ciągłe wdrażanie aplikacji przy użyciu usługi kompilacji Kudu za pomocą witryny Azure portal lub [potoki Azure](https://azure.microsoft.com/services/devops/pipelines/). 

Aby uzyskać więcej informacji na temat usług kontroli źródła, zobacz [Tworzenie repozytorium (GitHub)], [Tworzenie repozytorium (BitBucket)], lub [Tworzenie nowego repozytorium Git (repozytoriów platformy Azure)].

Aby ręcznie skonfigurować ciągłe wdrażanie z repozytorium chmury, który portal nie obsługuje bezpośrednio, takich jak [GitLab](https://gitlab.com/), zobacz [skonfigurować ciągłe wdrażanie za pomocą wymagane ręczne wykonanie czynności](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Zezwolić usłudze Azure App Service 

Aby korzystać z repozytoriów platformy Azure, upewnij się, że Twoja organizacja DevOps platformy Azure jest połączony z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [skonfigurowane konto usługom DevOps platformy Azure, dzięki czemu można wdrożyć w aplikacji sieci web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Zezwolić usłudze Azure App Service, aby nawiązać połączenie z repozytorium Bitbucket lub GitHub. Należy przeprowadzić autoryzację w usłudze usługi kontroli źródła, jeden raz. 

1. Wybierz **App Services** w [witryny Azure portal](https://portal.azure.com) lewy pasek nawigacyjny, a następnie wybierz aplikację sieci web, którą chcesz wdrożyć. 
   
1. Na stronie aplikacji wybierz **Centrum wdrażania** w menu po lewej stronie.
   
1. Na **Centrum wdrażania** wybierz opcję **GitHub** lub **Bitbucket**, a następnie wybierz pozycję **Autoryzuj**. 
   
   ![Wybierz usługi kontroli źródła, a następnie wybierz autoryzacji.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Zaloguj się do usługi, jeśli to konieczne i postępuj zgodnie z instrukcjami autoryzacji. 

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie 

Po upoważnisz usługi kontroli źródła, należy skonfigurować aplikację do ciągłego wdrażania za pomocą wbudowanych [serwer kompilacji Kudu App Service](#option-1-use-the-app-service-build-service), lub za pomocą [potoki Azure](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Option 1: Usługa App Service w kompilacji

Możesz użyć wbudowanej usługi App Service Kudu kompilacji serwera ciągłe wdrażanie z usługi GitHub, Bitbucket lub repozytoriów platformy Azure. 

1. Wybierz **App Services** w [witryny Azure portal](https://portal.azure.com) lewy pasek nawigacyjny, a następnie wybierz aplikację sieci web, którą chcesz wdrożyć. 
   
1. Na stronie aplikacji wybierz **Centrum wdrażania** w menu po lewej stronie.
   
1. Wybierz Twój dostawca kontroli źródła autoryzowanych **Centrum wdrażania** strony i wybierz **Kontynuuj**. Dla usługi GitHub lub Bitbucket, możesz również wybrać opcję **zmienić konto** do zmieniania konta używanego autoryzowanych. 
   
   > [!NOTE]
   > Aby korzystać z repozytoriów platformy Azure, upewnij się, że Twoja organizacja usługom DevOps platformy Azure jest połączony z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [skonfigurowane konto usługom DevOps platformy Azure, dzięki czemu można wdrożyć w aplikacji sieci web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   
1. Dla usługi GitHub i repozytoriów platformy Azure na **dostawcę konstrukcji** wybierz opcję **usługi App Service Utwórz usługę**, a następnie wybierz pozycję **Kontynuuj**. Bitbucket zawsze używa usługi kompilacji usługi App Service.
   
   ![Wybierz usługę kompilacji usługi App Service, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na **Konfiguruj** strony:
   
   - Usługi github, listy rozwijanej i wybierz **organizacji**, **repozytorium**, i **gałęzi** mają zostać wdrożone w sposób ciągły.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, konieczne może być autoryzowania usługi Azure App Service w witrynie GitHub. Przejdź do repozytorium GitHub, a następnie przejdź do **ustawienia** > **aplikacje** > **autoryzowanych aplikacji OAuth**. Wybierz **usługi Azure App Service**, a następnie wybierz pozycję **Grant**.
     
   - Bitbucket, wybierz Bitbucket **zespołu**, **repozytorium**, i **gałęzi** mają zostać wdrożone w sposób ciągły.
     
   - Dla repozytoriów platformy Azure wybierz **Azure DevOps organizacji**, **projektu**, **repozytorium**, i **gałęzi** mają zostać wdrożone w sposób ciągły.
     
     > [!NOTE]
     > Jeśli Twoja organizacja DevOps platformy Azure nie ma na liście, upewnij się, że jest połączony z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [skonfigurowane konto usługom DevOps platformy Azure, dzięki czemu można wdrożyć w aplikacji sieci web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. Wybierz **nadal**.
   
   ![Wypełnij informacje o repozytorium, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po skonfigurowaniu dostawcę konstrukcji, sprawdź ustawienia na **Podsumowanie** strony, a następnie wybierz pozycję **Zakończ**.
   
   Nowe zatwierdzenia w wybranym repozytorium i gałąź teraz ciągłe wdrażanie w aplikacji usługi app Service. Można śledzić zatwierdzenia i wdrożenia, które znajdują się na **Centrum wdrażania** strony.
   
   ![Śledzenie zatwierdzenia i wdrożenia w Centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Opcja 2: Skorzystaj z usługi Azure Pipelines 

Jeśli Twoje konto ma odpowiednie uprawnienia, możesz skonfigurować potoki usługi Azure, aby ciągłe wdrażanie z repozytoriów GitHub i repozytoriów platformy Azure. Aby uzyskać więcej informacji na temat wdrażania za pomocą potoków usługi Azure, zobacz [wdrażanie aplikacji sieci web w usłudze Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Dla usługi Azure App Service, aby utworzyć ciągłe dostarczanie potoki usługi Azure w Twojej organizacji DevOps platformy Azure: 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisu do usługi Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć **właściciela** roli w subskrypcji platformy Azure.

- Musisz być administratorem projektu DevOps platformy Azure, którego chcesz użyć.

Aby skonfigurować potoki usługi Azure (wersja zapoznawcza):

1. Wybierz **App Services** w [witryny Azure portal](https://portal.azure.com) lewy pasek nawigacyjny, a następnie wybierz aplikację sieci web, którą chcesz wdrożyć. 
   
1. Na stronie aplikacji wybierz **Centrum wdrażania** w menu po lewej stronie.
   
1. Na **dostawcę konstrukcji** wybierz opcję **potoki usługi Azure (wersja zapoznawcza)** , a następnie wybierz pozycję **Kontynuuj**. 
   
1. Na **Konfiguruj** stronie **kodu** sekcji:
   
   - Usługi github, listy rozwijanej i wybierz **organizacji**, **repozytorium**, i **gałęzi** mają zostać wdrożone w sposób ciągły.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, konieczne może być autoryzowania usługi Azure App Service w witrynie GitHub. Przejdź do repozytorium GitHub, a następnie przejdź do **ustawienia** > **aplikacje** > **autoryzowanych aplikacji OAuth**. Wybierz **usługi Azure App Service**, a następnie wybierz pozycję **Grant**.
     
   - Dla repozytoriów platformy Azure wybierz **Azure DevOps organizacji**, **projektu**, **repozytorium**, i **gałęzi** mają zostać wdrożone w sposób ciągły, lub konfigurowania nowej organizacji DevOps platformy Azure.
     
     > [!NOTE]
     > Istniejącej organizacji DevOps platformy Azure nie ma na liście, konieczne może się połączyć subskrypcję platformy Azure. Aby uzyskać więcej informacji, zobacz [zdefiniować potok wydania CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Wybierz **nadal**.
   
1. Dla repozytoriów platformy Azure w **kompilacji** sekcji, określ struktury języka, potoki usługi Azure należy używać do uruchamiania zadań kompilacji, a następnie wybierz pozycję **Kontynuuj**.
   
1. Na **testu** stronie, wybierz, czy włączyć testów obciążenia, a następnie wybierz **Kontynuuj**.
   
1. W zależności od planu usługi App Service [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/), może zostać wyświetlony **Deploy do wdrażania przejściowego** strony. Wybierz opcję [Włącz miejsca wdrożenia](deploy-staging-slots.md), a następnie wybierz pozycję **Kontynuuj**.
   
   > [!NOTE]
   > Potoki platformy Azure nie zezwala na ciągłe dostarczanie do miejsca produkcji. To ograniczenie zapobiega przypadkowemu wdrożenia do środowiska produkcyjnego. Konfigurowanie ciągłego dostarczania w miejscu przejściowym, sprawdź tam zmiany, a następnie zamienić gniazd, gdy wszystko jest gotowe.
   
1. Po skonfigurowaniu dostawcę konstrukcji, sprawdź ustawienia na **Podsumowanie** strony, a następnie wybierz pozycję **Zakończ**.
   
   Nowe zatwierdzenia w wybranym repozytorium i gałąź teraz ciągłe wdrażanie w aplikacji usługi app Service. Można śledzić zatwierdzenia i wdrożenia, które znajdują się na **Centrum wdrażania** strony.
   
   ![Śledzenie zatwierdzenia i wdrożenia w Centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Wyłączanie ciągłego wdrażania

Aby wyłączyć ciągłe wdrażanie, wybierz **rozłączenia** w górnej części aplikacji **Centrum wdrażania** strony.

![Wyłączanie ciągłego wdrażania](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Badać typowe problemy z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Korzystanie z programu Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Dokumentacja usługi Git](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Tworzenie repozytorium (GitHub)]: https://help.github.com/articles/create-a-repo
[Tworzenie repozytorium (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Tworzenie nowego repozytorium Git (repozytoriów platformy Azure)]: /azure/devops/repos/git/creatingrepo
