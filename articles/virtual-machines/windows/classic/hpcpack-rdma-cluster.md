---
title: Konfigurowanie klastra systemu Windows RDMA na uruchamianie aplikacji MPI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster Windows HPC Pack o rozmiarze H16r H16mr, A8 i A9 maszyn wirtualnych do używania w sieci Azure RDMA do uruchamiania aplikacji MPI.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 03/06/2018
ms.author: danlep
ms.openlocfilehash: 437c475735ec3823de51c5f9e996a5303fe9cfa7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852544"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Konfigurowanie klastra RDMA systemu Windows z pakietem HPC do uruchamiania aplikacji MPI
Konfigurowanie klastra RDMA systemu Windows na platformie Azure z [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) i [rozmiarów z funkcją RDMA HPC maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) na uruchamianie równoległe aplikacji komunikat interfejsu (Passing Interface). Po skonfigurowaniu z funkcją RDMA, opartych na systemie Windows Server węzłów w klastrze HPC Pack aplikacji MPI wydajnie komunikacji za pośrednictwem nieznaczne opóźnienia, uzyskać wysoką przepustowość sieci na platformie Azure, oparty na technologii dostępu do (pamięci RDMA) zdalnego pamięci.

Jeśli chcesz uruchamiać MPI obciążenia na maszynach wirtualnych systemu Linux, które uzyskują dostęp do sieci Azure RDMA, zobacz [Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI](../../linux/classic/rdma-cluster.md).

## <a name="hpc-pack-cluster-deployment-options"></a>Opcje wdrażania klastra HPC Pack
Microsoft HPC Pack to narzędzie, bez ponoszenia dodatkowych kosztów można utworzyć HPC klastrów lokalnie lub na platformie Azure na uruchamianie aplikacji systemu Windows lub Linux HPC. HPC Pack zawiera środowiska uruchomieniowego dla przez firmę Microsoft implementacją komunikat przekazywanie interfejsu systemu Windows (MS-MPI). W przypadku użycia z funkcją RDMA wystąpień uruchomiony obsługiwany system operacyjny Windows Server, HPC Pack zapewnia wydajne opcję uruchamiania aplikacji MPI systemu Windows, które uzyskują dostęp do sieci Azure RDMA. 

W tym artykule przedstawiono dwa scenariusze i linki do szczegółowych wskazówek, aby skonfigurować klaster Windows RDMA o Microsoft HPC Pack 2012 R2. 

* Scenariusz 1. Wdrażanie wystąpień roli procesu roboczego obliczeniowych (PaaS)
* Scenariusz 2. Wdrażanie węzłów obliczeniowych w obliczeniowych maszyn wirtualnych (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenariusz 1: Wdrażanie wystąpień roli procesu roboczego obliczeniowych (PaaS)
Z istniejącego klastra HPC Pack należy dodać zasoby obliczeniowe dodatkowe w wystąpień roli procesu roboczego platformy Azure (Azure węzłów) w chmurze usługa (PaaS). Ta funkcja, nazywane również "serii Azure" z HPC Pack obsługuje zakres rozmiarów dla wystąpień roli procesu roboczego. Podczas dodawania węzłów Azure, określ jedną z funkcją RDMA rozmiary.

Poniżej przedstawiono zagadnienia i czynności, aby zwiększania możliwości z funkcją RDMA wystąpieniach platformy Azure z istniejącego (zwykle w siedzibie firmy) klastra. Użyj podobne procedury, aby dodać wystąpienia roli procesu roboczego z węzłem głównym HPC Pack, które zostało wdrożone w maszynie Wirtualnej platformy Azure.

> [!NOTE]
> Samouczek serii na platformie Azure z pakietem HPC, zobacz [Konfigurowanie klastra hybrydowego pakietem HPC](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Należy pamiętać, zagadnień opisane w poniższych krokach, które dotyczą przede wszystkim z funkcją RDMA węzły platformy Azure.
> 
> 

![Serii na platformie Azure][burst]

### <a name="steps"></a>Kroki
1. **Wdrażanie i konfigurowanie węzłem głównym HPC Pack 2012 R2**
   
    Aby pobrać pakiet instalacyjny HPC Pack z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Wymagania i instrukcje dotyczące przygotowania do wdrożenia serii Azure, zobacz [serii do wystąpień procesu roboczego platformy Azure z pakietem Microsoft HPC](https://technet.microsoft.com/library/gg481749.aspx).
2. **Konfigurowanie certyfikatu zarządzania w subskrypcji platformy Azure**
   
    Konfigurowanie certyfikatu do zabezpieczenia połączenia między węzłem głównym i platformą Azure. Opcje i procedury, zobacz [scenariuszami, aby skonfigurować certyfikat zarządzania platformy Azure dla HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). W przypadku wdrożeń testowych HPC Pack instaluje domyślne Microsoft HPC certyfikat zarządzania platformy Azure możesz szybko przekazać do subskrypcji platformy Azure.
3. **Utwórz nową usługę w chmurze i konto magazynu**
   
    Użyj portalu Azure można utworzyć usługi w chmurze (klasyczne) i konto magazynu (klasyczne) do wdrożenia. Utwórz następujące zasoby w regionie, w którym jest dostępny rozmiar serii H A8 i A9, którego chcesz użyć. Zobacz [produkty Azure według regionu](https://azure.microsoft.com/regions/services/).

4. **Tworzenie szablonu Azure węzła**
   
    Użyj węzła Kreator tworzenia szablonu w Menedżerze klastra HPC. Aby uzyskać instrukcje, zobacz [utworzyć szablon Azure węzła](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) w "Kroki do wdrożenia usługi Azure węzłów z Microsoft HPC Pack".
   
    Początkowa testów zalecamy Konfigurowanie zasad ręczne dostępności w szablonie.
5. **Dodaj węzły do klastra**
   
    Użyj Kreator dodawania węzłów w Menedżerze klastra HPC. Aby uzyskać więcej informacji, zobacz [Dodawanie węzłów Azure do klastra HPC systemu Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Podczas określania rozmiaru węzłów, wybierz jedną z funkcją RDMA rozmiary wystąpienia.
   
   > [!NOTE]
   > W każdej serii do wdrożenia usługi Azure wystąpieniami obliczeniowych HPC Pack automatycznie wdraża co najmniej dwa wystąpienia z funkcją RDMA (na przykład A8) jako węzły serwera proxy, oprócz wystąpień roli procesu roboczego platformy Azure, które określisz. Węzły serwera proxy używają rdzeni, które są przydzielone do subskrypcji i spowodować naliczenie opłat wraz z wystąpień roli procesu roboczego platformy Azure.
   > 
   > 
6. **Start (zainicjować obsługi administracyjnej) węzły i umieść je w trybie online do uruchomienia zadań**
   
    Wybierz węzły i użyj **Start** akcji w Menedżerze klastra HPC. Po ukończeniu inicjowania obsługi administracyjnej Wybierz węzły i używać **przejdź do trybu Online** akcji w Menedżerze klastra HPC. Węzły są gotowe do uruchomienia zadania.
7. **Przesyłanie zadań do klastra**
   
   Użyj narzędzia przesyłania zadania HPC Pack do uruchomienia zadań klastra. Zobacz [Microsoft HPC Pack: Zarządzanie zadaniami](http://technet.microsoft.com/library/jj899585.aspx).
8. **Zatrzymaj (deprovision) węzły**
   
   Po zakończeniu uruchomionych zadań węzły Przełącz do trybu offline i użyć **zatrzymać** akcji w Menedżerze klastra HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenariusz 2: Wdrażanie obliczeniowe węzłów obliczeniowych maszyn wirtualnych (IaaS)
W tym scenariuszu wdrażania węzłem głównym HPC Pack i węzły obliczeniowe klastra na maszynach wirtualnych w sieci wirtualnej platformy Azure. HPC Pack udostępnia wiele [opcje wdrażania na maszynach wirtualnych Azure](../../linux/hpcpack-cluster-options.md), włącznie z automatycznego wdrażania skryptów i szablonów Szybki Start Azure. Na przykład następujące zagadnienia i czynności prowadzące do użycia [skrypt wdrożenia HPC Pack IaaS](hpcpack-cluster-powershell-script.md) można zautomatyzować wdrożenie klastra HPC Pack 2012 R2 na platformie Azure.

![Klaster maszyny wirtualne platformy Azure][iaas]

### <a name="steps"></a>Kroki
1. **Tworzenie węzła głównego klastra i obliczeniowe węzła maszyn wirtualnych, uruchamiając skrypt wdrożenia HPC Pack IaaS na komputerze klienckim**
   
    Pobierz skrypt wdrożenia IaaS pakietu HPC z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Aby przygotować komputer kliencki, tworzenia pliku skryptu konfiguracji, a następnie uruchom skrypt, zobacz [utworzyć klaster HPC z skrypt wdrożenia HPC Pack IaaS](hpcpack-cluster-powershell-script.md). 
   
    Aby kwestie dotyczące wdrażania z funkcją RDMA węzły obliczeniowe, zobacz [wysokiej wydajności obliczeniowe rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) i należy uwzględnić następujące informacje:
   
   * **Sieć wirtualna**: Określ nową sieć wirtualną w regionie, w której chcesz użyć rozmiaru H-series A8 i A9 jest dostępny. Zobacz [produkty Azure według regionu](https://azure.microsoft.com/regions/services/).

   * **System operacyjny Windows Server**: do obsługi połączeń RDMA, określić zgodne systemu Windows Server system operacyjny, takie jak Windows Server 2012 R2 dla węzła obliczeniowego maszyn wirtualnych.
   * **Usługi w chmurze**: ponieważ skrypt używa klasycznego modelu wdrażania, maszyn wirtualnych klastra są wdrażane za pomocą usług w chmurze Azure (`ServiceName` ustawień w pliku konfiguracji). Zaleca się wdrożenie z węzła głównego w jednej usłudze w chmurze i węzłów obliczeniowych w innej usługi chmury. 
   * **HEAD rozmiaru węzła**: W tym scenariuszu należy wziąć pod uwagę o rozmiarze co najmniej A4 (bardzo duży) dla węzła głównego.
   * **Rozszerzenie HpcVmDrivers**: skrypt wdrożenia instaluje agenta maszyny Wirtualnej platformy Azure i rozszerzenie HpcVmDrivers automatycznie podczas wdrażania rozmiar A8 i A9 węzły obliczeniowe z systemem operacyjnym Windows Server. HpcVmDrivers instaluje sterowniki w węźle obliczeń maszyn wirtualnych, aby umożliwić im połączenie z siecią RDMA. Na maszynach wirtualnych z funkcją RDMA H-series należy ręcznie zainstalować rozszerzenie HpcVmDrivers. Zobacz [wysokiej wydajności obliczeniowe rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Konfiguracja sieci klastra**: skrypt wdrożenia automatycznie konfiguruje klastra HPC Pack w topologii 5 (we wszystkich węzłach w sieci firmowej). Ta topologia jest wymagany dla wszystkich wdrożeń klastrów HPC Pack na maszynach wirtualnych. Nie należy zmieniać topologii sieci klastra później.
1. **Przełącz do uruchomienia zadań węzłów obliczeniowych w trybie online**
   
    Wybierz węzły i użyj **przejdź do trybu Online** akcji w Menedżerze klastra HPC. Węzły są gotowe do uruchomienia zadania.
3. **Przesyłanie zadań do klastra**
   
    Połączenie z węzłem głównym do przesyłania zadań lub ustawienie na komputerze lokalnym w tym celu. Aby uzyskać informacje, zobacz [przesyłania zadań HPC klastra w systemie Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Przełącz do trybu offline węzły i Zatrzymaj (deallocate) je**
   
    Gdy wszystko będzie gotowe wykonywanych zadań, należy podjąć węzły w tryb offline w Menedżerze klastra HPC. Następnie użyj narzędzi zarządzania Azure, aby je zamknąć.

## <a name="run-mpi-applications-on-the-cluster"></a>Uruchamianie aplikacji MPI w klastrze
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Przykład: Uruchom mpipingpong w klastrze HPC Pack
Aby zweryfikować wdrożenie HPC Pack wystąpień z funkcją RDMA, uruchom HPC Pack **mpipingpong** polecenia w klastrze. **mpipingpong** wysyła pakiety danych między węzły sparowanego wielokrotnie w celu obliczenia opóźnienia i przepływności pomiarów i statystyki dla sieci aplikacji z obsługą funkcji RDMA. W tym przykładzie przedstawiono typowy wzorzec do uruchamiania zadań MPI (w tym przypadku **mpipingpong**) przy użyciu klastra **mpiexec** polecenia.

W tym przykładzie przyjęto założenie, dodać węzły platformy Azure w ramach konfiguracji "serii Azure" ([scenariusz 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Jeśli HPC Pack wdrożone w klastrze maszynach wirtualnych platformy Azure, należy zmodyfikować składni polecenia określić grupę na inny węzeł i ustaw dodatkowe zmienne środowiskowe do kierowania ruchu sieciowego do sieci RDMA.

Aby uruchomić mpipingpong w klastrze:

1. W węźle głównym lub na komputerze klienckim poprawnie skonfigurowane Otwórz wiersz polecenia.
2. Aby oszacować opóźnienia między parami węzłów we wdrożeniu serii Azure w czterech węzłów, wpisz następujące polecenie, aby przesłać zadanie można uruchomić mpipingpong o rozmiar pakietu małe i dużo iteracji:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Polecenie zwraca identyfikator zadania, w której zostało przesłane.
   
    Jeśli wdrożono w klastrze HPC Pack wdrożonych na maszynach wirtualnych Azure, określ grupy węzła, który zawiera compute węzła maszyn wirtualnych wdrożonych w usłudze w chmurze pojedynczego i zmodyfikować **mpiexec** polecenia w następujący sposób:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Po zakończeniu zadania, aby wyświetlić dane wyjściowe (w tym przypadku dane wyjściowe zadania 1 zadania), wpisz następujące polecenie
   
    ```Command
    task view <JobID>.1
    ```
   
    gdzie &lt; *JobID* &gt; jest Identyfikatorem zadania, które zostało przesłane.
   
    Dane wyjściowe obejmuje opóźnienia wyniki podobne do następującego.
   
    ![Czas oczekiwania na polecenie ping pong][pingpong1]
4. Aby oszacować przepustowość między parami węzłów Azure serii, wpisz następujące polecenie, aby przesłać zadania do uruchomienia **mpipingpong** rozmiar dużych pakietów i kilka iteracji:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Polecenie zwraca identyfikator zadania, w której zostało przesłane.
   
    W klastrze HPC Pack wdrożonych na maszynach wirtualnych Azure należy zmodyfikować polecenie tak, zgodnie z opisem w kroku 2.
5. Po zakończeniu zadania, aby wyświetlić dane wyjściowe (w tym przypadku dane wyjściowe zadania 1 zadania), wpisz następujące polecenie:
   
    ```Command
    task view <JobID>.1
    ```
   
   Dane wyjściowe obejmują przepływności podobny do następującego.
   
   ![Przepływność pong ping][pingpong2]

### <a name="mpi-application-considerations"></a>Zagadnienia dotyczące aplikacji MPI
Poniżej przedstawiono zagadnienia dotyczące uruchamiania aplikacji MPI pakietem HPC na platformie Azure. Niektóre są stosowane tylko do wdrożeń węzły platformy Azure (wystąpienia roli procesu roboczego dodane w ramach konfiguracji "serii Azure").

* Wystąpienia roli procesu roboczego w usłudze w chmurze są okresowo ponownie udostępnić bez uprzedzenia przez platformę Azure (na przykład do konserwacji systemu lub w przypadku wystąpienia awarii). Jeśli wystąpienie jest udostępnienia, gdy jest uruchomione zadanie MPI, wystąpienie utracie danych i po jej pierwszego wdrożenia, co może spowodować niepowodzenie zadania MPI powraca do stanu. Więcej węzłów, które służą w jednym zadaniu MPI, tym dłużej zadanie działa, tym bardziej prawdopodobne który jedno wystąpienie jest ponownie udostępnić zadania jest uruchomiona. Należy również rozważyć to wyznaczeniu jednego węzła w ramach wdrożenia jako serwer plików.
* Uruchamianie zadań MPI na platformie Azure, nie trzeba korzystać z funkcją RDMA wystąpień. Możesz użyć dowolnej wielkości wystąpienia, która jest obsługiwana przez HPC Pack. Jednak zaleca się z funkcją RDMA wystąpień dla uruchomionych zadań MPI stosunkowo dużej skali, które są wrażliwe na opóźnienie i przepustowości sieci łączącej węzły. Innych rozmiarach umożliwia uruchamianie zadań MPI liter przepustowości i opóźnień, zaleca się uruchamiania małych zadań, w których pojedyncze zadanie działa na tylko kilku węzłów.
* Aplikacje wdrożone na wystąpieniach platformy Azure podlegają postanowień licencyjnych skojarzone z aplikacją. Skontaktuj się z dostawcą komercyjnych stosowania licencjonowania lub inne ograniczenia dotyczące uruchamiania w chmurze. Nie wszyscy dostawcy oferują licencjonowanie oparte na płatności zgodnie z rzeczywistym użyciem.
* Wystąpieniach platformy Azure konieczne dalsze Instalatora w celu dostępu do węzłów lokalnie, udziałów i serwerów licencji. Na przykład aby włączyć węzły platformy Azure na dostęp do lokalnego serwera licencji, można skonfigurować do lokacji sieci wirtualnej platformy Azure.
* Do uruchamiania aplikacji MPI w wystąpieniach platformy Azure, zarejestrować każda aplikacja MPI Zapora systemu Windows w wystąpieniach, uruchamiając **hpcfwutil** polecenia. Dzięki temu komunikacji MPI została wykonana na porcie, który jest przypisywany dynamicznie przez zaporę.
  
  > [!NOTE]
  > Dla serii do wdrożeń platformy Azure można również skonfigurować polecenie wyjątek zapory do automatycznego uruchamiania na wszystkie nowe węzły platformy Azure, które zostaną dodane do klastra. Po uruchomieniu **hpcfwutil** polecenia i sprawdź, czy Twoje działania aplikacji, Dodaj polecenie do uruchomienia skryptu do węzłów Azure. Aby uzyskać więcej informacji, zobacz [użycie skryptu uruchamiania dla węzłów Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack użycie zmiennej środowiskowej CCP_MPI_NETMASK klastra, aby określić zakres adresów dopuszczalne komunikacji MPI. Począwszy od HPC Pack 2012 R2 zmiennej środowiskowej CCP_MPI_NETMASK klastra ma wpływ tylko na MPI komunikacji między węzły obliczeń przyłączonych do domeny klastra (lokalnie lub w maszynach wirtualnych platformy Azure). Zmienna jest ignorowana przez węzły w serii dodane do konfiguracji platformy Azure.
* Zadań MPI nie można uruchomić w wystąpieniach platformy Azure, które zostały wdrożone w różnych usług w chmurze (na przykład w serii do wdrożeń platformy Azure z szablonów inny węzeł lub węzły obliczeniowe maszyny Wirtualnej Azure wdrożyć w wielu usług w chmurze). Jeśli masz wielu wdrożeń węzła Azure, które są uruchamiane z szablonami inny węzeł zadań MPI należy uruchomić na tylko jeden zestaw węzłów Azure.
* Gdy dodać węzły platformy Azure do klastra, a ich Przełącz do trybu online, usługa harmonogramu zadań HPC natychmiast próbuje uruchamiać zadania na węzłach. Jeśli tylko część obciążenia można uruchomić na platformie Azure, upewnij się, możesz zaktualizować lub tworzenia szablonów zadań, aby określić, jakie typy zadań można uruchamiać na platformie Azure. Na przykład aby upewnić się, że zadania przekazane za pomocą szablonu zadania wykonywany wyłącznie na węzłach Azure, Dodaj właściwość węzła grupy do szablonu zadania i wybierz AzureNodes jako wymagana wartość. Tworzenie niestandardowych grup węzły platformy Azure, należy użyć polecenia cmdlet środowiska PowerShell klastra HPC HpcGroup Dodaj.

## <a name="next-steps"></a>Kolejne kroki
* Alternatywą wobec przy użyciu pakietu HPC opracowywania usługi partia zadań Azure do uruchamiania aplikacji MPI na zarządzana pula węzły obliczeniowe na platformie Azure. Zobacz [korzystać z zadań w wielu wystąpieniach na uruchamianie aplikacji komunikat interfejsu (Passing Interface) w partii zadań Azure](../../../batch/batch-mpi.md).
* Jeśli chcesz uruchamiać Linux MPI aplikacje, które uzyskują dostęp do sieci Azure RDMA, zobacz [Konfigurowanie klastra Linux RDMA na uruchamianie aplikacji MPI](../../linux/classic/rdma-cluster.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
