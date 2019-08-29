---
title: Ciągłe wdrażanie — Azure App Service | Microsoft Docs
description: Dowiedz się, jak włączyć ciągłe wdrażanie w usłudze Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 98f82914af8216789a04d3cfd2972f83c16b3fa0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070661"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Ciągłe wdrażanie do Azure App Service

[Azure App Service](overview.md) umożliwia ciągłe wdrażanie z repozytorium GitHub, BitBucket i [Azure Repos](https://azure.microsoft.com/services/devops/repos/) repozytoriów przez ściąganie najnowszych aktualizacji. W tym artykule pokazano, jak używać Azure Portal do ciągłego wdrażania aplikacji za pomocą usługi kompilacji kudu lub [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/). 

Aby uzyskać więcej informacji na temat usług kontroli źródła, zobacz [Tworzenie repozytorium (GitHub)], [Tworzenie repozytorium (BitBucket)]lub [Utwórz nowe repozytorium git (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autoryzuj Azure App Service 

Aby użyć Azure Repos, upewnij się, że organizacja usługi Azure DevOps jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci Web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

W przypadku usługi BitBucket lub GitHub Autoryzuj Azure App Service, aby nawiązać połączenie z repozytorium. Wystarczy tylko autoryzować z usługą kontroli źródła. 

1. Wybierz pozycję **App Services** w lewym [Azure Portal](https://portal.azure.com) nawigacyjnym, a następnie wybierz aplikację sieci Web, którą chcesz wdrożyć. 
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.
   
1. Na stronie **centrum wdrażania** wybierz pozycję **GitHub** lub **BitBucket**, a następnie wybierz pozycję **Autoryzuj**. 
   
   ![Wybierz pozycję Usługa kontroli źródła, a następnie wybierz pozycję Autoryzuj.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. W razie potrzeby zaloguj się do usługi i postępuj zgodnie z monitami o autoryzację. 

## <a name="enable-continuous-deployment"></a>Włącz ciągłe wdrażanie 

Po zatwierdzeniu usługi kontroli źródła Skonfiguruj aplikację do ciągłego wdrażania za pomocą wbudowanego [serwera Kudu App Service Build](#option-1-use-the-app-service-build-service)lub [Azure Pipelines](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Option 1: Korzystanie z usługi kompilacji App Service

Wbudowanego kudu App Service Build Server można użyć do ciągłego wdrażania z usługi GitHub, BitBucket lub Azure Repos. 

1. Wybierz pozycję **App Services** w lewym [Azure Portal](https://portal.azure.com) nawigacyjnym, a następnie wybierz aplikację sieci Web, którą chcesz wdrożyć. 
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.
   
1. Wybierz autoryzowanego dostawcę kontroli źródła na stronie **centrum wdrażania** , a następnie wybierz pozycję **Kontynuuj**. W witrynie GitHub lub BitBucket można także wybrać pozycję **Zmień konto** , aby zmienić autoryzowane konto. 
   
   > [!NOTE]
   > Aby użyć Azure Repos, upewnij się, że Azure DevOps Services organizacja jest połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci Web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. W witrynie GitHub lub Azure Repos na stronie **dostawca kompilacji** wybierz pozycję **App Service usługa kompilacji**, a następnie wybierz pozycję **Kontynuuj**. BitBucket zawsze używa usługi kompilacji App Service.
   
   ![Wybierz pozycję App Service Build Service, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Na stronie **Konfigurowanie** :
   
   - W witrynie GitHub wybierz pozycję **organizacja**, **repozytorium**i **gałąź** , którą chcesz stale wdrożyć.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Azure App Service w serwisie GitHub. Przejdź do repozytorium GitHub i przejdź do pozycji **Ustawienia** > **aplikacje** > **autoryzowane aplikacje uwierzytelniania OAuth**. Wybierz pozycję **Azure App Service**, a następnie wybierz pozycję **Udziel**.
     
   - W przypadku BitBucket wybierz **zespół**BitBucket, **repozytorium**i **gałąź** , które chcesz wdrożyć w sposób ciągły.
     
   - W obszarze Azure Repos wybierz organizację, **projekt**, **repozytorium**i **gałąź** **usługi Azure DevOps**, która ma być ciągle wdrażana.
     
     > [!NOTE]
     > Jeśli Twoja organizacja usługi Azure DevOps nie znajduje się na liście, upewnij się, że jest ona połączona z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konta Azure DevOps Services, aby można było je wdrożyć w aplikacji sieci Web](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Wybierz **nadal**.
   
   ![Wypełnij pola informacje o repozytorium, a następnie wybierz pozycję Kontynuuj.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie** , a następnie wybierz pozycję **Zakończ**.
   
   Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w aplikacji App Service. Zatwierdzenia i wdrożenia można śledzić na stronie **centrum wdrażania** .
   
   ![Śledzenie zatwierdzeń i wdrożeń w centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Opcja 2: Skorzystaj z usługi Azure Pipelines 

Jeśli Twoje konto ma wymagane uprawnienia, możesz skonfigurować Azure Pipelines do ciągłego wdrażania z repozytorium GitHub lub Azure Repos repozytoriów. Aby uzyskać więcej informacji o wdrażaniu za pomocą Azure Pipelines, zobacz [wdrażanie aplikacji sieci Web na platformie Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Aby uzyskać Azure App Service do tworzenia Azure Pipelines ciągłego dostarczania w organizacji usługi Azure DevOps: 

- Twoje konto platformy Azure musi mieć uprawnienia do zapisu w Azure Active Directory i tworzenia usługi. 
  
- Twoje konto platformy Azure musi mieć rolę **właściciela** w ramach subskrypcji platformy Azure.

- Musisz być administratorem w projekcie usługi Azure DevOps, którego chcesz użyć.

Aby skonfigurować Azure Pipelines (wersja zapoznawcza):

1. Wybierz pozycję **App Services** w lewym [Azure Portal](https://portal.azure.com) nawigacyjnym, a następnie wybierz aplikację sieci Web, którą chcesz wdrożyć. 
   
1. Na stronie aplikacja wybierz pozycję **centrum wdrażania** w menu po lewej stronie.
   
1. Na stronie **dostawca kompilacji** wybierz pozycję **Azure Pipelines (wersja zapoznawcza)** , a następnie wybierz pozycję **Kontynuuj**. 
   
1. Na stronie **Konfigurowanie** w sekcji **kod** :
   
   - W witrynie GitHub wybierz pozycję **organizacja**, **repozytorium**i **gałąź** , którą chcesz stale wdrożyć.
     
     > [!NOTE]
     > Jeśli nie widzisz żadnych repozytoriów, może być konieczne autoryzowanie Azure App Service w serwisie GitHub. Przejdź do repozytorium GitHub i przejdź do pozycji **Ustawienia** > **aplikacje** > **autoryzowane aplikacje uwierzytelniania OAuth**. Wybierz pozycję **Azure App Service**, a następnie wybierz pozycję **Udziel**.
     
   - W obszarze Azure Repos wybierz **organizację Azure DevOps**, **projekt**, **repozytorium**i **gałąź** , którą chcesz wdrożyć w sposób ciągły, lub skonfiguruj nową organizację usługi Azure DevOps.
     
     > [!NOTE]
     > Jeśli istniejąca organizacja usługi Azure DevOps nie jest wymieniona, może być konieczne połączenie jej z subskrypcją platformy Azure. Aby uzyskać więcej informacji, zobacz [Definiowanie potoku wydania dysku CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Wybierz **nadal**.
   
1. W przypadku Azure Repos w sekcji **kompilacja** określ strukturę języka, która Azure Pipelines powinna być używana do uruchamiania zadań kompilacji, a następnie wybierz pozycję **Kontynuuj**.
   
1. Na stronie **test** wybierz, czy włączyć testy obciążenia, a następnie wybierz pozycję **Kontynuuj**.
   
1. W zależności od [warstwy cenowej](https://azure.microsoft.com/pricing/details/app-service/plans/)planu App Service może zostać wyświetlona strona **wdrażanie do przemieszczania** . Zdecyduj, czy chcesz [włączyć miejsca wdrożenia](deploy-staging-slots.md), a następnie wybierz pozycję **Kontynuuj**.
   
   > [!NOTE]
   > Azure Pipelines nie umożliwia ciągłego dostarczania do miejsca produkcyjnego. To ograniczenie zapobiega przypadkowym wdrożeniom w środowisku produkcyjnym. Skonfiguruj ciągłe dostarczanie do miejsca przejściowego, Sprawdź zmiany tam, a następnie Zamień miejsca, gdy wszystko będzie gotowe.
   
1. Po skonfigurowaniu dostawcy kompilacji przejrzyj ustawienia na stronie **Podsumowanie** , a następnie wybierz pozycję **Zakończ**.
   
   Nowe zatwierdzenia w wybranym repozytorium i rozgałęzieniu teraz wdrażają się w sposób ciągły w aplikacji App Service. Zatwierdzenia i wdrożenia można śledzić na stronie **centrum wdrażania** .
   
   ![Śledzenie zatwierdzeń i wdrożeń w centrum wdrażania](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Wyłącz ciągłe wdrażanie

Aby wyłączyć ciągłe wdrażanie, wybierz pozycję Rozłącz w górnej części strony **centrum wdrażania** aplikacji.

![Wyłącz ciągłe wdrażanie](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Użyj nieobsługiwanych repozytoriów

W przypadku aplikacji systemu Windows możesz ręcznie skonfigurować ciągłe wdrażanie z repozytorium Git lub Mecurial w chmurze, które nie jest bezpośrednio obsługiwane przez portal, na przykład [GitLab](https://gitlab.com/). Możesz to zrobić, wybierając pole zewnętrzne na stronie **centrum wdrażania** . Aby uzyskać więcej informacji, zobacz [Konfigurowanie ciągłego wdrażania przy użyciu kroków ręcznych](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zbadaj typowe problemy związane z ciągłym wdrażaniem](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Korzystanie z programu Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Dokumentacja usługi Git](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Tworzenie repozytorium (GitHub)]: https://help.github.com/articles/create-a-repo
[Tworzenie repozytorium (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Utwórz nowe repozytorium git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
