---
title: Samouczek — zintegrowane programy DevOps dla usług IaaS i PaaS na platformie Azure
description: W tym samouczku dowiesz się, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie (CD) aplikacji na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Pipelines.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77912532"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>Samouczek: Zintegrowane programy DevOps dla usług IaaS i PaaS na platformie Azure

Dzięki kompleksowym rozwiązaniom na platformie Azure zespoły mogą implementować praktyki DevOps w każdej fazie cyklu życia aplikacji: planować, rozwijać, dostarczać i obsługiwać. 

Poniżej znajdują się niektóre usługi platformy Azure, które upraszczają obciążenia w chmurze i mogą być łączone w celu umożliwienia niezwykle zaawansowanych scenariuszy.
Technologie te, w połączeniu z ludźmi i procesami, umożliwiają zespołom ciągłe dostarczanie wartości klientom. 

- Platforma https://portal.azure.com Azure: — portal do tworzenia obciążeń w chmurze. Zarządzanie wszystkim, od prostych aplikacji internetowych po złożone aplikacje w chmurze 
- Azure DevOps: https://dev.azure.com — Planuj inteligentniej, współpracuj lepiej i wysyłaj szybciej dzięki zestawowi nowoczesnych usług deweloperskich 
- Studio usługi Azure https://ml.azure.com Machine Learning: — przygotowywanie danych, szkolenie i wdrażanie modeli uczenia maszynowego 
 

Azure DevOps to wbudowana usługa platformy Azure, która automatyzuje każdą część procesu DevOps z ciągłą integracją i ciągłym dostarczaniem dla dowolnego zasobu platformy Azure.
Niezależnie od tego, czy aplikacja korzysta z maszyn wirtualnych, aplikacji sieci web, aplikacji Kubernetes lub innych zasobów, można zaimplementować infrastrukturę jako kod, ciągłą integrację, ciągłe testowanie, ciągłe dostarczanie i ciągłe monitorowanie za pomocą platformy Azure i platformy Azure DevOps.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS - Konfigurowanie ciągłej integracji/ciągłego wdrażania 
Usługa Azure Pipelines udostępnia kompletny, w pełni funkcjonalny zestaw narzędzi automatyzacji ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych. Można skonfigurować potok ciągłego dostarczania dla maszyny Wirtualnej platformy Azure bezpośrednio z witryny Azure portal. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania dla wdrożeń wielu komputerów z witryny Azure Portal. Konfigurowanie ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych.

Maszyny wirtualne mogą być dodawane jako obiekty docelowe do [grupy wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) i mogą być kierowane do aktualizacji toczenia wielu komputerów. Widoki historii wdrażania w grupach wdrażania zapewniają identyfikowalność od maszyny Wirtualnej do potoku, a następnie do zatwierdzenia. 
 
**Rolling updates:** Wdrożenie stopniowe zastępuje wystąpienia poprzedniej wersji aplikacji wystąpieniami nowej wersji aplikacji na stałym zestawie maszyn (zestaw rolling) w każdej iteracji. Przejdźmy do przewodnika, jak można skonfigurować aktualizację stopniową do maszyn wirtualnych.  
Aktualizacje stopniowe można skonfigurować do **"maszyn wirtualnych"** w witrynie Azure portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku. 
1. Zaloguj się do witryny Azure portal i przejdź do maszyny wirtualnej. 
2. W lewym okienku maszyny Wirtualnej przejdź do menu **dostarczania** ciągłego. Następnie kliknij **Przycisk Konfiguruj**. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. W panelu konfiguracji kliknij "Azure DevOps Organization", aby wybrać istniejące konto lub utworzyć je. Następnie wybierz projekt, w ramach którego chcesz skonfigurować potok.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. Grupa wdrożeniowa to logiczny zestaw maszyn docelowych wdrażania, które reprezentują środowiska fizyczne; na przykład "Dev", "Test", "UAT" i "Produkcja". Można utworzyć nową grupę wdrożeniową lub wybrać istniejącą grupę wdrożeniową. Opcjonalnie można oznaczyć maszynę za pomocą roli. Na przykład "web", "db" itp.  
5. Kliknij **przycisk OK** w oknie dialogowym, aby skonfigurować potok dostarczania ciągłego. 
6. Po zakończeniu będziesz mieć potok dostarczania ciągłego skonfigurowany do wdrażania na maszynie wirtualnej.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. Zobaczysz, że wdrożenie na maszynie wirtualnej jest w toku. Możesz kliknąć łącze, aby przejść do potoku. Kliknij **w wersji 1,** aby wyświetlić wdrożenie. Możesz też kliknąć **przycisk Edytuj,** aby zmodyfikować definicję potoku wydania. 
8. Jeśli masz wiele maszyn wirtualnych do skonfigurowania, powtórz kroki 2-5, aby inne maszyny wirtualne zostały dodane do grupy wdrożeniowej. 
9. Po zakończeniu kliknij definicję potoku, przejdź do organizacji Azure DevOps i kliknij **edytuj** potok wersji. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. Kliknij na link **1 zadanie, 1 zadanie** na etapie **dev.** Kliknij fazę **wdrażania.**  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. W okienku konfiguracji po prawej stronie widać, że domyślnie potok jest skonfigurowany do wykonywania aktualizacji stopniowej do wszystkich obiektów docelowych równolegle. Można skonfigurować wdrożenia się zdarzyć jeden na raz lub pod względem procentowym za pomocą suwaka.  
  
  
**Canary** zmniejsza ryzyko, powoli wdrażając zmianę na małą podgrupę użytkowników. W miarę zdobywania większej pewności siebie w nowej wersji można rozpocząć zwalnianie jej na więcej serwerów w infrastrukturze i kierowanie do niej większej liczby użytkowników. Można skonfigurować wdrożenia kanary do **"maszyn wirtualnych"** za pomocą witryny Azure portal przy użyciu opcji ciągłego dostarczania. Oto przewodnik krok po kroku. 
1. Zaloguj się do witryny Azure portal i przejdź do maszyny wirtualnej 
2. Wykonaj kroki 2-5 w poprzedniej sekcji, aby dodać wiele maszyn wirtualnych do grupy wdrażania. 
3. Dodaj niestandardowy tag do maszyn wirtualnych, które mają być częścią wdrożeń kanarek. Na przykład "kanarek".
4. Po skonfigurowaniu potoku dla maszyn wirtualnych kliknij potok, uruchom organizację Programu Azure DevOps, **edytuj** potok i przejdź do etapu **deweloperskiego.** Dodaj znacznik do filtru "kanarek". 
5. Dodaj kolejną fazę grupy wdrażania, skonfiguruj fazę za pomocą tagów, aby kierować pozostałe maszyny wirtualne w grupie wdrożeniowej.  
6. Opcjonalnie należy skonfigurować krok ręcznej weryfikacji, który może promować/odrzucać wdrożenia kanaryjne. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**Blue-Green** zmniejsza przestoje wdrożenia dzięki identycznym środowisku czuwania. W każdej chwili jedno ze środowisk jest na żywo. Przygotowując się do nowej wersji, wykonujesz ostatni etap testowania w środowisku zielonym. Gdy oprogramowanie działa w środowisku zielonym, przełącz ruch tak, aby wszystkie przychodzące żądania przechodziły do środowiska zielonego - niebieskie środowisko jest teraz bezczynne.
Wdrożenia blue-green można skonfigurować na **"maszyny wirtualne"** z witryny Azure portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku. 

1. Zaloguj się do witryny Azure portal i przejdź do maszyny wirtualnej 
2. Wykonaj kroki 2-5 w sekcji **Aktualizacje stopniowe,** aby dodać wiele maszyn wirtualnych do grupy wdrażania. Dodaj niestandardowy tag do maszyn wirtualnych, które mają być częścią wdrożeń niebiesko-zielonych. Na przykład "niebieski" lub "zielony" dla maszyn wirtualnych, które są dla roli rezerwowej. 
3. Po skonfigurowaniu potoku dla maszyn wirtualnych kliknij potok, uruchom organizację Azure DevOps, **edytuj** potok, przejdź do etapu **dewelopera.** Dodaj znacznik do filtru "zielony". 
4. Dodaj fazę bez agenta, skonfiguruj fazę za pomocą kroku ręcznego sprawdzania poprawności i kroku interfejsu API invoke-REST, aby zamienić tagi. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Projekt Programu DevOps platformy Azure 
Korzystanie z platformy Azure łatwiejsze niż kiedykolwiek wcześniej.
 
Dzięki projektom DevOps rozpocznij uruchamianie aplikacji w dowolnej usłudze platformy Azure w zaledwie trzech krokach: wybierz język aplikacji, środowisko wykonawcze i usługę platformy Azure.
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Zasoby dodatkowe 
- [Wdrażanie na maszynach wirtualnych platformy Azure przy użyciu projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Wdrażanie ciągłego wdrażania aplikacji w zestawie skalowania maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
