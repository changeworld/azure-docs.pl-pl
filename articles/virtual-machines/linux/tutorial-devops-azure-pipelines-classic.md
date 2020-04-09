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
ms.openlocfilehash: 4159bf27c39087926d982552c49c606f3484de77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885909"
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
Usługa Azure Pipelines udostępnia kompletny, w pełni funkcjonalny zestaw narzędzi automatyzacji ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych. Można skonfigurować potok ciągłego dostarczania dla maszyny Wirtualnej platformy Azure bezpośrednio z witryny Azure portal. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania dla wdrożeń wielu komputerów z witryny Azure Portal. 


**Konfigurowanie ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych**

Maszyny wirtualne mogą być dodawane jako obiekty docelowe do [grupy wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups) i mogą być kierowane do aktualizacji wielu komputerów. W zależności od wymagań możesz wybrać jedną ze strategii wdrażania out-of-box, _Rolling_, _Canary,_ _Blue-Green_ lub można je dodatkowo dostosować. Po wdrożeniu widoki historii wdrażania w grupach wdrażania zapewnia identyfikowalność od maszyny Wirtualnej do potoku, a następnie do zatwierdzenia. 
 
**Wdrożenia stopniowe:** Wdrożenie stopniowe zastępuje wystąpienia poprzedniej wersji aplikacji wystąpieniami nowej wersji aplikacji na stałym zestawie maszyn (zestaw rolling) w każdej iteracji. Przejdźmy do przewodnika, jak można skonfigurować aktualizację stopniową do maszyn wirtualnych.  
Aktualizacje stopniowe można skonfigurować do **"maszyn wirtualnych"** w witrynie Azure portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku. 
1. Zaloguj się do witryny Azure portal i przejdź do maszyny wirtualnej. 
2. W lewym okienku maszyny Wirtualnej przejdź do menu **dostarczania** ciągłego. Następnie kliknij **Przycisk Konfiguruj**. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. W panelu konfiguracji kliknij "Azure DevOps Organization", aby wybrać istniejące konto lub utworzyć je. Następnie wybierz projekt, w ramach którego chcesz skonfigurować potok.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. Grupa wdrożeniowa to logiczny zestaw maszyn docelowych wdrażania, które reprezentują środowiska fizyczne; na przykład "Dev", "Test", "UAT" i "Produkcja". Można utworzyć nową grupę wdrożeniową lub wybrać istniejącą grupę wdrożeniową. 
5. Wybierz potok kompilacji, który publikuje pakiet do wdrożenia na maszynie wirtualnej. Należy zauważyć, że opublikowany pakiet powinien mieć _skrypt wdrożenia.ps1_ lub _deploy.sh_ w folderze _deployscripts_ w katalogu głównym pakietu. Ten skrypt wdrażania zostanie wykonany przez potok devops platformy Azure w czasie wykonywania.
6. Wybierz wybraną strategię wdrażania. W takim przypadku wybierz opcję "Rolling".
7. Opcjonalnie można oznaczyć maszynę za pomocą roli. Na przykład "web", "db" itp. Pomaga to kierować maszyny wirtualne, które mają tylko określoną rolę.
8. Kliknij **przycisk OK** w oknie dialogowym, aby skonfigurować potok dostarczania ciągłego. 
9. Po zakończeniu będziesz mieć potok dostarczania ciągłego skonfigurowany do wdrażania na maszynie wirtualnej.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. Zobaczysz, że wdrożenie na maszynie wirtualnej jest w toku. Możesz kliknąć łącze, aby przejść do potoku. Kliknij **w wersji 1,** aby wyświetlić wdrożenie. Możesz też kliknąć **przycisk Edytuj,** aby zmodyfikować definicję potoku wydania. 
11. Jeśli masz wiele maszyn wirtualnych do skonfigurowania, powtórz kroki 2-4 dla innych maszyn wirtualnych, które mają zostać dodane do grupy wdrażania. Należy zauważyć, że jeśli wybierzesz grupę wdrożeniową, dla której istnieje już uruchomienie potoku, maszyna wirtualna zostanie po prostu dodana do grupy wdrożeń bez tworzenia nowych potoków. 
12. Po zakończeniu kliknij definicję potoku, przejdź do organizacji Azure DevOps i kliknij **edytuj** potok wersji. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. Kliknij na link **1 zadanie, 1 zadanie** na etapie **dev.** Kliknij fazę **wdrażania.**
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. W okienku konfiguracji po prawej stronie można określić liczbę maszyn, które mają być wdrażane równolegle w każdej iteracji. W przypadku, gdy chcesz wdrożyć na wielu komputerach w czasie, można określić go pod względem procentowym za pomocą suwaka.  

15. Zadanie Wykonaj skrypt wdrażania domyślnie wykona skrypt _wdrożenia.ps1_ lub _deploy.sh_ w _folderze deployscripts_ w katalogu głównym opublikowanego pakietu.
  
**Canary Wdrożenia:** Canary wdrożenia zmniejsza ryzyko przez powolne wdrażanie zmiany do małej podgrupy użytkowników. W miarę zdobywania większej pewności siebie w nowej wersji można rozpocząć zwalnianie jej na więcej serwerów w infrastrukturze i kierowanie do niej większej liczby użytkowników. Można skonfigurować wdrożenia kanary do **"maszyn wirtualnych"** za pomocą witryny Azure portal przy użyciu opcji ciągłego dostarczania. Oto przewodnik krok po kroku. 
1. Zaloguj się do witryny Azure portal i przejdź do maszyny wirtualnej 
2. Wykonaj kroki 2-7 w sekcji **Wdrożenia stopniowe,** aby dodać wiele maszyn wirtualnych do grupy wdrożeniowej. W przypadku listy rozwijanej strategii wdrażania wybierz opcję "Kanarek".
![AzDevOps_configure_canary](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

3. Dodaj tag "kanarek" do maszyn wirtualnych, które mają być częścią wdrożeń kanarek i tag "prod" do maszyn wirtualnych, które są częścią wdrożeń po wdrożeniu kanaryjskim w pomyślnym.
4. Po zakończeniu będziesz mieć potok dostarczania ciągłego skonfigurowany do wdrażania na maszynie wirtualnej.
![AzDevOps_canary_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)


5. Podobnie jak w sekcji **Wdrożenia stopniowe,** można kliknąć na **Edytuj** potoku wydania w usłudze Azure DevOps, aby wyświetlić konfigurację potoku. Potok składa się z 3 faz - pierwsza faza jest fazą DG i wdraża na maszynach wirtualnych, które są oznaczone jako _kanarek._ Druga faza, wstrzymuje potok i czeka na ręczną interwencję, aby wznowić bieg. Gdy użytkownik jest zadowolony, że wdrożenie kanarek jest stabilny, może wznowić uruchomienie potoku, który następnie uruchomi trzecią fazę, która zostanie wdrożona do maszyn wirtualnych oznaczonych jako _prod_. ![AzDevOps_canary_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)



**Wdrożenia blue-green:** wdrożenie blue-green skraca czas przestojów dzięki identycznemu środowisku rezerwowego. W każdej chwili jedno ze środowisk jest na żywo. Przygotowując się do nowej wersji, wykonujesz ostatni etap testowania w środowisku zielonym. Gdy oprogramowanie działa w środowisku zielonym, przełącz ruch tak, aby wszystkie przychodzące żądania przechodziły do środowiska zielonego - niebieskie środowisko jest teraz bezczynne.
Wdrożenia blue-green można skonfigurować na **"maszyny wirtualne"** z witryny Azure portal przy użyciu opcji ciągłego dostarczania. 

Oto przewodnik krok po kroku.

1. Zaloguj się do witryny Azure portal i przejdź do maszyny wirtualnej 
2. Wykonaj kroki 2-7 w sekcji **Wdrożenia stopniowe,** aby dodać wiele maszyn wirtualnych do grupy wdrożeniowej. Dodaj tag "niebieski" lub "zielony" do maszyn wirtualnych, które mają być częścią wdrożeń Blue-Green. Jeśli maszyna wirtualna jest dla roli wstrzymania, należy oznaczyć go jako "zielony".
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

4. Po zakończeniu będziesz mieć potok dostarczania ciągłego skonfigurowany do wdrażania na maszynie wirtualnej.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

5. Podobnie jak w odniesieniu **do wdrożeń stopniowych,** można kliknąć na **Edytuj** potoku wydania w usłudze Azure DevOps, aby wyświetlić konfigurację potoku. Potok składa się z 3 faz — pierwsza faza jest fazą DG i jest wdrażana na maszynach wirtualnych oznaczonych jako _zielone_ (maszyny wirtualne w trybie gotowości). Druga faza wstrzymuje potok i czeka na ręczną interwencję, aby wznowić bieg. Gdy użytkownik jest zadowolony, że wdrożenie jest stabilne, może teraz przekierować ruch do _zielonych_ maszyn wirtualnych i wznowić uruchamianie potoku, który następnie zamieni _niebieskie_ i _zielone_ znaczniki na maszynach wirtualnych. To upewnia się, że maszyny wirtualne, które mają starszą wersję aplikacji są oznaczone jako _zielone_ i są wdrażane w następnym uruchomieniu potoku.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

6. Należy zauważyć, że ta strategia wdrażania wymaga, że musi istnieć co najmniej jedna maszyna wirtualna oznaczona jako niebieska i zielona. Upewnij się, że przed wznowieniem uruchamiania potoku na etapie interwencji ręcznej masz co najmniej jedną maszynę wirtualną oznaczoną jako _niebieska_.





 
## <a name="azure-devops-project"></a>Projekt Programu DevOps platformy Azure 
Korzystanie z platformy Azure łatwiejsze niż kiedykolwiek wcześniej.
 
Dzięki projektom DevOps rozpocznij uruchamianie aplikacji w dowolnej usłudze platformy Azure w zaledwie trzech krokach: wybierz język aplikacji, środowisko wykonawcze i usługę platformy Azure.
 
[Dowiedz się więcej](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>Zasoby dodatkowe 
- [Wdrażanie na maszynach wirtualnych platformy Azure przy użyciu projektu DevOps](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Wdrażanie ciągłego wdrażania aplikacji w zestawie skalowania maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
