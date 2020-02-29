---
title: Samouczek — zintegrowana DevOps dla IaaS i PaaS na platformie Azure
description: W tym samouczku dowiesz się, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie aplikacji na maszynach wirtualnych platformy Azure przy użyciu Azure Pipelines.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912532"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Samouczek: zintegrowana DevOps dla IaaS i PaaS na platformie Azure

Kompleksowe rozwiązania na platformie Azure umożliwiają zespołom wdrażanie DevOps praktyk w każdej fazie cyklu życia aplikacji: planowanie, opracowywanie, dostarczanie i obsługiwanie. 

Poniżej znajdują się niektóre usługi platformy Azure, które upraszczają obciążenia w chmurze i mogą być połączone w celu zapewnienia bardzo zaawansowanych scenariuszy.
Te technologie, w połączeniu z osobami i procesami, umożliwiają zespołom ciągłe udostępnianie wartości klientom. 

- Azure: https://portal.azure.com — Portal do tworzenia obciążeń w chmurze. Zarządzanie i monitorowanie wszystkiego z prostych aplikacji sieci Web w złożonych aplikacjach w chmurze 
- Azure DevOps: https://dev.azure.com — Planuj inteligentniejsze, współpracuj lepiej i dostarczaj szybciej dzięki zestawowi nowoczesnych usług deweloperskich 
- Azure Machine Learning Studio: przygotowywanie https://ml.azure.com danych, uczenie i wdrażanie modeli uczenia maszynowego 
 

Azure DevOps to wbudowana usługa platformy Azure, która automatyzuje każdą część procesu DevOps z ciągłą integracją i ciągłym dostarczaniem dla dowolnego zasobu platformy Azure.
Bez względu na to, czy Twoja aplikacja korzysta z maszyn wirtualnych, aplikacji sieci Web, Kubernetes czy innego zasobu, można zaimplementować infrastrukturę jako kod, ciągłą integrację, ciągłe testowanie, ciągłe dostarczanie i ciągłe monitorowanie za pomocą platformy Azure i usługi Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS — Konfigurowanie ciągłej integracji/ciągłego dostarczania 
Azure Pipelines zawiera kompletny, w pełni funkcjonalny zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych. Potok ciągłego dostarczania można skonfigurować dla maszyny wirtualnej platformy Azure bezpośrednio z poziomu Azure Portal. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania dla wdrożeń obejmujących wiele maszyn z Azure Portal. Konfigurowanie ciągłej integracji/ciągłego wdrażania Virtual Machines.

Maszyny wirtualne mogą być dodawane jako elementy docelowe do [grupy wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) i mogą być przeznaczone do aktualizacji stopniowych obejmujących wiele maszyn. Widoki historii wdrożenia w ramach grup wdrożeń zapewniają możliwość śledzenia z maszyny wirtualnej do potoku, a następnie do zatwierdzenia. 
 
**Aktualizacje stopniowe**: wdrożenie stopniowe zastępuje wystąpienia poprzedniej wersji aplikacji wystąpieniami nowej wersji aplikacji na stałym zestawie maszyn (zestaw stopniowy) w każdej iteracji. Zobaczmy, jak skonfigurować aktualizację stopniową dla maszyn wirtualnych.  
Można skonfigurować aktualizacje stopniowe dla "**maszyn wirtualnych**" w Azure Portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku. 
1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej. 
2. W lewym okienku maszyny wirtualnej przejdź do menu  **ciągłego dostarczania** . Następnie kliknij pozycję **Konfiguruj**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. W panelu Konfiguracja kliknij pozycję "organizacja usługi Azure DevOps", aby wybrać istniejące konto, lub utwórz je. Następnie wybierz projekt, w którym chcesz skonfigurować potok.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Grupa wdrożenia to logiczny zestaw maszyn docelowych wdrożenia, które reprezentują środowiska fizyczne; na przykład "dev", "test", "przeprowadzających" i "Product". Można utworzyć nową grupę wdrożenia lub wybrać istniejącą grupę wdrożenia. Opcjonalnie możesz oznakować maszynę z rolą. Na przykład "Web", "DB" itd.  
5. Kliknij przycisk **OK** w oknie dialogowym, aby skonfigurować potok ciągłego dostarczania. 
6. Po wykonaniu tej czynności będziesz mieć potok ciągłego dostarczania skonfigurowany do wdrożenia na maszynie wirtualnej.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Zobaczysz, że wdrożenie maszyny wirtualnej jest w toku. Możesz kliknąć link, aby przejść do potoku. Kliknij pozycję **Release-1** , aby wyświetlić wdrożenie. Możesz też kliknąć pozycję **Edytuj** , aby zmodyfikować definicję potoku wydania. 
8. Jeśli masz wiele maszyn wirtualnych do skonfigurowania, powtórz kroki 2-5 dla innych maszyn wirtualnych, które mają zostać dodane do grupy wdrożenia. 
9. Po zakończeniu kliknij definicję potoku, przejdź do organizacji usługi Azure DevOps, a następnie kliknij pozycję **Edytuj** potok wersji. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Kliknij **zadanie Link 1, 1 zadanie** w fazie **deweloperskiej** . Kliknij fazę **Wdróż** .  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. W okienku Konfiguracja po prawej stronie można zobaczyć, że domyślnie potok jest skonfigurowany tak, aby przetworzyć stopniowaną aktualizację wszystkich obiektów docelowych. Wdrożenia można skonfigurować tak, aby były wykonywane pojedynczo lub pod względem wartości procentowej przy użyciu suwaka.  
  
  
System **Kanaryjskie** zmniejsza ryzyko przez spowolnienie w przypadku zmiany do małego podzbioru użytkowników. W miarę zwiększania zaufania do nowej wersji można rozpocząć zwalnianie jej na więcej serwerów w infrastrukturze i kierowanie do niej większej liczby użytkowników. Wdrożenia programu Kanaryjskie można skonfigurować dla "**maszyn wirtualnych**" Azure Portal przy użyciu opcji ciągłego dostarczania. Oto przewodnik krok po kroku. 
1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej 
2. Postępuj zgodnie z instrukcjami 2-5 w poprzedniej sekcji, aby dodać wiele maszyn wirtualnych do grupy wdrożenia. 
3. Dodaj tag niestandardowy do maszyn wirtualnych, które mają być częścią wdrożeń programu Kanaryjskie. Na przykład "Kanaryjskie".
4. Po skonfigurowaniu potoku dla maszyn wirtualnych kliknij potok, uruchom usługę Azure DevOps Organization, **Edytuj** potok i przejdź do etapu **dev** . Dodaj tag do filtru "Kanaryjskie". 
5. Dodaj kolejną fazę grupy wdrożenia, skonfiguruj fazę ze znacznikami, aby wskazać pozostałe maszyny wirtualne w grupie wdrożenia.  
6. Opcjonalnie można skonfigurować ręczny krok walidacji, który może podwyższyć/odrzucić wdrożenia programu Kanaryjskie. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Niebieski-zielony** redukuje przestoje wdrożenia, mając identyczne środowisko rezerwowe. W dowolnym momencie w jednym z tych środowisk jest aktywny. Podczas przygotowywania do nowej wersji należy przeprowadzić końcowy etap testowania w środowisku zielonym. Gdy oprogramowanie działa w środowisku zielonym, należy przełączyć ruch, aby wszystkie żądania przychodzące przechodzą do zielonego środowiska — niebieskie środowisko jest teraz w stanie bezczynności.
Wdrożenia Blue-Green można skonfigurować dla "**maszyn wirtualnych**" z Azure Portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku. 

1. Zaloguj się do Azure Portal i przejdź do maszyny wirtualnej 
2. Postępuj zgodnie z instrukcjami 2-5 w sekcji **aktualizacje stopniowe** , aby dodać wiele maszyn wirtualnych do grupy wdrożenia. Dodaj tag niestandardowy do maszyn wirtualnych, które mają być częścią wdrożeń Blue-Green. Na przykład "Blue" lub "Green" dla maszyn wirtualnych dla roli autonomicznej. 
3. Po skonfigurowaniu potoku dla maszyn wirtualnych kliknij potok, uruchom usługę Azure DevOps Organization, **Edytuj** potok, przejdź do etapu **dev** . Dodaj tag do filtru "Green". 
4. Dodaj fazę bez agenta, skonfiguruj fazę z ręcznym etapem walidacji oraz krokem interfejsu API Invoke-REST, aby zamienić Tagi. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Projekt usługi Azure DevOps 
Rozpocznij pracę z platformą Azure łatwiej niż kiedykolwiek wcześniej.
 
Za pomocą DevOps Projects Zacznij uruchamiać aplikację w dowolnej usłudze platformy Azure w zaledwie trzech krokach: Wybierz język aplikacji, środowisko uruchomieniowe i usługę platformy Azure.
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Dodatkowe zasoby 
- [Wdrażanie na platformie Azure Virtual Machines przy użyciu projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Implementowanie ciągłego wdrażania aplikacji w zestawie skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
