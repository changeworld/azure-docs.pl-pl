---
title: Tworzenie środowiska klastra sieci szkieletowej usług w laboratoriach devtest azure
description: Dowiedz się, jak utworzyć środowisko z samodzielnym klastrem sieci szkieletowej usług oraz uruchomić i zatrzymać klaster przy użyciu harmonogramów.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170337"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Tworzenie środowiska z samodzielnym klastrem sieci szkieletowej usług w laboratoriach devtest azure
Ten artykuł zawiera informacje dotyczące tworzenia środowiska z samodzielnym klastrem sieci szkieletowej usług w laboratoriach DevTest azure. 

## <a name="overview"></a>Omówienie
Laboratoria DevTest mogą tworzyć samodzielne środowiska testowe zdefiniowane przez szablony usługi Azure Resource Management. Te środowiska zawierają zarówno zasoby IaaS, takie jak maszyny wirtualne, jak i zasoby PaaS, takie jak sieć szkieletowa usług. DevTest Labs umożliwia zarządzanie maszynami wirtualnymi w środowisku, zapewniając polecenia do sterowania maszynami wirtualnymi. Te polecenia umożliwiają uruchamianie lub zatrzymywania maszyny wirtualnej zgodnie z harmonogramem. Podobnie DevTest Labs może również pomóc w zarządzaniu klastrami sieci szkieletowej usług w środowisku. Klaster sieci szkieletowej usług można uruchomić lub zatrzymać w środowisku ręcznie lub za pomocą harmonogramu.

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric
Klastry sieci szkieletowej usług są tworzone przy użyciu środowisk w laboratoriach DevTest. Każde środowisko jest definiowane przez szablon usługi Azure Resource Manager w repozytorium Git. [Publiczne repozytorium Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) dla laboratoriów DevTest labs zawiera szablon Menedżera zasobów w celu utworzenia klastra sieci szkieletowej usług w folderze [ServiceFabric-Cluster.](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 

1. Najpierw utwórz laboratorium w laboratorium usługi Azure DevTest Labs przy użyciu instrukcji w [następującym](devtest-lab-create-lab.md)artykule: Tworzenie laboratorium . Należy zauważyć, że opcja **Środowiska publiczne** jest domyślnie **włączone.** 
2. Upewnij się, że dostawca sieci szkieletowej usług jest zarejestrowany dla subskrypcji, wykonując następujące kroki:
    1. Wybierz **subskrypcje** w lewym menu nawigacyjnym i wybierz **subskrypcję**
    2. Na stronie **Subskrypcja** wybierz pozycję **Dostawcy zasobów** w sekcji **Ustawienia** w menu po lewej stronie. 
    3. Jeśli **microsoft.ServiecFabric** nie jest zarejestrowany, **wybierz**zarejestruj . 
3. Na stronie **Laboratorium DevTest** dla laboratorium wybierz pozycję **+ Dodaj** na pasku narzędzi. 
    
    ![Przycisk Dodaj na pasku narzędzi](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na stronie **Wybieranie strony podstawowej** wybierz pozycję **Klaster laboratorium sieci szkieletowej** usług na liście. 

    ![Wybierz klaster laboratorium sieci szkieletowej usług na liście](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na stronie **Konfigurowanie ustawień** wykonaj następujące czynności: 
    1. Określ **nazwę** **środowiska klastra**. Jest to nazwa grupy zasobów na platformie Azure, w której ma zostać utworzony klaster sieci szkieletowej usług. 
    2. Wybierz **system operacyjny dla** maszyn wirtualnych klastra. Wartość domyślna to: **Windows**.
    3. Określ nazwę **administratora** klastra. 
    4. Określ **hasło** administratora. 
    5. W przypadku **certyfikatu**wprowadź informacje o certyfikacie jako ciąg zakodowany w bazie Base64. Aby utworzyć certyfikat, wykonaj następujące czynności:
        1. Pobierz plik **Create-ClusterCertificate.ps1** z [repozytorium Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Alternatywnie należy sklonować repozytorium na komputerze. 
        2. Uruchom program **PowerShell**. 
        3. Uruchom plik **ps1** za `.\Create-ClusterCertificate.ps1`pomocą polecenia . W notatniku zostanie wyświetlony plik tekstowy z informacjami potrzebnymi do wypełnienia pól związanych z certyfikatami na tej stronie. . 
    6. Wprowadź **hasło certyfikatu**.
    7. Określ **odcisk palca** certyfikatu.
    8. Wybierz **pozycję Dodaj** na stronie **Konfigurowanie ustawień.** 

        ![Konfigurowanie ustawień klastra](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Po utworzeniu klastra zostanie wyświetlana grupa zasobów o nazwie środowiska podanego w poprzednim kroku. Po rozwinięciu zobaczysz klaster sieci szkieletowej usług w nim. Jeśli stan grupy zasobów jest zablokowany w **temacie Tworzenie**, wybierz **pozycję Odśwież** na pasku narzędzi. Środowisko **klastra sieci szkieletowej usług** tworzy klaster 1-węzłowy 5 węzłów w systemie Linux lub Windows.

    W poniższym przykładzie **mysfabricclusterrg** jest nazwą grupy zasobów, która jest tworzona specjalnie dla klastra sieci szkieletowej usług. Należy pamiętać, że środowiska laboratoryjne są samodzielne w grupie zasobów, w której są tworzone. Oznacza to, że szablon definiujący środowisko, które może uzyskiwać dostęp tylko do zasobów w nowo utworzonej grupie zasobów lub [sieciach wirtualnych skonfigurowanych do użycia przez laboratorium.](devtest-lab-configure-vnet.md) Ten przykład powyżej tworzy wszystkie wymagane zasoby w tej samej grupie zasobów.

    ![Utworzony klaster](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Uruchamianie lub zatrzymywania klastra
Klaster można uruchomić lub zatrzymać na samej stronie Laboratorium DevTest lub na stronie Klastra sieci szkieletowej usług udostępnianej przez devtest labs. 

### <a name="from-the-devtest-lab-page"></a>Na stronie Laboratorium DevTest
Można uruchomić lub zatrzymać klaster na stronie Laboratorium DevTest dla laboratorium. 

1. Wybierz **trzy kropki (...)** dla klastra sieci szkieletowej usług, jak pokazano na poniższej ilustracji: 

    ![Polecenia uruchamiania i zatrzymywania klastra](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. W menu kontekstowym są widoczne dwa polecenia, aby **uruchomić** i **zatrzymać** klaster. Polecenie start uruchamia wszystkie węzły w klastrze. Polecenie stop zatrzymuje wszystkie węzły w klastrze. Po zatrzymaniu klastra sam klaster sieci szkieletowej usług pozostaje w stanie gotowości, ale żadne węzły nie są dostępne, dopóki polecenie start nie zostanie ponownie wyeksuowane w klastrze w laboratorium.

    Istnieje kilka zagadnień, które należy zwrócić uwagę podczas korzystania z klastra sieci szkieletowej usług w środowisku testowym. Może upłynąć trochę czasu, aby klaster sieci szkieletowej usług w pełni nawodnienia po ponownym uruchomieniu węzłów. Aby zachować dane od zamknięcia do uruchomienia, dane muszą być zapisywane na dysku zarządzanym dołączonym do maszyny wirtualnej. Istnieją wpływy na wydajność podczas korzystania z dołączonego dysku zarządzanego, więc jest zalecane tylko dla środowisk testowych. Jeśli dysk używany do przechowywania danych nie jest wspierany, dane są tracone po wydaniu polecenia stop w klastrze.

### <a name="from-the-service-fabric-cluster-page"></a>Na stronie Klaster sieci szkieletowej usług 
Istnieje inny sposób uruchamiania lub zatrzymywania klastra. 

1. Wybierz klaster sieci szkieletowej usług w widoku drzewa na stronie Laboratorium DevTest. 

    ![Wybieranie klastra](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na stronie **Klaster sieci szkieletowej usług** klastra na pasku narzędzi są widoczne polecenia uruchamiania lub zatrzymywania klastra. 

    ![Uruchamianie lub zatrzymywania poleceń na stronie Klaster sieci szkieletowej usług](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurowanie harmonogramu automatycznego uruchamiania i automatycznego zamykania
Klastry sieci szkieletowej usług można również uruchomić lub zatrzymać zgodnie z harmonogramem. To środowisko jest podobne do środowiska dla maszyn wirtualnych w laboratorium. Aby zaoszczędzić pieniądze, domyślnie każdy klaster utworzony w laboratorium jest automatycznie zamykany w czasie zdefiniowanym przez [zasady zamykania](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)laboratorium . Można zastąpić, określając, czy klaster powinien zostać zamknięty, czy określając czas zamykania klastra. 

![Istniejące harmonogramy automatycznego uruchamiania i automatycznego zamykania](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Zapisz się do harmonogramu automatycznego uruchamiania
Aby zapisać się do harmonogramu uruchamiania, wykonaj następujące czynności:

1. Wybierz **autostart** w menu po lewej stronie
2. Wybierz **pozycję Włącz,** aby **zezwolić na zaplanowanie automatycznego uruchamiania klastra sieci szkieletowej tej sieci**szkieletowej. Ta strona jest włączona tylko wtedy, gdy właściciel laboratorium zezwolił użytkownikom na automatyczne uruchamianie ich maszyn wirtualnych lub klastrów sieci szkieletowej usług.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Automatyczna strona gwiazdkowa](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurowanie ustawień automatycznego zamykania 
Aby zmienić ustawienia zamknięcia systemu, wykonaj następujące czynności:

1. Wybierz **opcję Automatyczne zamykanie** w menu po lewej stronie. 
2. Na tej stronie możesz zrezygnować z automatycznego zamykania, wybierając **opcję Wyłącz** dla **włączone .** 
3. Jeśli wybrano **opcję Włącz** dla **włączone,** wykonaj następujące czynności:
    1. Określ **godzinę** zamknięcia systemu.
    2. Określ **strefę czasową** dla czasu. 
    3. Określ, czy devtest labs ma wysyłać **powiadomienia** przed automatycznym zamknięciem. 
    4. Jeśli dla opcji powiadomienia wybrano **opcję Tak,** określ adres URL i/lub **adres e-mail** elementu **Webhook,** aby wysyłać powiadomienia. 
    5. Wybierz pozycję **Zapisz** na pasku narzędzi.

        ![Automatyczne zamykanie strony](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Aby wyświetlić węzły w klastrze sieci szkieletowej usług
Strona klastra sieci szkieletowej usług, które zostały wyświetlone w krokach wcześniej jest specyficzna dla DevTest Labs strony. Nie pokazuje węzłów w klastrze. Aby wyświetlić więcej informacji o klastrze, wykonaj następujące kroki:

1. Na stronie **Laboratorium testów deweloperskich** dla laboratorium wybierz **grupę zasobów** w widoku drzewa w sekcji Moje maszyny **wirtualne.**

    ![Wybieranie grupy zasobów](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na stronie **Grupa zasobów** na liście są widoczne wszystkie zasoby w grupie zasobów. Wybierz **klaster sieci szkieletowej usług** z listy. 

    ![Wybieranie klastra na liście](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Zostanie wyświetlona strona **Klaster sieci szkieletowej** usług dla klastra. Jest to strona, która zapewnia sieci szkieletowej usług. Zobaczysz wszystkie informacje o klastrach, takie jak węzły, typy węzłów itp.

    ![Strona główna klastra sieci szkieletowej usług](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Następne kroki
Szczegółowe informacje na temat środowisk można znaleźć w następujących artykułach: 

- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowisk publicznych w laboratoriach devtest azure](devtest-lab-configure-use-public-environments.md)
