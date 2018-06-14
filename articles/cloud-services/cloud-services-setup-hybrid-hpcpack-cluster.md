---
title: Konfigurowanie klastra HPC Pack hybrydowe na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i do skonfigurowania hybrydowego wysokiej wydajności (HPC) klastrów obliczeniowych mały Użyj Microsoft HPC Pack i Azure
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
ms.openlocfilehash: ad5c13723eef352148a40e3e7f4f2ff616867296
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2017
ms.locfileid: "23985310"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Skonfiguruj hybrydowych wysokiej wydajności obliczeniowej klastra (HPC) z pakietu Microsoft HPC Pack i węzłów obliczeń platformy Azure na żądanie
Umożliwia Microsoft HPC Pack 2012 R2 i Azure skonfigurować małe, hybrydowego wysokiej wydajności obliczeniowej klastra (HPC). Klastra wyświetlane w tym artykule składa się z węzłem głównym HPC Pack lokalnymi i niektóre wyliczenia węzłów wdrażanie na żądanie w systemie Azure usługa w chmurze. Następnie można uruchomić zadania obliczeń w klastrze hybrydowego.

![Klaster HPC hybrydowego][Overview] 

Ten samouczek pokazuje, jednym z podejść, czasem nazywana klastrem "serii w chmurze" na potrzeby uruchamiania aplikacji obliczeniowych skalowalne, na żądanie zasobów platformy Azure.

Ten samouczek zakłada nie doświadczenie z lub klastry obliczeniowe HPC Pack. Ma ona tylko pomogą Ci we wdrażaniu klastra obliczeniowego hybrydowego szybko w celach demonstracyjnych. Zagadnienia i czynności, aby wdrożyć klaster HPC Pack hybrydowe na większą skalę w środowisku produkcyjnym lub użyć HPC Pack 2016, zobacz [szczegółowe wskazówki](http://go.microsoft.com/fwlink/p/?LinkID=200493). W innych sytuacjach pakietem HPC łącznie z automatycznego wdrażania klastra na maszynach wirtualnych Azure, zobacz [opcje klastra HPC z pakietem Microsoft HPC w systemie Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure** — Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/) w zaledwie kilka minut.
* **Na komputerze lokalnym systemem Windows Server 2012 R2 lub Windows Server 2012** -używa tego komputera jako węzła głównego klastra HPC. Jeśli nie są już systemem Windows Server, musisz pobrać i zainstalować [wersji ewaluacyjnej](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Komputer musi należeć do domeny usługi Active Directory. Do celów testowych można skonfigurować węzła głównego komputer jako kontroler domeny. Aby dodać rolę serwera usług domenowych w usłudze Active Directory, a następnie Podwyższ węzła głównego komputer jako kontroler domeny, zobacz dokumentację dla systemu Windows Server.
  * Aby obsługiwać HPC Pack, systemu operacyjnego muszą zostać zainstalowane w jednym z tych języków: angielski, japoński lub angielski, chiński (uproszczony).
  * Sprawdź, czy krytycznych i ważne aktualizacje są instalowane.
* **HPC Pack 2012 R2** - [Pobierz](http://go.microsoft.com/fwlink/p/?linkid=328024) instalacji pakietu do najnowszej wersji bezpłatnie i skopiuj pliki na komputerze węzła głównego. Wybierz pliki instalacyjne, w tym samym języku co instalacji systemu Windows Server.

    >[!NOTE]
    > Jeśli chcesz użyć HPC Pack 2016 zamiast HPC Pack 2012 R2, są wymagane dodatkowe czynności konfiguracyjne. Zobacz [szczegółowe wskazówki](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Konto domeny** — to konto musi mieć uprawnienia administratora lokalnego w węźle głównym HPC Pack instalacji.
* **Połączenie TCP na porcie 443** z węzłem głównym na platformie Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Zainstaluj w węźle głównym HPC Pack
Microsoft HPC Pack należy najpierw zainstalować na komputerze lokalnym systemem Windows Server. Ten komputer staje się węzła głównego klastra.

1. Zaloguj się do węzła głównego przy użyciu konta domeny, które ma uprawnienia administratora lokalnego.

2. Uruchom Kreatora instalacji pakietu HPC, uruchamiając polecenie Setup.exe z plików instalacyjnych HPC Pack.

3. Na **Instalator HPC Pack 2012 R2** kliknij **nowej instalacji lub dodawania nowych funkcji do istniejącej instalacji**.

    ![HPC Pack 2012 Instalatora][install_hpc1]

4. Na **strony Umowy użytkownika oprogramowania Microsoft**, kliknij przycisk **dalej**.

5. Na **Wybieranie typu instalacji** kliknij przycisk **utworzenie nowego klastra HPC, tworząc węzła głównego**, a następnie kliknij przycisk **dalej**.

6. Kreator przeprowadza kilku testów przed instalacją. Kliknij przycisk **dalej** na **reguły instalacji** strony, jeśli wszystkie testy zostały zaliczone pomyślnie. W przeciwnym razie zapoznaj się z informacjami i wprowadź niezbędne zmiany w danym środowisku. Następnie ponownie uruchom testy lub w razie potrzeby uruchom Kreatora instalacji ponownie.
7. Na **konfiguracji bazy danych HPC** upewnij się, że **Head, węzła** jest zaznaczone dla wszystkich baz danych HPC, a następnie kliknij przycisk **dalej**. 

    ![Konfiguracja bazy danych][install_hpc4]

8. Zaakceptuj domyślne na pozostałych stronach kreatora. Na **zainstalować wymagane składniki** kliknij przycisk **zainstalować**.
   
    ![Instalowanie][install_hpc6]

9. Po zakończeniu instalacji, usuń zaznaczenie pola wyboru **Uruchom Menedżera klastra HPC** , a następnie kliknij przycisk **Zakończ**. (Można uruchomić Menedżera klastra HPC w kolejnym kroku.)
   
    ![Zakończ][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Przygotowanie subskrypcji platformy Azure
Wykonaj poniższe kroki w [portalu Azure](https://portal.azure.com) z subskrypcją platformy Azure. Po wykonaniu tych czynności można wdrożyć węzły platformy Azure z węzłem głównym lokalnymi. 
  
  > [!NOTE]
  > Również Zanotuj identyfikator subskrypcji platformy Azure, która jest potrzebna później. Identyfikator znajduje się w **subskrypcje** w portalu.
  > 

### <a name="upload-the-default-management-certificate"></a>Przekaż domyślnego certyfikatu zarządzania
HPC Pack instaluje certyfikat z podpisem własnym na węzła głównego o nazwie certyfikatu domyślne Microsoft HPC Azure Management, który można przekazać jako certyfikat zarządzania platformy Azure. Ten certyfikat jest dostępne w celu wdrożenia testowania i weryfikacja koncepcji bezpiecznego połączenia między węzłem głównym i platformą Azure.

1. Na komputerze węzła głównego, zaloguj się do [portalu Azure](https://portal.azure.com).

2. Kliknij przycisk **subskrypcje** > *your_subscription_name*.

3. Kliknij przycisk **certyfikaty zarządzania** > **przekazać**.

4. Przeglądaj w węźle głównym dla pliku 2012\Bin\hpccert.cer C:\Program Files\Microsoft HPC Pack. Następnie kliknij przycisk **przekazać**.

   
**Domyślne HPC Azure Management** certyfikatu zostanie wyświetlony na liście certyfikatów zarządzania.

### <a name="create-an-azure-cloud-service"></a>Tworzenie usługi w chmurze platformy Azure
> [!NOTE]
> Aby uzyskać najlepszą wydajność należy utworzyć usługi w chmurze i konto magazynu (w kolejnym kroku) w tym samym regionie geograficznym.
> 
> 

1. W portalu kliknij **usługi (klasyczne) w chmurze** > **+ Dodaj**.

2.  Wpisz nazwę DNS dla usługi, wybierz grupę zasobów i lokalizację, a następnie kliknij **Utwórz**.


### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
1. W portalu kliknij **kont magazynu (klasyczne)** > **+ Dodaj**.

2. Wpisz nazwę konta, a następnie wybierz **klasycznego** modelu wdrażania.

3. Wybierz grupę zasobów i lokalizację i pozostaw innych ustawień na wartości domyślne. Następnie kliknij pozycję **Utwórz**.

## <a name="configure-the-head-node"></a>Skonfiguruj węzła głównego
Aby użyć Menedżera klastra HPC, aby wdrożyć węzły platformy Azure i do przesyłania zadań, należy najpierw wykonać pewne czynności konfiguracyjne wymagane klastra.

1. W węźle głównym Uruchom Menedżera klastra HPC. Jeśli **wybierz węzeł Head** zostanie wyświetlone okno dialogowe, kliknij przycisk **komputera lokalnego**. **Listy zadań do wykonania wdrożenia** pojawi się.

2. W obszarze **wymagane zadania wdrażania**, kliknij przycisk **konfiguracji sieci**.
   
    ![Konfigurowanie sieci][config_hpc2]

3. W Kreatorze konfiguracji sieci wybierz **wszystkie węzły tylko w sieci przedsiębiorstwa** (topologia 5). Ta konfiguracja sieci jest najprostsza dla celów demonstracyjnych.
   
    ![Topologia 5][config_hpc3]
   
4. Kliknij przycisk **dalej** zaakceptować wartości domyślne na pozostałych stronach kreatora. Następnie na **przeglądu** , kliknij pozycję **Konfiguruj** w celu ukończenia konfiguracji sieci.

5. W **listy zadań do wykonania wdrożenia**, kliknij przycisk **poświadczenia instalacji**.

6. W **poświadczeń instalacji** oknie dialogowym wpisz poświadczenia konta domeny, które zostały użyte do zainstalowania HPC Pack. Następnie kliknij przycisk **OK**. 
   
    ![Poświadczenia instalacji][config_hpc6]
   
7. W **listy zadań do wykonania wdrożenia**, kliknij przycisk **skonfigurować nazewnictwa nowe węzły**.

8. W **Określ węzeł nazwy serii** okna dialogowego Zaakceptuj domyślne nazwy serii i kliknij przycisk **OK**. Ukończenia tego kroku, nawet jeśli węzły platformy Azure, dodane w tym samouczku są nazywane automatycznie.
   
    ![Węzeł nazewnictwa][config_hpc8]
   
9. W **listy zadań do wykonania wdrożenia**, kliknij przycisk **Tworzenie szablonu węzła**. Później w samouczku używasz szablonu węzła Aby dodać węzły platformy Azure do klastra.

10. W węźle Kreator tworzenia szablonu wykonaj następujące czynności:
    
    a. Na **wybierz typ szablonu węzła** kliknij przycisk **szablonu węzła w systemie Windows Azure**, a następnie kliknij przycisk **dalej**.
    
    ![Szablon węzła][config_hpc10]
    
    b. Kliknij przycisk **dalej** zaakceptować domyślną nazwę szablonu.
    
    c. Na **Podaj informacje o subskrypcji** wprowadź identyfikator subskrypcji platformy Azure (dostępne w danych konta platformy Azure). Następnie w **certyfikat zarządzania**, wyszukaj **domyślne Microsoft HPC Azure Management.** Następnie kliknij przycisk **Next** (Dalej).
    
    ![Szablon węzła][config_hpc12]
    
    d. Na **Podaj informacje o usłudze** wybierz usługę w chmurze i konto magazynu, który został utworzony w poprzednim kroku. Następnie kliknij przycisk **Next** (Dalej).
    
    ![Szablon węzła][config_hpc13]
    
    e. Kliknij przycisk **dalej** zaakceptować wartości domyślne na pozostałych stronach kreatora. Następnie na **przeglądu** , kliknij pozycję **Utwórz** do utworzenia szablonu węzła.
    
    > [!NOTE]
    > Domyślnie szablon Azure węzła zawiera ustawienia umożliwiające uruchamianie (udostępniania) i zatrzymywanie węzłów ręcznie, za pomocą Menedżera klastra HPC. Opcjonalnie można skonfigurować harmonogram uruchamiania i zatrzymywania automatycznie węzły platformy Azure.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Dodaj węzły platformy Azure do klastra
Teraz można użyć szablonu węzła, aby dodać węzły platformy Azure do klastra. Dodawania węzłów do klastra są przechowywane informacje o ich konfiguracji, tak aby (zainicjować obsługi administracyjnej) można uruchomić je w dowolnym momencie w usłudze w chmurze. Subskrypcja tylko pobiera obciążony węzły platformy Azure po uruchomionych wystąpień w usłudze w chmurze.

Wykonaj następujące kroki, aby dodać dwa węzły małych.

1. W Menedżerze klastra HPC, kliknij przycisk **węzła zarządzania** (nazywane **zarządzanie zasobami** w bieżącej wersji pakietu HPC) > **Dodaj węzeł**.
   
    ![Dodaj węzeł][add_node1]

2. W Kreatorze dodawania węzła na **wybierz metodę wdrożenia** kliknij przycisk **węzłów dodać systemu Windows Azure**, a następnie kliknij przycisk **dalej**.
   
    ![Dodaj węzeł Azure][add_node1_1]

3. Na **określ nowe węzły** wybierz wcześniej utworzony szablon Azure węzeł (nazywany domyślnie **domyślny szablon AzureNode**). Następnie określ **2** węzłów rozmiar **małych**, a następnie kliknij przycisk **dalej**.
   
    ![Określ węzły][add_node2]
   
4. Na **Kończenie pracy Kreatora dodawania węzłów** kliknij przycisk **Zakończ**.
    
     Dwa węzły platformy Azure o nazwie **AzureCN 0001** i **AzureCN 0002**, zostaną wyświetlone w Menedżerze klastra HPC. Są w **wdrożone nie** stanu.
   
    ![Dodanych węzłach][add_node3]

## <a name="start-the-azure-nodes"></a>Uruchom węzły platformy Azure
Jeśli chcesz korzystać z zasobów klastra na platformie Azure, należy użyć Menedżera klastra HPC do uruchomienia węzłów (zainicjować obsługi administracyjnej) platformy Azure i umieść je w tryb online.

1. W Menedżerze klastra HPC, kliknij przycisk **węzła zarządzania** (nazywane **zarządzanie zasobami** w bieżącej wersji pakietu HPC) i wybierz węzły platformy Azure.

2. Kliknij przycisk **Start**, a następnie kliknij przycisk **OK**.
   
   ![Uruchom węzłów][add_node4]
   
    Węzły przejście do **inicjowania obsługi administracyjnej** stanu. Wyświetl dziennik inicjowania obsługi administracyjnej, aby śledzić postęp inicjowania obsługi administracyjnej.
   
    ![Zainicjuj obsługę węzłów][add_node6]

3. Po kilku minutach węzłów Azure zakończyć inicjowanie obsługi i znajdują się w **Offline** stanu. W tym stanie wystąpień ról są uruchomione, ale jeszcze nie może zaakceptować zadań klastra.

4. Aby upewnić się, że uruchomiona są wystąpień roli w portalu Azure kliknij **usługi w chmurze (klasyczne)** > *your_cloud_service_name*.
   
   Powinny pojawić się dwa **HpcWorkerRole** wystąpień (węzłów) w usłudze. HPC Pack automatycznie wdraża dwa **HpcProxy** wystąpień (rozmiar średnia liczba godzin) do obsługi komunikacji między węzłem głównym i platformą Azure.

   ![Uruchomione wystąpienia][view_instances1]

5. Aby wyświetlić Azure węzły w tryb online w celu uruchomienia zadań klastra, wybierz węzły, kliknij prawym przyciskiem myszy, a następnie kliknij **przejdź do trybu Online**.
   
    ![Węzły w trybie offline][add_node7]
   
    Menedżer klastrów HPC wskazuje, że węzły są w **Online** stanu.

## <a name="run-a-command-across-the-cluster"></a>Uruchom polecenie w ramach klastra
Aby sprawdzić instalację, należy użyć HPC Pack **clusrun** polecenie do uruchomienia polecenia lub aplikacji na co najmniej jednym węźle klastra. Prosty przykład użyć **clusrun** można pobrać konfiguracji IP węzły platformy Azure.

1. W węźle głównym Otwórz wiersz polecenia jako administrator.

2. Wpisz następujące polecenie:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Jeśli zostanie wyświetlony monit, wprowadź hasło administratora klastra. Powinny pojawić się podobne do następujących danych wyjściowych polecenia.
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>Uruchom zadanie testowe
Teraz przesłać zadanie testów, które działa w klastrze hybrydowego. W tym przykładzie jest zadaniem proste parametrycznych (typ obliczenia leżą równoległe). W tym przykładzie jest uruchamiany podzadania zwiększających całkowitą do siebie samego za pomocą **ustawić /a** polecenia. Wszystkie węzły w klastrze przyczyniają się do zakończenia podzadań liczb całkowitych od 1 do 100.

1. W Menedżerze klastra HPC, kliknij przycisk **zadania zarządzania** > **nowe zadanie parametrycznych odchylenia**.
   
    ![Nowe zadanie][test_job1]

2. W **nowe zadanie parametrycznych odchylenia** okna dialogowego, **wiersza polecenia**, typ `set /a *+*` (Zastępowanie domyślnego wiersza polecenia pojawi się). Pozostaw wartości domyślne dla pozostałych ustawień, a następnie kliknij przycisk **przesyłania** można przesłać zadania.
   
    ![Parametrycznych][param_sweep1]

3. Po zakończeniu zadania, kliknij dwukrotnie **Moje zadania odchylenia** zadania.

4. Kliknij przycisk **zadania widoku**, a następnie kliknij przycisk podzadania, aby wyświetlić obliczeniowej danych wyjściowych w tym samym poziomie.
   
    ![Wyniki zadań][view_job361]

5. Aby zobaczyć, który węzeł wykonać obliczenia na tym samym poziomie, kliknij **przydzielone węzłów**. (Klastra mogą być wyświetlane nazwy innego węzła).
   
    ![Wyniki zadań][view_job362]

## <a name="stop-the-azure-nodes"></a>Zatrzymaj węzły platformy Azure
Po wypróbowanie klastra, należy zatrzymać węzły platformy Azure, aby uniknąć niepotrzebnych opłat do Twojego konta. Ten krok zatrzymuje usługę w chmurze i usuwa wystąpień roli platformy Azure.

1. W Menedżerze klastra HPC w **węzła zarządzania** (nazywane **zarządzanie zasobami** w poprzednich wersjach programu HPC Pack), wybierz oba węzły platformy Azure. Następnie kliknij przycisk **zatrzymać**.
   
    ![Zatrzymaj węzłów][stop_node1]

2. W **zatrzymania systemu Windows Azure węzłów** okno dialogowe, kliknij przycisk **zatrzymać**. 
   
3. Węzły przejście do **zatrzymywanie** stanu. Po upływie kilku minut, Menedżer klastra HPC pokazuje, że węzły są **wdrożone nie**.
   
    ![Węzły Niewdrożone][stop_node4]

4. Aby upewnić się, że wystąpień roli nie są już uruchomione na platformie Azure w portalu Azure kliknij **usługi (klasyczne) w chmurze** > *your_cloud_service_name*. Brak wystąpień są wdrażane w środowisku produkcyjnym. 
   
    Na tym kończy się samouczka.

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z dokumentacją dla [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Aby skonfigurować hybrydowe wdrożenie klastra HPC Pack na większą skalę, zobacz [serii do wystąpień roli procesu roboczego platformy Azure z pakietem Microsoft HPC](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Inne sposoby tworzenia klastra HPC Pack na platformie Azure, w tym za pomocą szablonów usługi Azure Resource Manager dla [opcje klastra HPC z pakietem Microsoft HPC w systemie Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
