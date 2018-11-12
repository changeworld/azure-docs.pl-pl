---
title: Konfigurowanie hybrydowego klastra HPC Pack na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować mały hybrydowego obliczeń (HPC) klastra o wysokiej wydajności. za pomocą pakietu Microsoft HPC Pack i na platformie Azure
services: cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: hpc-pack
ms.assetid: ''
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: 55dfd7e5ea93ae941d73612cc70ed82d48db725a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51236742"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Konfigurowanie hybrydowego klastra obliczeń o wysokiej wydajności za pomocą pakietu Microsoft HPC Pack i węzłów obliczeniowych platformy Azure na żądanie
Skonfigurować małe, hybrydowych obliczeń o wysokiej wydajności (HPC) klastra za pomocą pakietu Microsoft HPC Pack 2012 R2 i na platformie Azure. Klaster przedstawione w tym artykule zawiera węzeł główny HPC Pack do środowiska lokalnego i niektóre obliczeniowe węzły, które można wdrożyć na żądanie na platformie Azure usługa w chmurze. Następnie można uruchomić zadania obliczeniowe w klastrze hybrydowym.

![Hybrydowego klastra HPC][Overview] 

W tym samouczku pokazano jedno z podejść, czasami nazywane "dużego ruchu do chmury," klaster na potrzeby uruchamiania aplikacji intensywnie korzystających z obliczeń skalowalne i na żądanie zasobów platformy Azure.

W tym samouczku przyjęto założenie, nie musisz mieć doświadczenia, klastry obliczeniowe lub pakietu HPC Pack. Jej celem jest tylko pomocne we wdrażaniu hybrydowy klaster obliczeniowy szybko w celach demonstracyjnych. Zagadnień i sposobów wdrażania hybrydowego klastra HPC Pack na większą skalę w środowisku produkcyjnym lub użyć zestawu HPC Pack 2016, zobacz [szczegółowe wskazówki](https://go.microsoft.com/fwlink/p/?LinkID=200493). W innych sytuacjach za pomocą pakietu HPC Pack w tym automatyczne wdrożenie klastra na maszynach wirtualnych platformy Azure, zobacz [opcji klastra HPC za pomocą pakietu Microsoft HPC Pack na platformie Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut.
* **Na komputerze lokalnym systemem Windows Server 2012 R2 lub Windows Server 2012** — korzystać z tego komputera jako węzłem głównym klastra HPC. Jeśli nie są jeszcze uruchomiony system Windows Server, można go pobrać i zainstalować [wersję ewaluacyjną](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Komputer musi należeć do domeny usługi Active Directory. Do celów testowych można skonfigurować komputer węzłem jako kontroler domeny. Aby dodać rolę serwera usług domenowych Active Directory i przyczyniają się do komputera z węzłem głównym jako kontroler domeny, zobacz dokumentację dla systemu Windows Server.
  * Aby zapewnić obsługę pakietu HPC Pack, należy zainstalowany system operacyjny w jednym z tych języków: angielski, japoński lub chiński (uproszczony).
  * Sprawdź, czy są zainstalowane ważne aktualizacje krytyczne.
* **HPC Pack 2012 R2** - [Pobierz](https://go.microsoft.com/fwlink/p/?linkid=328024) instalacji pakietu do najnowszej wersji bezpłatna i skopiuj pliki do komputera węzła głównego. Wybierz pliki instalacyjne, w tym samym języku co instalacji systemu Windows Server.

    >[!NOTE]
    > Dodatkowa konfiguracja jest potrzebna, jeśli chcesz użyć zestawu HPC Pack 2016 zamiast HPC Pack 2012 R2. Zobacz [szczegółowe wskazówki](https://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Konto domeny** — to konto musi mieć prawa administratora lokalnego na węzeł główny do zainstalowania pakietu HPC Pack.
* **Łączność TCP na porcie 443** z węzła głównego do platformy Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Instalowanie pakietu HPC Pack na węzeł główny
Możesz najpierw zainstalować pakiet Microsoft HPC Pack na komputerze w środowisku lokalnym z systemem Windows Server. Ten komputer staje się z węzłem głównym klastra.

1. Zaloguj się do węzła głównego przy użyciu konta domeny mającego uprawnienia administratora lokalnego.

2. Uruchom Kreatora instalacji pakietu HPC Pack, uruchamiając polecenie Setup.exe przy użyciu plików instalacyjnych pakietu HPC Pack.

3. Na **Instalator HPC Pack 2012 R2** ekranu, kliknij przycisk **nowa instalacja lub Dodaj nowe funkcje do istniejącej instalacji**.

    ![HPC Pack 2012 Instalatora][install_hpc1]

4. Na **strony umowę z użytkownikiem oprogramowania Microsoft**, kliknij przycisk **dalej**.

5. Na **Wybieranie typu instalacji** kliknij **utworzenie nowego klastra HPC, tworząc węzła głównego**, a następnie kliknij przycisk **dalej**.

6. Kreator uruchamia się wiele testów przed instalacją. Kliknij przycisk **dalej** na **reguły instalacji** strony, jeśli wszystkie testy zostały zakończone pomyślnie. W przeciwnym razie zapoznaj się z informacjami, pod warunkiem i wprowadź niezbędne zmiany w danym środowisku. Następnie uruchom ponownie testy, lub w razie potrzeby uruchom Kreatora instalacji ponownie.
7. Na **konfiguracji bazy danych HPC** strony, upewnij się, że **węzeł główny** jest zaznaczone dla wszystkich baz danych HPC, a następnie kliknij przycisk **dalej**. 

    ![Konfiguracja bazy danych][install_hpc4]

8. Zaakceptuj domyślne wartości na pozostałych stronach kreatora. Na **zainstalować wymagane składniki** kliknij **zainstalować**.
   
    ![Instalowanie][install_hpc6]

9. Po zakończeniu instalacji, usuń zaznaczenie pola wyboru **Uruchom Menedżera klastra HPC** a następnie kliknij przycisk **Zakończ**. (Możesz rozpocząć Menedżer klastra HPC w późniejszym kroku.)
   
    ![Zakończ][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Przygotowanie subskrypcji platformy Azure
Wykonaj poniższe kroki w [witryny Azure portal](https://portal.azure.com) z subskrypcją platformy Azure. Po wykonaniu tych czynności można wdrożyć węzły na platformie Azure z węzłem głównym w środowisku lokalnym. 
  
  > [!NOTE]
  > Ponadto Zanotuj identyfikator subskrypcji platformy Azure, w którym będą potrzebne później. Identyfikator znajduje się w **subskrypcje** w portalu.
  > 

### <a name="upload-the-default-management-certificate"></a>Przekaż domyślny certyfikat zarządzania
Pakiet HPC Pack instaluje certyfikat z podpisem własnym w węźle głównym o nazwie certyfikat zarządzania Azure domyślne HPC firmy Microsoft, można przekazać jako certyfikat zarządzania platformy Azure. Ten certyfikat znajduje się do testowania i weryfikacji koncepcji wdrożenia do zabezpieczenia połączenia między węzłem głównym i platformą Azure.

1. Na komputerze węzła głównego, zaloguj się do [witryny Azure portal](https://portal.azure.com).

2. Kliknij przycisk **subskrypcje** > *your_subscription_name*.

3. Kliknij przycisk **certyfikaty zarządzania** > **przekazywanie**.

4. Przeglądaj w węźle głównym dla pliku 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack. Następnie kliknij przycisk **przekazywanie**.

   
**Domyślne HPC Azure Management** certyfikat zostanie wyświetlony na liście certyfikatów zarządzania.

### <a name="create-an-azure-cloud-service"></a>Tworzenie usługi w chmurze platformy Azure
> [!NOTE]
> Aby uzyskać najlepszą wydajność należy utworzyć usługę w chmurze i konto magazynu (w dalszej części) w tym samym regionie geograficznym.
> 
> 

1. W portalu, kliknij przycisk **Cloud services (wersja klasyczna)** > **+ Dodaj**.

2.  Wpisz nazwę DNS dla usługi, wybierz grupę zasobów i lokalizację, a następnie kliknij przycisk **Utwórz**.


### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
1. W portalu, kliknij przycisk **konta magazynu (klasyczne)** > **+ Dodaj**.

2. Wpisz nazwę konta, a następnie wybierz pozycję **klasycznego** modelu wdrażania.

3. Wybierz grupę zasobów i lokalizacji i pozostaw ustawienia domyślne wartości. Następnie kliknij pozycję **Utwórz**.

## <a name="configure-the-head-node"></a>Konfigurowanie węzła głównego
Aby wdrożyć węzły na platformie Azure i przesyłania zadań, należy użyć Menedżera klastra HPC, należy najpierw wykonać pewne czynności konfiguracyjne wymagane klastra.

1. W węźle głównym Uruchom Menedżera klastra HPC. Jeśli **wybierz węzeł główny** pojawi się okno dialogowe, kliknij przycisk **komputera lokalnego**. **Listy zadań do wykonania wdrożenia** pojawia się.

2. W obszarze **wymagane zadania związane z wdrażaniem**, kliknij przycisk **skonfigurować sieć**.
   
    ![Konfigurowanie sieci][config_hpc2]

3. W Kreatorze konfiguracji sieci wybierz **wszystkie węzły tylko w sieci przedsiębiorstwa** (topologia 5). Ta konfiguracja sieci jest najprostszym dla celów demonstracyjnych.
   
    ![Topologia 5][config_hpc3]
   
4. Kliknij przycisk **dalej** aby zaakceptować domyślne wartości na pozostałych stronach kreatora. Następnie na **przeglądu** kliknij pozycję **Konfiguruj** w celu ukończenia konfiguracji sieci.

5. W **listy zadań do wykonania wdrożenia**, kliknij przycisk **poświadczenia instalacji**.

6. W **poświadczenia instalacji** okna dialogowego wpisz poświadczenia konta domeny, które umożliwia zainstalowanie pakietu HPC Pack. Następnie kliknij przycisk **OK**. 
   
    ![Poświadczenia instalacji][config_hpc6]
   
7. W **listy zadań do wykonania wdrożenia**, kliknij przycisk **konfigurowanie nazw nowych węzłów**.

8. W **Określ serii nazw węzła** okna dialogowego pole, zaakceptuj wartość domyślną nazewnictwa serii i kliknij przycisk **OK**. Mimo że Azure węzły, które możesz dodać w tym samouczku są nazywane automatycznie, należy wykonać ten krok.
   
    ![Nadawanie nazw węzła][config_hpc8]
   
9. W **listy zadań do wykonania wdrożenia**, kliknij przycisk **Tworzenie szablonu węzła**. W dalszej części samouczka używasz szablonu węzła można dodać węzły platformy Azure do klastra.

10. W węźle Kreatora tworzenia szablonu wykonaj następujące czynności:
    
    a. Na **wybierz typ szablonu węzła** kliknij **szablonu węzła usługi Windows Azure**, a następnie kliknij przycisk **dalej**.
    
    ![Węzeł szablonu][config_hpc10]
    
    b. Kliknij przycisk **dalej** do Zaakceptuj domyślną nazwę szablonu.
    
    c. Na **Podaj informacje o subskrypcji** strony, wprowadź swój identyfikator subskrypcji platformy Azure (dostępna w informacje o Twoim koncie platformy Azure). Następnie w **certyfikat zarządzania**, Przeglądaj w poszukiwaniu **domyślne Microsoft HPC Azure Management.** Następnie kliknij przycisk **Next** (Dalej).
    
    ![Węzeł szablonu][config_hpc12]
    
    d. Na **Podaj informacje o usłudze** wybierz usługę w chmurze i konto magazynu, który został utworzony w poprzednim kroku. Następnie kliknij przycisk **Next** (Dalej).
    
    ![Węzeł szablonu][config_hpc13]
    
    e. Kliknij przycisk **dalej** aby zaakceptować domyślne wartości na pozostałych stronach kreatora. Następnie na **przeglądu** kliknij pozycję **Utwórz** tworzenia szablonu węzła.
    
    > [!NOTE]
    > Domyślnie szablon węzeł platformy Azure zawiera ustawienia umożliwiające uruchamianie (zainicjować obsługi administracyjnej) i zatrzymywanie węzły ręcznie, przy użyciu Menedżera klastra HPC. Opcjonalnie można skonfigurować harmonogram w celu uruchamiania i zatrzymywania automatycznie węzły na platformie Azure.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Dodaj węzły na platformie Azure do klastra
Teraz można dodać węzły platformy Azure do klastra należy użyć szablonu węzła. Dodawania węzłów do klastra są przechowywane informacje o ich konfiguracji, tak, aby uruchomić (zainicjować obsługi administracyjnej) je w dowolnym momencie w usłudze w chmurze. Twoja subskrypcja pobiera naliczane tylko za węzły na platformie Azure po uruchomieniu wystąpienia w usłudze w chmurze.

Wykonaj następujące kroki, aby dodać dwa węzły małych.

1. W Menedżerze klastra HPC, kliknij przycisk **zarządzania węzłami** (o nazwie **zarządzania zasobami** w bieżących wersjach pakietu HPC Pack) > **Dodaj węzeł**.
   
    ![Dodaj węzeł][add_node1]

2. W Kreatorze dodawania węzłów na **wybierz metodę wdrożenia** kliknij **węzłów Dodaj platformy Windows Azure**, a następnie kliknij przycisk **dalej**.
   
    ![Dodaj węzeł platformy Azure][add_node1_1]

3. Na **określ nowe węzły** stronie, wybierz szablon węzeł platformy Azure, który został wcześniej utworzony (o nazwie domyślnie **domyślnego szablonu AzureNode**). Następnie określ **2** węzłów o rozmiarze **małych**, a następnie kliknij przycisk **dalej**.
   
    ![Określ węzłów][add_node2]
   
4. Na **Kończenie pracy Kreatora dodawania węzłów** kliknij **Zakończ**.
    
     Dwa węzły platformy Azure, o nazwie **AzureCN 0001** i **AzureCN 0002**, pojawiają się w Menedżerze klastra HPC. Oba są w **wdrożone nie** stanu.
   
    ![Dodano węzłów][add_node3]

## <a name="start-the-azure-nodes"></a>Rozpocznij węzły na platformie Azure
Jeśli chcesz korzystać z zasobów klastra na platformie Azure umożliwia Menedżer klastrów HPC uruchomienie węzłów (zainicjować obsługi administracyjnej) platformy Azure i przełączyć w tryb online.

1. W Menedżerze klastra HPC, kliknij przycisk **zarządzania węzłami** (o nazwie **zarządzania zasobami** w bieżących wersjach pakietu HPC Pack) i wybierz węzły na platformie Azure.

2. Kliknij przycisk **Start**, a następnie kliknij przycisk **OK**.
   
   ![Uruchomienie węzłów][add_node4]
   
    Węzły przejście do **aprowizacji** stanu. Wyświetl dziennik inicjowania obsługi administracyjnej w celu śledzenia postępu inicjowania obsługi administracyjnej.
   
    ![Aprowizacja węzłów][add_node6]

3. Po kilku minutach zakończyć aprowizację węzłów platformy Azure i są w **Offline** stanu. W tym stanie wystąpienia roli są uruchomione, ale nie może jeszcze akceptować zadań klastra.

4. Aby upewnić się, że wystąpienia roli są uruchomione w witrynie Azure portal kliknij pozycję **usług w chmurze (klasyczne)** > *your_cloud_service_name*.
   
   Powinny zostać wyświetlone dwa **HpcWorkerRole** wystąpienia (węzły) działające w usłudze. Pakiet HPC Pack również automatycznie wdraża dwa **HpcProxy** wystąpień (rozmiar średni) w celu obsługi komunikacji między węzłem głównym i platformą Azure.

   ![Uruchamianie wystąpień][view_instances1]

5. Aby przywrócić węzłów platformy Azure w trybie online do obsługi zadań klastra, zaznacz węzły, kliknij prawym przyciskiem myszy, a następnie kliknij **przejdź do trybu Online**.
   
    ![Węzły w trybie offline][add_node7]
   
    Menedżer klastrów HPC wskazuje, że węzły są w **Online** stanu.

## <a name="run-a-command-across-the-cluster"></a>Uruchom polecenie w klastrze
Aby sprawdzić instalację, należy użyć pakietu HPC Pack **clusrun** polecenie, aby uruchomić polecenie lub aplikacji na jeden lub więcej węzłów klastra. Prostym przykładem użycia **clusrun** można pobrać konfiguracji IP węzły na platformie Azure.

1. W węźle głównym Otwórz wiersz polecenia jako administrator.

2. Wpisz następujące polecenie:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Po wyświetleniu monitu wprowadź hasło administratora klastra. Polecenie dane wyjściowe powinny być podobne do następujących.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Uruchom zadanie testowe
Teraz można przesłać zadania testowego, który działa w klastrze hybrydowym. W tym przykładzie jest proste parametrycznych zadań (typ obliczenia równoległe). W tym przykładzie jest uruchamiany podzadań, które dodać liczbę całkowitą do siebie samego przy użyciu **set /a** polecenia. Wszystkie węzły w klastrze składają się na zakończenie podzadania liczb całkowitych z zakresu od 1 do 100.

1. W Menedżerze klastra HPC, kliknij przycisk **zadań zarządzania** > **nowe zadanie Parametric Sweep**.
   
    ![Nowe zadanie][test_job1]

2. W **nowe zadanie Parametric Sweep** dialogowym **wiersza polecenia**, typ `set /a *+*` (Zastępowanie domyślnego wiersza polecenia, który pojawia się). Pozostaw wartości domyślne pozostałych ustawień, a następnie kliknij przycisk **przesyłania** Aby przesłać zadanie.
   
    ![Parametrycznych][param_sweep1]

3. Po zakończeniu zadania, kliknij dwukrotnie **Moje zadania odchylenia** zadania.

4. Kliknij przycisk **zadania widoku**, a następnie kliknij przycisk w podzadanie do wyświetlania danych wyjściowych obliczeniowego tego podzadania.
   
    ![Wyniki zadania][view_job361]

5. Aby zobaczyć, który węzeł wykonywane obliczenia na tym samym poziomie, kliknij **przydzielonych węzłów**. (Klaster może wyświetlać nazwę innego węzła).
   
    ![Wyniki zadania][view_job362]

## <a name="stop-the-azure-nodes"></a>Zatrzymaj węzły na platformie Azure
Po klastra możesz wypróbować, Zatrzymaj węzłów platformy Azure, aby uniknąć niepotrzebnych opłat do swojego konta. W tym kroku zatrzymuje usługę w chmurze i usuwa wystąpień roli platformy Azure.

1. W Menedżerze klastra HPC w **zarządzania węzłami** (o nazwie **zarządzania zasobami** w poprzednich wersjach pakietu HPC Pack), wybierz oba węzły na platformie Azure. Następnie kliknij przycisk **zatrzymać**.
   
    ![Zatrzymaj węzłów][stop_node1]

2. W **węzłów zatrzymania usługi Windows Azure** okno dialogowe, kliknij przycisk **zatrzymać**. 
   
3. Węzły przejście do **zatrzymywanie** stanu. Po kilku minutach, Menedżer klastra HPC wskazuje, że węzły są **wdrożone nie**.
   
    ![Węzły Niewdrożone][stop_node4]

4. Aby upewnić się, że wystąpienia roli nie są już uruchomione na platformie Azure w witrynie Azure portal kliknij **Cloud services (wersja klasyczna)** > *your_cloud_service_name*. Brak wystąpień są wdrażane w środowisku produkcyjnym. 
   
    Na tym kończy się tego samouczka.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się z dokumentacją dla [pakietu HPC Pack](https://technet.microsoft.com/library/cc514029).
* Do skonfigurowania hybrydowego wdrożenia klastra pakietu HPC Pack na większą skalę, zobacz [serii do wystąpień roli procesu roboczego platformy Azure za pomocą pakietu Microsoft HPC Pack](https://go.microsoft.com/fwlink/p/?LinkID=200493).
* Inne sposoby tworzenia klastra pakietu HPC Pack na platformie Azure, w tym za pomocą szablonów usługi Azure Resource Manager, zobacz [opcji klastra HPC za pomocą pakietu Microsoft HPC Pack na platformie Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
