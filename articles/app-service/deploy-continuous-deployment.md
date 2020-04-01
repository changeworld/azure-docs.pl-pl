---
title: Konfigurowanie ciągłego wdrażania
description: Dowiedz się, jak włączyć usługę CI/CD do usługi Azure App Service w usłudze GitHub, BitBucket, usłudze Azure Repos lub innych repo. Wybierz potok kompilacji, który odpowiada Twoim potrzebom.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437240"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie usługi Azure App Service

[Usługa Azure App Service](overview.md) umożliwia ciągłe wdrażanie z repozytoriów Repozytoriów GitHub, BitBucket i [Azure Repozytoriów,](https://azure.microsoft.com/services/devops/repos/) pobierając najnowsze aktualizacje. W tym artykule pokazano, jak używać witryny Azure Portal do ciągłego wdrażania aplikacji za pośrednictwem usługi kompilacji Kudu lub [potoków platformy Azure.](https://azure.microsoft.com/services/devops/pipelines/) 

Aby uzyskać więcej informacji na temat usług kontroli źródła, zobacz [Tworzenie repozytorium (GitHub)], [Tworzenie repozytorium (BitBucket)]lub [Tworzenie nowego repozytorium Git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autoryzuj usługę Azure App Service 

Aby korzystać z usługi Azure Repos, upewnij się, że twoja organizacja Azure DevOps jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta usług Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci web.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)

W przypadku Bitbucket lub GitHub autoryzuj usługę Azure App Service, aby połączyć się z repozytorium. Wystarczy autoryzować za pomocą usługi kontroli źródła tylko raz. 

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj **usługi app services** i wybierz.

   ![Wyszukaj usługi aplikacji.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Wybierz usługę app service, którą chcesz wdrożyć.

   ![Wybierz aplikację.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Na stronie aplikacji wybierz pozycję **Centrum wdrażania** w menu po lewej stronie.
   
1. Na stronie **Centrum wdrażania** wybierz pozycję **GitHub** lub **Bitbucket**, a następnie wybierz pozycję **Autoryzuj**. 
   
   ![Wybierz usługę kontroli źródła, a następnie wybierz pozycję Autoruj.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. W razie potrzeby zaloguj się do usługi i postępuj zgodnie z instrukcjami autoryzacji. 

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie 

Po autoryzacji usługi kontroli źródła skonfiguruj aplikację do ciągłego wdrażania za pośrednictwem wbudowanego serwera [kompilacji kudu app service](#option-1-kudu-app-service) lub za pośrednictwem [usługi Azure Pipelines.](#option-2-azure-pipelines) 

### <a name="option-1-kudu-app-service"></a>Opcja 1: Usługa aplikacji Kudu

Wbudowany serwer kompilacji usługi Kudu App Service umożliwia ciągłe wdrażanie z usługi GitHub, Bitbucket lub Azure Repos. 

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj **usługi App Services,** a następnie wybierz usługę app service, którą chcesz wdrożyć. 
   
1. Na stronie aplikacji wybierz pozycję **Centrum wdrażania** w menu po lewej stronie.
   
1. Wybierz autoryzowanego dostawcę kontroli źródła na stronie **Centrum wdrażania** i wybierz pozycję **Kontynuuj**. W przypadku GitHub lub Bitbucket możesz również wybrać **zmień konto,** aby zmienić autoryzowane konto. 
   
   > [!NOTE]
   > Aby korzystać z usługi Azure Repos, upewnij się, że twoja organizacja usługi Azure DevOps services jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta usług Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci web.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)
   
1. W przypadku aplikacji GitHub lub Repozytorium platformy Azure na stronie **Dostawca kompilacji** wybierz pozycję **App Service build service**, a następnie wybierz pozycję **Kontynuuj**. Bitbucket zawsze używa usługi kompilacji usługi app service.
   
   ![Wybierz pozycję Usługa kompilacji usługi app service, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na stronie **Konfigurowanie:**
   
   - W przypadku gitHub rozwijane i wybierz **organizację,** **repozytorium**i **gałąź,** którą chcesz wdrożyć w sposób ciągły.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie usługi Azure App Service w usłudze GitHub. Przejdź do repozytorium GitHub i przejdź do **ustawień** > **aplikacji** > **Autoryzowanych aplikacji OAuth**. Wybierz **usługę Azure App Service**, a następnie wybierz pozycję **Przyznaj**. W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby udzielić uprawnień.
     
   - W przypadku bitbucket wybierz **zespół**Bitbucket , **Repozytorium**i **Gałąź,** którą chcesz wdrożyć w sposób ciągły.
     
   - W przypadku repozytorium platformy Azure wybierz **organizację,** **projekt,** **repozytorium**i **gałąź,** którą chcesz wdrożyć w sposób ciągły.
     
     > [!NOTE]
     > Jeśli twojej organizacji Azure DevOps nie ma na liście, upewnij się, że jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta usługi Azure DevOps Services, aby można było wdrożyć je w aplikacji sieci web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)..
     
1. Wybierz przycisk **Kontynuuj**.
   
   ![Wypełnij informacje o repozytorium, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie,** a następnie wybierz pozycję **Zakończ**.
   
1. Nowe zatwierdzenia w wybranym repozytorium i gałęzi teraz wdrażać stale w aplikacji usługi App Service. Zatwierdzanie i wdrożenia można śledzić na stronie **Centrum wdrażania.**
   
   ![Śledzenie zatwierdzeń i wdrożeń w Centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Opcja 2: Potoki platformy Azure 

Jeśli twoje konto ma niezbędne uprawnienia, można skonfigurować usługi Azure Pipelines do ciągłego wdrażania z usługi GitHub lub Usługi Azure Reppos. Aby uzyskać więcej informacji na temat wdrażania za pośrednictwem usługi Azure Pipelines, zobacz [Wdrażanie aplikacji sieci web w usługach Azure App Services.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)

#### <a name="prerequisites"></a>Wymagania wstępne

Aby usługa Azure App Service tworzyła ciągłe dostarczanie przy użyciu potoków platformy Azure, twoja organizacja Azure DevOps powinna mieć następujące uprawnienia: 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisywania w usłudze Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć rolę **właściciela** w subskrypcji platformy Azure.

- Musisz być administratorem w projekcie Azure DevOps, którego chcesz użyć.

#### <a name="github--azure-pipelines"></a>GitHub + potoki platformy Azure

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj **usługi App Services,** a następnie wybierz usługę app service, którą chcesz wdrożyć. 
   
1. Na stronie aplikacji wybierz pozycję **Centrum wdrażania** w menu po lewej stronie.

1. Wybierz **gitHub** jako dostawcę kontroli źródła na stronie **Centrum wdrażania** i wybierz pozycję **Kontynuuj**. W przypadku **usługi GitHub**możesz wybrać **opcję Zmień konto,** aby zmienić autoryzowane konto.

    ![kontrola źródła](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. Na stronie **Dostawca kompilacji** wybierz pozycję **Azure Pipelines (Preview),** a następnie wybierz pozycję **Kontynuuj**.

    ![dostawca kompilacji](media/app-service-continuous-deployment/select-build-provider.png)
   
1. Na stronie **Konfigurowanie** w sekcji **Kod** wybierz **pozycję Organizacja**, **Repozytorium**i **Gałąź,** którą chcesz wdrożyć w sposób ciągły, a następnie wybierz pozycję **Kontynuuj**.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie usługi Azure App Service w usłudze GitHub. Przejdź do repozytorium GitHub i przejdź do **ustawień** > **aplikacji** > **Autoryzowanych aplikacji OAuth**. Wybierz **usługę Azure App Service**, a następnie wybierz pozycję **Przyznaj**. W przypadku repozytoriów organizacji musisz być właścicielem organizacji, aby udzielić uprawnień.
       
    W sekcji **Kompilacja** określ organizację, projekt, projekt, strukturę języka, której usługi Azure Pipelines powinny używać do uruchamiania zadań kompilacji, a następnie wybierz pozycję **Kontynuuj**.

   ![dostawca kompilacji](media/app-service-continuous-deployment/build-configure.png)

1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie,** a następnie wybierz pozycję **Zakończ**.

   ![dostawca kompilacji](media/app-service-continuous-deployment/summary.png)
   
1. Nowe zatwierdzenia w wybranym repozytorium i gałęzi teraz wdrażać stale w usłudze app service. Zatwierdzanie i wdrożenia można śledzić na stronie **Centrum wdrażania.**
   
   ![Śledzenie zatwierdzeń i wdrożeń w Centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Repozytoria platformy Azure + potoki platformy Azure

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj **usługi App Services,** a następnie wybierz usługę app service, którą chcesz wdrożyć. 
   
1. Na stronie aplikacji wybierz pozycję **Centrum wdrażania** w menu po lewej stronie.

1. Wybierz **pozycję Repozytoria platformy Azure** jako dostawcę kontroli źródła na stronie Centrum **wdrażania** i wybierz pozycję **Kontynuuj**.

    ![kontrola źródła](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. Na stronie **Dostawca kompilacji** wybierz pozycję **Azure Pipelines (Preview),** a następnie wybierz pozycję **Kontynuuj**.

    ![kontrola źródła](media/app-service-continuous-deployment/azure-pipelines.png)

1. Na stronie **Konfigurowanie** w sekcji **Kod** wybierz **pozycję Organizacja**, **Repozytorium**i **Gałąź,** którą chcesz wdrożyć w sposób ciągły, a następnie wybierz pozycję **Kontynuuj**.

   > [!NOTE]
   > Jeśli istniejącej organizacji Azure DevOps nie ma na liście, może być konieczne powiązanie jej z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Definiowanie potoku wydania dysku CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   W sekcji **Kompilacja** określ organizację, projekt, projekt, strukturę języka, której usługi Azure Pipelines powinny używać do uruchamiania zadań kompilacji, a następnie wybierz pozycję **Kontynuuj**.

   ![dostawca kompilacji](media/app-service-continuous-deployment/build-configure.png)

1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie,** a następnie wybierz pozycję **Zakończ**.  
     
   ![dostawca kompilacji](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Nowe zatwierdzenia w wybranym repozytorium i gałęzi teraz wdrażać stale w usłudze app service. Zatwierdzanie i wdrożenia można śledzić na stronie **Centrum wdrażania.**

## <a name="disable-continuous-deployment"></a>Wyłączanie ciągłego wdrażania

Aby wyłączyć ciągłe wdrażanie, wybierz **pozycję Rozłącz** u góry strony **Centrum wdrażania** aplikacji.

![Wyłączanie ciągłego wdrażania](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Korzystanie z nieobsługiwało repozytoriów

W przypadku aplikacji systemu Windows można ręcznie skonfigurować ciągłe wdrażanie z repozytorium Git lub Mercurial w chmurze, którego portal nie obsługuje bezpośrednio, na przykład [GitLab](https://gitlab.com/). Należy to zrobić, wybierając pole Zewnętrzne na stronie **Centrum wdrażania.** Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego wdrażania przy użyciu kroków ręcznych](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Badanie typowych problemów związanych z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Korzystanie z programu Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Dokumentacja Git](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Tworzenie repozytorium (GitHub)]: https://help.github.com/articles/create-a-repo
[Tworzenie repozytorium (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Tworzenie nowego repozytorium Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
