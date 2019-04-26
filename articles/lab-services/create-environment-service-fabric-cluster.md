---
title: Utwórz środowisko z klastrem usługi Service Fabric w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowisko z klastrem usługi Service Fabric niezależna i uruchamianie i zatrzymywanie klastra za pomocą harmonogramów.
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
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312187"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Utwórz środowisko przy użyciu niezależna klastra usługi Service Fabric w usłudze Azure DevTest Labs
Ten artykuł zawiera informacje na temat sposobu tworzenia środowiska z klastrem usługi Service Fabric niezależna w usłudze Azure DevTest Labs. 

## <a name="overview"></a>Omówienie
DevTest Labs można tworzyć środowiska testowe niezależna, zgodnie z definicją w szablonach usługi Azure Resource Management. Te środowiska zawierają zarówno zasobów IaaS, takich jak maszyny wirtualne i zasobów PaaS, takich jak usługi Service Fabric. DevTest Labs umożliwia zarządzanie maszynami wirtualnymi w środowisku, zapewniając polecenia sterujące maszyn wirtualnych. Polecenia te zapewniają możliwość uruchamianie lub zatrzymywanie maszyny wirtualnej zgodnie z harmonogramem. Podobnie DevTest Labs może również ułatwić zarządzanie klastrami usługi Service Fabric w środowisku. Możesz rozpocząć lub zatrzymać klaster usługi Service Fabric w środowisku, ręcznie lub za pomocą harmonogramu.

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric
Klastry usługi Service Fabric są tworzone przy użyciu środowisk w usłudze DevTest Labs. Każde środowisko jest definiowany przez szablonu usługi Azure Resource Manager w taki sposób, w repozytorium Git. [Publicznego repozytorium Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) DevTest Labs zawiera szablon usługi Resource Manager, aby utworzyć klaster usługi Service Fabric w [klaster ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) folderu. 

1. Najpierw utwórz laboratorium Azure DevTest Labs, zgodnie z instrukcjami podanymi w następującym artykule: [Tworzenie laboratorium](devtest-lab-create-lab.md). Należy zauważyć, że **środowiskach publicznych** jest opcja **na** domyślnie. 
2. Upewnij się, że dostawcy usługi Service Fabric jest zarejestrowany dla Twojej subskrypcji, wykonaj następujące czynności:
    1. Wybierz **subskrypcje** na pasku menu nawigacji po lewej stronie i wybierz swoje **subskrypcji**
    2. Na **subskrypcji** wybierz opcję **dostawców zasobów** w **ustawienia** sekcji, w menu po lewej stronie. 
    3. Jeśli **Microsoft.ServiecFabric** nie jest zarejestrowane, wybierz opcję **zarejestrować**. 
3. Na **laboratorium DevTest Lab** strony dla laboratorium, wybierz opcję **+ Dodaj** na pasku narzędzi. 
    
    ![Dodaj przycisk na pasku narzędzi](./media/create-environment-service-fabric-cluster/add-button.png)
3. Na **wybierz podstawowej** wybierz opcję **klastra laboratorium usługi Service Fabric** na liście. 

    ![Wybierz klaster laboratorium usługi Service Fabric na liście](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Na **konfigurowania ustawień** wykonaj następujące czynności: 
    1. Określ **nazwa** klastra **środowiska**. Jest to nazwa grupy zasobów na platformie Azure, w którym ma zostać utworzony klaster usługi Service Fabric. 
    2. Wybierz **systemu operacyjnego (OS)** dla maszyn wirtualnych klastra. Wartość domyślna to: **Windows**.
    3. Określ nazwę dla **administratora** klastra. 
    4. Określ **hasło** dla administratora. 
    5. Aby uzyskać **certyfikatu**, wprowadź informacje dotyczące certyfikatu jako ciąg zakodowany w formacie Base64. Aby utworzyć certyfikat, wykonaj następujące czynności:
        1. Pobierz **ClusterCertificate.ps1 Utwórz** plik wchodzącej w skład [repozytorium Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Alternatywnie można sklonować repozytorium na komputerze. 
        2. Uruchom program **PowerShell**. 
        3. Uruchom **ps1** plików za pomocą polecenia `.\Create-ClusterCertificate.ps1`. Zostanie wyświetlony plik tekstowy, otwarty w Notatniku informacjami, które należy wypełnić pola związane z certyfikatem na tej stronie. . 
    6. Wprowadź **hasło dla certyfikatu**.
    7. Określ **odcisk palca** dla certyfikatu.
    8. Wybierz **Dodaj** na **Konfigurowanie ustawień** strony. 

        ![Konfiguruj ustawienia klastra](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Po utworzeniu klastra, zobaczysz grupę zasobów o nazwie środowiska, które podano w poprzednim kroku. Po rozwinięciu, zobaczysz klastra usługi Service Fabric w nim. Jeśli stan grupy zasobów jest zablokowana w **tworzenie**, wybierz opcję **Odśwież** na pasku narzędzi. **Klastra usługi Service Fabric** środowiska tworzy klaster z 5 węzłami 1-nodetype w systemie Linux lub Windows.

    W poniższym przykładzie **mysfabricclusterrg** to nazwa grupy zasobów, które są tworzone specjalnie dla klastra usługi Service Fabric. Należy zauważyć, że środowisk laboratoryjnych są niezależne w obrębie grupy zasobów, w którym są tworzone. Oznacza to, że szablon definiujący środowisko, w którym dostęp tylko do zasobów w ramach nowo utworzonej grupy zasobów lub [sieci wirtualne są skonfigurowane do użycia przez laboratorium](devtest-lab-configure-vnet.md). W przykładzie powyżej tworzy wszystkich wymaganych zasobów w tej samej grupie zasobów.

    ![Klaster utworzony](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Uruchamianie lub zatrzymywanie klastra
Możesz rozpocząć lub zatrzymać klastra z obu laboratorium DevTest Lab samej strony lub ze strony klastra usługi Service Fabric, udostępniane przez usługi DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na stronie laboratorium DevTest Lab
Możesz rozpocząć lub zatrzymać klastra na stronie laboratorium DevTest Lab dla swojego laboratorium. 

1. Wybierz **wielokropek (...)**  dla klastra usługi Service Fabric, jak pokazano na poniższej ilustracji: 

    ![Uruchamianie i zatrzymywanie poleceń dla klastra](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Zobaczysz dwa polecenia w menu kontekstowym, aby **start** i **zatrzymać** klastra. Polecenie start Uruchamia wszystkie węzły w klastrze. Polecenie stop Zatrzymuje wszystkie węzły w klastrze. Po zatrzymaniu klaster sam klaster usługi Service Fabric pozostaje w stanie gotowe, ale nie węzły są dostępne, dopóki ponownie polecenia uruchamiania w klastrze w środowisku laboratoryjnym.

    Jest kilka istotnych kwestii, należy pamiętać podczas korzystania z klastra usługi Service Fabric w środowisku testowym. Jego może zająć trochę czasu, w pełni przywrócenia z magazynu trwałego po ponownym uruchomieniu węzły klastra usługi Service Fabric. Aby zachować dane z zamknięcia do uruchamiania, należy zapisać danych na dysk zarządzany, dołączonych do maszyny wirtualnej. Ma to wpływ na wydajność, używania dołączonego dysku zarządzanego, więc jest zalecane tylko dla środowisk testowych. Jeśli dysk używany do przechowywania danych nie jest obsługiwane, następnie dane zostaną utracone po wydaniu polecenia zatrzymania w klastrze.

### <a name="from-the-service-fabric-cluster-page"></a>Ze strony klastra usługi Service Fabric 
Istnieje inny sposób, aby rozpocząć lub zatrzymać klaster. 

1. Wybierz klaster usługi Service Fabric w widoku drzewa na stronie laboratorium DevTest Lab. 

    ![Wybierz klaster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Na **klastra usługi Service Fabric** strony dla klastra, zobacz polecenia na pasku narzędzi, aby rozpocząć lub zatrzymać klaster. 

    ![Uruchamianie lub zatrzymywanie poleceń na stronie klastra usługi Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurowanie automatycznego uruchamiania i automatycznego zamykania harmonogramu
Klastry usługi Service Fabric może również być uruchomiona lub zatrzymana zgodnie z harmonogramem. To środowisko jest podobne do środowiska dla maszyn wirtualnych w laboratorium. Aby zaoszczędzić pieniądze, domyślnie, każdy klaster automatycznie utworzone w laboratorium kończy pracę w czasie, zdefiniowane przez laboratorium [zasad zamykania](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). Można zastąpić, określając, czy klaster powinien zostać wyłączony lub określając razem klastra zostanie zamknięta. 

![Istniejące harmonogramy automatycznego uruchamiania i automatyczne zamknięcie](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Zezwalaj na korzystanie z automatycznego harmonogramu uruchamiania
Aby zapoznać się z harmonogramem uruchamiania, wykonaj następujące czynności:

1. Wybierz **Auto-start** w menu po lewej stronie
2. Wybierz **na** dla **zezwolić na ten klaster usługi Service fabric być zaplanowane do automatycznego uruchamiania**. Ta strona jest włączona tylko wtedy, gdy właściciel laboratorium ma Użytkownicy mogący automatyczne uruchamianie ich maszyn wirtualnych ani klastrów usługi Service Fabric.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Strona gwiazdki automatycznie](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurowanie automatycznego zamykania ustawień 
Aby zmienić ustawienia zamknięcia, wykonaj następujące czynności:

1. Wybierz **automatycznego zamykania** w menu po lewej stronie. 
2. Na tej stronie można zrezygnować z automatycznego zamykania, wybierając **poza** dla **włączone**. 
3. Jeśli wybrano **na** dla **włączone**, wykonaj następujące kroki:
    1. Określ **czasu** zamknięcia systemu.
    2. Określ **strefa czasowa** raz. 
    3. Określ, czy usługa DevTest Labs, aby wysłać **powiadomienia** przed automatycznego zamykania. 
    4. W przypadku wybrania **tak** opcji powiadomienia, określ **adresu URL elementu Webhook** i/lub **adres e-mail** do wysyłania powiadomień. 
    5. Wybierz pozycję **Zapisz** na pasku narzędzi.

        ![Automatycznie zamknij stronę w dół](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Aby wyświetlić węzły w klastrze usługi Service Fabric
Na stronie usługi DevTest Labs dotyczy strona klastra usługi Service Fabric, którą wcześniej, jak już wspomniano w krokach. Go nie wyświetlone węzły w klastrze. Aby uzyskać więcej informacji o klastrze, wykonaj następujące kroki:

1. Na **laboratorium DevTest Lab** strony dla laboratorium, wybierz opcję **grupy zasobów** w widoku drzewa w **Moje maszyny wirtualne** sekcji.

    ![Wybieranie grupy zasobów](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Na **grupy zasobów** stronie zobaczysz wszystkie zasoby w grupie zasobów, na liście. Wybierz swoje **klastra usługi Service Fabric** z listy. 

    ![Wybierz klaster, na liście](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Zostanie wyświetlony **klastra usługi Service Fabric** strony dla klastra. Jest to strona która zapewnia usługi Service Fabric. Zobaczysz wszystkie informacje o klastrach, takie jak węzły, typy węzłów itp.

    ![Strona główna klaster usługi Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły, aby uzyskać szczegółowe informacje o środowiskach: 

- [Tworzenie środowisk z wieloma maszynami wirtualnymi i zasobów PaaS za pomocą szablonów usługi Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Konfigurowanie i używanie środowiskach publicznych w usłudze Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
