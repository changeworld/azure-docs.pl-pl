---
title: Konfigurowanie klastra Windows RDMA do uruchamiania aplikacji MPI | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć klaster Windows HPC Pack o rozmiarze H16r, H16mr, A8 i A9 maszyn wirtualnych, aby użyć sieci RDMA platformy Azure do uruchamiania aplikacji MPI.
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
ms.openlocfilehash: 7bb49e48f3777304aa6f40cee40e0b7147994201
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345241"
---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Konfigurowanie klastra Windows RDMA za pomocą pakietu HPC Pack do uruchamiania aplikacji MPI
Konfigurowanie klastra Windows RDMA na platformie Azure przy użyciu [pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) i [obsługą dostępu RDMA HPC rozmiarach](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) do uruchamiania aplikacji równoległych interfejsu przekazywania komunikatów (MPI). Po skonfigurowaniu węzłów funkcją RDMA, oparte na systemie Windows Server w klastrze pakietu HPC Pack aplikacji MPI wydajne komunikowanie się za pośrednictwem o niskich opóźnieniach i wysokiej przepustowości sieci na platformie Azure, w oparciu o technologię zdalnego bezpośredniego pamięci (RDMA) dostępu.

## <a name="hpc-pack-cluster-deployment-options"></a>Opcje wdrażania klastra pakietu HPC Pack
Pakiet Microsoft HPC Pack to narzędzie, pod warunkiem bez dodatkowych opłat, aby utworzyć HPC klastrów w środowisku lokalnym lub na platformie Azure do uruchamiania aplikacji Windows lub Linux HPC. Pakiet HPC Pack zawiera środowisko uruchomieniowe dla implementacji Microsoft komunikat przekazywanie interfejsu dla Windows (MS-MPI). W przypadku użycia z funkcją RDMA wystąpień obsługiwanym systemie operacyjnym Windows Server, pakiet HPC Pack udostępnia wydajne możliwość uruchamiania aplikacji Windows MPI, uzyskujących dostęp do sieci RDMA na platformie Azure. 

W tym artykule przedstawiono dwa scenariusze i linki do szczegółowych wskazówek, aby skonfigurować klaster Windows RDMA za pomocą pakietu Microsoft HPC Pack 2012 R2. 

* Scenariusz 1. Wdrażanie wystąpień roli procesu roboczego intensywnych obliczeń (PaaS)
* Scenariusz 2. Wdrażanie węzłów obliczeniowych w intensywnie korzystających z obliczeń maszynach wirtualnych (IaaS)

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenariusz 1: Wdrażanie wystąpień roli procesu roboczego intensywnych obliczeń (PaaS)
Z istniejącego klastra pakietu HPC Pack dodać dodatkowych zasobów obliczeniowych w wystąpieniach roli procesu roboczego platformy Azure (węzłów platformy Azure) uruchomiona w usłudze w chmurze (PaaS). Ta funkcja jest określana skrótem "przejścia do platformy Azure" z pakietu HPC Pack obsługuje szeroką gamę rozmiarów wystąpień roli procesu roboczego. Podczas dodawania węzłów platformy Azure, należy określić jeden z obsługą dostępu RDMA rozmiarów.

Poniżej przedstawiono zagadnienia i czynności, aby szybciej obsługą dostępu RDMA wystąpień platformy Azure z istniejącej (zwykle w siedzibie firmy) klastra. Użyj podobne procedury, aby dodać wystąpienia roli procesu roboczego z węzłem głównym pakietu HPC Pack, które zostało wdrożone w Maszynie wirtualnej platformy Azure.

> [!NOTE]
> Samouczek przejścia na platformę Azure za pomocą pakietu HPC, zobacz [skonfiguruj klaster hybrydowy, za pomocą pakietu HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Należy zwrócić uwagę zagadnienia w poniższych krokach, które mają zastosowanie szczególnie w funkcją RDMA węzły na platformie Azure.
> 
> 

![Przejścia na platformę Azure][burst]

### <a name="steps"></a>Kroki
1. **Wdrażanie i konfigurowanie węzeł główny HPC Pack 2012 R2**
   
    Pobieranie pakietu instalacyjnego pakietu HPC Pack z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=49922). Wymagania i instrukcje, aby przygotować się do wdrożenia platformy Azure serii zobacz [serii do wystąpień procesu roboczego platformy Azure za pomocą pakietu Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).
2. **Konfigurowanie certyfikatu zarządzania w subskrypcji platformy Azure**
   
    Konfigurowanie certyfikatu do zabezpieczenia połączenia między węzłem głównym i platformą Azure. Opcje i procedury, zobacz [scenariuszami, aby skonfigurować certyfikat zarządzania platformy Azure dla pakietu HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). W przypadku wdrożeń testowych pakietu HPC Pack instaluje domyślne Microsoft HPC certyfikat zarządzania platformy Azure możesz szybko przekazać do subskrypcji platformy Azure.
3. **Utwórz nową usługę w chmurze i konto magazynu**
   
    Użyj witryny Azure portal do utworzenia usługi w chmurze (klasyczne) i konto magazynu (klasyczne) dla wdrożenia. Utworzyć te zasoby w regionie, w których jest dostępny rozmiar wystąpienia serii H, A8 lub A9, którego chcesz użyć. Zobacz [produkty Azure według regionów](https://azure.microsoft.com/regions/services/).

4. **Tworzenie szablonu węzła platformy Azure**
   
    Użyj węzła Kreator tworzenia szablonu w Menedżerze klastra HPC. Aby uzyskać instrukcje, zobacz [utworzyć szablon zestaw Azure node](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) w "Kroki do wdrożenia usługi Azure węzłów za pomocą pakietu Microsoft HPC Pack".
   
    Dla testów wstępnych zalecamy Konfigurowanie zasad ręczne dostępności w szablonie.
5. **Dodaj węzły do klastra**
   
    Użyj Kreator dodawania węzłów w Menedżerze klastra HPC. Aby uzyskać więcej informacji, zobacz [Dodawanie węzłów Azure w klastrze HPC Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Określając rozmiar węzłów, wybierz jeden z obsługą dostępu RDMA rozmiarów wystąpień.
   
   > [!NOTE]
   > W każdej dużego ruchu do wdrażania platformy Azure z wystąpieniami intensywnie pakietu HPC Pack automatycznie wdraża co najmniej dwa wystąpienia funkcją RDMA (na przykład A8) jako węzły serwera proxy, oprócz wystąpień roli procesu roboczego platformy Azure, które określisz. Węzły serwera proxy używają rdzeni, które są przydzielane do subskrypcji, a następnie naliczone opłaty wraz z wystąpień roli procesu roboczego platformy Azure.
   > 
   > 
6. **Uruchom (zainicjować obsługi administracyjnej) węzły i umieść je w trybie online do obsługi zadań**
   
    Wybierz węzły, a następnie użyć **Start** akcji w Menedżerze klastra HPC. Po ukończeniu inicjowania obsługi administracyjnej zaznacz węzły i używać **przejdź do trybu Online** akcji w Menedżerze klastra HPC. Węzły są gotowe do uruchomienia zadania.
7. **Przesyłanie zadań do klastra**
   
   Narzędzia pakietu HPC Pack zadania przesyłania do obsługi zadań klastra. Zobacz [Microsoft HPC Pack: Zarządzanie zadaniami](http://technet.microsoft.com/library/jj899585.aspx).
8. **Zatrzymaj (anulowanie aprowizacji) węzłów**
   
   Po zakończeniu zadania uruchomione węzły Przełącz do trybu offline i używać **zatrzymać** akcji w Menedżerze klastra HPC.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenariusz 2: Wdrażanie węzłów obliczeniowych w intensywnie korzystających z obliczeń maszynach wirtualnych (IaaS)
W tym scenariuszu wdrażania pakietu HPC Pack węzeł główny i węzły obliczeniowe klastra na maszynach wirtualnych w sieci wirtualnej platformy Azure. Pakiet HPC Pack udostępnia kilka [opcje wdrażania na maszynach wirtualnych Azure](../../windows/hpcpack-cluster-options.md), w tym zautomatyzowane wdrażanie skryptów i szablonów szybkiego startu platformy Azure. Na przykład następujące zalecenia i kroki poprowadzą Cię do użycia [skryptem wdrażania IaaS pakietu HPC Pack](hpcpack-cluster-powershell-script.md) do automatycznego wdrożenia klastra pakietu HPC Pack 2012 R2 na platformie Azure.

![Klaster w maszynach wirtualnych platformy Azure][iaas]

### <a name="steps"></a>Kroki
1. **Tworzenie węzła głównego klastra i obliczeniowych maszyn wirtualnych węzła, uruchamiając skrypt wdrażania IaaS pakietu HPC Pack na komputerze klienckim**
   
    Pobierz pakiet skryptem wdrażania IaaS pakietu HPC z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949).
   
    Aby przygotować komputer kliencki, należy utworzyć plik konfiguracji skrypt, a następnie uruchom skrypt, zobacz [Utwórz klaster HPC ze skryptem wdrażania IaaS pakietu HPC Pack](hpcpack-cluster-powershell-script.md). 
   
    Zagadnienia do wdrożenia z obsługą dostępu RDMA węzłów obliczeniowych, zobacz [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#rdma-capable-instances) i należy pamiętać o następujących:
   
   * **Sieć wirtualna**: Określ nową sieć wirtualną w regionie, w której jest dostępny rozmiar serii H, A8 lub A9, którego chcesz użyć. Zobacz [produkty Azure według regionów](https://azure.microsoft.com/regions/services/).

   * **System operacyjny Windows Server**: do obsługi połączeń RDMA, określić zgodny system Windows Server system operacyjny, np. Windows Server 2012 R2 dla maszyn wirtualnych dla węzłów obliczeniowych.
   * **Usługi w chmurze**: ponieważ skrypt używa klasycznego modelu wdrażania, maszyny wirtualne klastra są wdrażane przy użyciu usług Azure cloud services (`ServiceName` ustawienia w pliku konfiguracji). Firma Microsoft zaleca wdrażanie z węzłem głównym w jednej usłudze w chmurze i węzły obliczeniowe w innej usługi w chmurze. 
   * **Rozmiar węzła w elemencie head**: W tym scenariuszu należy wziąć pod uwagę rozmiar co najmniej A4 (bardzo duże) węzła głównego.
   * **Rozszerzenie HpcVmDrivers**: skrypt wdrażania instalowana agenta maszyny Wirtualnej platformy Azure i rozszerzenia HpcVmDrivers automatycznie podczas wdrażania rozmiar węzłów obliczeniowych A8 lub A9 z systemem operacyjnym Windows Server. HpcVmDrivers zainstalowania sterowników w węźle obliczeniowym maszyn wirtualnych, aby umożliwić im połączenie z sieci RDMA. Na maszynach wirtualnych z funkcją RDMA serii H należy ręcznie zainstalować rozszerzenia HpcVmDrivers. Zobacz [o wysokiej wydajności obliczeń rozmiarów maszyn wirtualnych](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   * **Konfiguracja sieci klastra**: skrypt wdrożeniowy automatycznie konfiguruje klastra pakietu HPC Pack w topologii 5 (we wszystkich węzłach w sieci przedsiębiorstwa). Ta topologia jest wymagana dla wszystkich wdrożeń klastra pakietu HPC Pack na maszynach wirtualnych. Nie zmieniaj topologii sieci klastra później.
1. **Przenieś węzły obliczeniowe w trybie online do obsługi zadań**
   
    Wybierz węzły, a następnie użyć **przejdź do trybu Online** akcji w Menedżerze klastra HPC. Węzły są gotowe do uruchomienia zadania.
3. **Przesyłanie zadań do klastra**
   
    Łączenie z węzłem głównym, aby przesłać zadania lub ustawić na komputerze lokalnym, aby to zrobić. Aby uzyskać informacje, zobacz [przesyłanie zadań HPC klastra na platformie Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Przełącz węzłów do trybu offline i Zatrzymaj (Cofnij ich przydział) je**
   
    Po zakończeniu uruchomionych zadań, należy podjąć węzły w trybie offline w Menedżerze klastra HPC. Następnie należy użyć narzędzia do zarządzania platformy Azure, aby je zamknąć.

## <a name="run-mpi-applications-on-the-cluster"></a>Uruchamiania aplikacji MPI w klastrze
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Przykład: Uruchom mpipingpong w klastrze pakietu HPC Pack
Aby zweryfikować wdrożenie pakietu HPC Pack wystąpień funkcją RDMA, uruchom pakietu HPC Pack **mpipingpong** polecenia w klastrze. **mpipingpong** wysyła pakiety danych między sparowanych węzłów wielokrotnie, aby obliczyć pomiary opóźnienia i przepływności i statystyki dotyczące sieci aplikacji z obsługą funkcji RDMA. Ten przykład przedstawia typowy wzorzec do uruchamiania zadań MPI (w tym przypadku **mpipingpong**) przy użyciu klastra **mpiexec** polecenia.

W tym przykładzie przyjęto założenie, dodaje węzły na platformie Azure w ramach konfiguracji "dużego ruchu do platformy Azure" ([scenariusz 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Pakiet HPC Pack jest wdrożona w klastrze maszyn wirtualnych platformy Azure, musisz zmodyfikować składnię polecenia, aby określić grupę na inny węzeł i ustawić dodatkowe zmienne środowiskowe do kierowania ruchu sieciowego do sieci RDMA.

Aby uruchomić mpipingpong w klastrze:

1. W węźle głównym lub na komputerze klienckim prawidłowo skonfigurowane Otwórz wiersz polecenia.
2. Aby oszacować opóźnienia między parami węzłów we wdrożeniu platformy Azure serii czterech węzłów, wpisz następujące polecenie, aby przesłać zadanie do uruchomienia mpipingpong rozmiar mały pakiet i liczby iteracji:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Polecenie zwraca identyfikator przesyłanego zadania.
   
    Jeśli wdrożono klastra pakietu HPC Pack wdrożony na maszynach wirtualnych platformy Azure, określ grupy węzeł, który zawiera obliczenia węzła maszyn wirtualnych wdrożonych w usłudze w chmurze i modyfikowanie **mpiexec** polecenia w następujący sposób:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Po zakończeniu zadania, aby wyświetlić dane wyjściowe (w tym przypadku dane wyjściowe zadania 1 zadanie), wpisz następujące polecenie
   
    ```Command
    task view <JobID>.1
    ```
   
    gdzie &lt; *JobID* &gt; jest Identyfikatorem zadania, który został przesłany.
   
    Dane wyjściowe obejmują czas oczekiwania na wyniki podobne do następujących.
   
    ![Czas oczekiwania na polecenie ping pong][pingpong1]
4. Aby oszacować przepływność między parami węzłów dużego ruchu platformy Azure, wpisz następujące polecenie, aby przesłać zadanie do uruchomienia **mpipingpong** rozmiar dużych pakietów i kilku iteracji:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Polecenie zwraca identyfikator przesyłanego zadania.
   
    W klastrze pakietu HPC Pack wdrożony na maszynach wirtualnych platformy Azure zmodyfikuj polecenie zanotowanym w kroku 2.
5. Po zakończeniu zadania, aby wyświetlić dane wyjściowe (w tym przypadku dane wyjściowe zadania 1 zadanie), wpisz następujące polecenie:
   
    ```Command
    task view <JobID>.1
    ```
   
   Dane wyjściowe obejmują wyników przepływności jest podobny do następującego.
   
   ![Polecenie ping pong przepływności][pingpong2]

### <a name="mpi-application-considerations"></a>Zagadnienia dotyczące aplikacji MPI
Poniżej przedstawiono zagadnienia dotyczące uruchamiania aplikacji MPI za pomocą pakietu HPC Pack na platformie Azure. Niektóre są stosowane tylko do wdrożeń węzły na platformie Azure (wystąpienia roli procesu roboczego dodany w konfiguracji "dużego ruchu do platformy Azure").

* Wystąpienia roli procesu roboczego w usłudze w chmurze są okresowo aprowizowany ponownie bez powiadomienia przez platformę Azure (na przykład w przypadku konserwacji systemu lub w przypadku wystąpienia awarii). Jeśli wystąpienie jest udostępnienia, gdy jest on uruchomiony zadania MPI, wystąpienie traci swoje dane i wróci do stanu, gdy jej pierwszego wdrożenia, co może powodować niepowodzenie zadania MPI. Więcej węzłów, które można używać dla pojedynczego zadania MPI, a już zadanie uruchamia, tym większe prawdopodobieństwo, jednego z wystąpień jest aprowizowany ponownie, gdy zadanie jest uruchomione. Należy również rozważyć to jeśli jeden węzeł we wdrożeniu należy wyznaczyć jako serwer plików.
* Uruchamianie zadań MPI w systemie Azure, nie trzeba korzystać z obsługą dostępu RDMA wystąpień. Możesz użyć dowolnego rozmiaru wystąpienia, który jest obsługiwany przez pakiet HPC Pack. Jednak wystąpień obsługą dostępu RDMA są zalecane w przypadku uruchamiania zadań MPI stosunkowo dużej skali, które są wrażliwe na opóźnienie i przepustowości sieci, która łączy z węzłów. O innych rozmiarach są używane do uruchamiania zadań MPI uwzględniana opóźnienia i przepustowość, zaleca się uruchamianie małych zadań, w których jedno zadanie działa na tylko kilku węzłów.
* Aplikacje wdrożone w wystąpieniach platformy Azure są z zastrzeżeniem postanowień licencyjnych, powiązane z daną aplikacją. Skontaktuj się z dostawcą aplikacji komercyjnej w przypadku licencjonowania lub innych ograniczeń do uruchamiania w chmurze. Nie wszyscy dostawcy oferują licencjonowanie oparte na płatności zgodnie z rzeczywistym użyciem.
* Wystąpienia platformy Azure konieczne dalsze Instalatora w celu dostępu do węzłów lokalnych, udziały i serwerów licencji. Na przykład aby włączyć węzłów platformy Azure, które umożliwia dostęp do lokalnego serwera licencji, można skonfigurować lokacja lokacja sieci wirtualnej platformy Azure.
* Do uruchamiania aplikacji MPI w wystąpieniach platformy Azure, zarejestruj każdej aplikacji MPI za pomocą zapory Windows na wystąpieniach, uruchamiając **hpcfwutil** polecenia. Dzięki temu komunikacja MPI wyznacz na porcie, który jest przypisywany dynamicznie przez zaporę.
  
  > [!NOTE]
  > Dla dużego ruchu do wdrożeń na platformie Azure można również skonfigurować wyjątek zapory polecenia są uruchamiane automatycznie na wszystkich nowych węzłów platformy Azure, które są dodawane do klastra. Po uruchomieniu **hpcfwutil** poleceń i sprawdź, czy Twoje działania aplikacji, należy dodać polecenie do skryptu uruchamiania dla węzłów platformy Azure. Aby uzyskać więcej informacji, zobacz [używać skryptu uruchamiania dla węzłów Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* Pakiet HPC Pack użycie zmiennej środowiskowej klastra CCP_MPI_NETMASK, aby określić zakres dopuszczalnych adresów komunikacji MPI. Począwszy od pakietu HPC Pack 2012 R2, zmienna środowiskowa CCP_MPI_NETMASK klastra ma wpływ tylko na MPI komunikacji między węzłami obliczeniowymi klaster przyłączony do domeny (lokalnie lub na maszynach wirtualnych platformy Azure). Zmienna jest ignorowany przez węzły w serii dodane do konfiguracji platformy Azure.
* Zadania MPI nie można uruchomić w wystąpieniach platformy Azure, które są wdrożone w różnych usługach w chmurze (na przykład w serii do wdrożeń na platformie Azure przy użyciu szablonów innego węzła lub węzłów obliczeniowych maszyn wirtualnych platformy Azure wdrożonych w wielu usług w chmurze). Jeśli masz wiele wdrożeń węzeł platformy Azure, które są uruchomione przy użyciu szablonów innego węzła, zadania MPI należy uruchomić na tylko jeden zestaw węzłów platformy Azure.
* Podczas dodawania węzłów platformy Azure do klastra i przełączyć w tryb online, usługa harmonogramu zadań HPC natychmiast próbuje uruchomić zadania w węzłach. Jeśli tylko część obciążenia można uruchamiać na platformie Azure, upewnij się, zaktualizować, lub utworzyć szablony zadań w celu zdefiniowania, jakie typy zadań można uruchamiać na platformie Azure. Na przykład aby upewnić się, że zadania przesłane za pomocą szablonu zadania uruchamiane tylko na węzły na platformie Azure, Dodaj właściwość węzła grupy do szablonu zadania i wybierz AzureNodes jako wymaganej wartości. Tworzenie niestandardowych grup węzłów platformy Azure, użyj polecenia cmdlet środowiska PowerShell klastra HPC HpcGroup Dodaj.

## <a name="next-steps"></a>Kolejne kroki
* Alternatywnie za pomocą pakietu HPC Pack programować za pomocą usługi Azure Batch do uruchamiania aplikacji MPI w zarządzanych pulach węzłów obliczeniowych na platformie Azure. Zobacz [użyć zadania obejmujące wiele wystąpień do uruchamiania aplikacji interfejsu przekazywania komunikatów (MPI) w usłudze Azure Batch](../../../batch/batch-mpi.md).

<!--Image references-->
[burst]:media/hpcpack-rdma-cluster/burst.png
[iaas]:media/hpcpack-rdma-cluster/iaas.png
[pingpong1]:media/hpcpack-rdma-cluster/pingpong1.png
[pingpong2]:media/hpcpack-rdma-cluster/pingpong2.png
