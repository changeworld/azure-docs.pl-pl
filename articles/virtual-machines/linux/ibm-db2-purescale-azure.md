---
title: IBM DB2 pureScale na platformie Azure
description: W tym artykule pokazujemy architekturę do uruchamiania środowiska IBM DB2 pureScale na platformie Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945054"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale na platformie Azure

Środowisko IBM DB2 pureScale zapewnia klaster bazy danych dla platformy Azure o wysokiej dostępności i skalowalności w systemach operacyjnych Linux. W tym artykule przedstawiono architekturę uruchamiania db2 pureScale na platformie Azure.

## <a name="overview"></a>Omówienie

Przedsiębiorstwa od dawna korzystają z tradycyjnych relacyjnych platform systemu zarządzania bazami danych (RDBMS), aby zaspokoić potrzeby związane z przetwarzaniem transakcji online (OLTP). W dzisiejszych czasach wiele z nich migruje swoje środowiska baz danych oparte na komputerach mainframe na platformę Azure, aby zwiększyć pojemność, zmniejszyć koszty i utrzymać stałą strukturę kosztów operacyjnych. Migracja jest często pierwszym krokiem w modernizacji starszej platformy. 

Niedawno klient korporacyjny ponownie hostował swoje środowisko IBM DB2 działające na z/OS do IBM DB2 pureScale na platformie Azure. Rozwiązanie klastra bazy danych Db2 pureScale zapewnia wysoką dostępność i skalowalność w systemach operacyjnych Linux. Klient pomyślnie uruchomił db2 jako autonomiczne wystąpienie skalowania w górę na jednej maszynie wirtualnej (VM) w dużym systemie skalowania w górę na platformie Azure przed zainstalowaniem db2 pureScale. 

Ibm DB2 pureScale on Linux, choć nie jest identyczny z oryginalnym środowiskiem, oferuje podobne funkcje o wysokiej dostępności i skalowalności jak IBM DB2 dla z/OS działające w konfiguracji Równoległego Sysplexa na mainframe. W tym scenariuszu klaster jest połączony za pośrednictwem interfejsu iSCSI z klastrem magazynu udostępnionego. Użyliśmy systemu plików GlusterFS, bezpłatnego, skalowalnego, rozproszonego systemu plików open source zoptymalizowanego specjalnie pod kątem przechowywania w chmurze. Jednak IBM nie obsługuje już tego rozwiązania. Aby utrzymać wsparcie ibm, należy użyć obsługiwanego systemu plików zgodnego z interfejsem iSCSI. Microsoft oferuje bezpośrednie miejsca do magazynowania (S2D) jako opcja

W tym artykule opisano architekturę używaną do tej migracji platformy Azure. Klient użył Red Hat Linux 7.4 do przetestowania konfiguracji. Ta wersja jest dostępna w portalu Azure Marketplace. Przed wybraniem dystrybucji systemu Linux należy zweryfikować aktualnie obsługiwane wersje. Szczegółowe informacje można znaleźć w dokumentacji [programów IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) i [GlusterFS.](https://docs.gluster.org/en/latest/)

Ten artykuł jest punktem wyjścia dla planu wdrożenia DB2. Wymagania biznesowe będą się różnić, ale ma zastosowanie ten sam podstawowy wzorzec. Można również użyć tego wzorca architektury dla aplikacji przetwarzania analitycznego online (OLAP) na platformie Azure.

W tym artykule nie okrywa się różnice i możliwe zadania migracji do przenoszenia bazy danych IBM DB2 dla z/OS do IBM DB2 pureScale działającej na linuksie. I nie zapewnia szacowania rozmiaru i analizy obciążenia dla przejścia z DB2 z/OS do DB2 pureScale. 

Aby pomóc Ci zdecydować się na najlepszą architekturę DB2 pureScale dla twojego środowiska, zaleca się pełne oszacowanie rozmiaru i wykonanie hipotezy. W systemie źródłowym należy wziąć pod uwagę db2 z/OS Parallel Sysplex z architekturą udostępniania danych, konfiguracją obiektu sprzęgania i statystykami użycia obiektu rozproszonych danych (DDF).

> [!NOTE]
> W tym artykule opisano jedno podejście do migracji DB2, ale istnieją inne. Na przykład DB2 pureScale można również uruchomić w zwirtualizowanych środowiskach lokalnych. IBM obsługuje db2 na Microsoft Hyper-V w różnych konfiguracjach. Aby uzyskać więcej informacji, zobacz [architektura wirtualizacji pureScale DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) w Centrum wiedzy IBM.

## <a name="architecture"></a>Architektura

Aby obsługiwać wysoką dostępność i skalowalność na platformie Azure, można użyć architektury danych współdzielonych w poziomie dla db2 pureScale. Migracja klienta używane następujące przykładowej architektury.

![DB2 pureScale na maszynach wirtualnych platformy Azure z pamięcią masową i siecią](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale na maszynach wirtualnych platformy Azure z pamięcią masową i siecią")


Diagram przedstawia warstwy logiczne potrzebne dla klastra db2 pureScale. Należą do nich maszyny wirtualne dla klienta, do zarządzania, buforowania, dla aparatu bazy danych i do udostępnionego magazynu. 

Oprócz węzłów aparatu bazy danych diagram zawiera dwa węzły używane do buforowania klastrów (CFs). Co najmniej dwa węzły są używane dla samego aparatu bazy danych. Serwer DB2 należący do klastra pureScale jest nazywany członkiem. 

Klaster jest połączony za pośrednictwem interfejsu iSCSI z trzywęzłowym udostępnionym klastrem magazynu w celu zapewnienia skalowanego w poziomie magazynu i wysokiej dostępności. DB2 pureScale jest zainstalowany na maszynach wirtualnych platformy Azure z systemem Linux.

Takie podejście jest szablonem, który można modyfikować pod kątem rozmiaru i skali organizacji. Jest on oparty na następujących:

-   Co najmniej dwa elementy członkowskie bazy danych są łączone z co najmniej dwoma węzłami CF. Węzły zarządzają globalną pulą buforów (GBP) dla pamięci współdzielonej i usługami globalnego menedżera blokad (GLM), aby kontrolować współużytkowany dostęp i blokować rywalizację z aktywnymi członkami. Jeden węzeł CF działa jako podstawowy, a drugi jako pomocniczy węzeł CF trybu failover. Aby uniknąć pojedynczego punktu awarii w środowisku, klaster pureScale DB2 wymaga co najmniej czterech węzłów.

-   Współdzielona pamięć masowa o wysokiej wydajności (pokazana w rozmiarze P30 na diagramie). Każdy węzeł używa tego magazynu.

-   Sieci o wysokiej wydajności dla elementów członkowskich danych i udostępnionego magazynu.

### <a name="compute-considerations"></a>Zagadnienia dotyczące obliczeń

Ta architektura uruchamia warstwy aplikacji, magazynu i danych na maszynach wirtualnych platformy Azure. [Skrypty konfiguracji wdrażania](https://aka.ms/db2onazure) tworzą następujące elementy:

-   Klaster DB2 pureScale. Typ zasobów obliczeniowych potrzebnych na platformie Azure zależy od konfiguracji. Ogólnie rzecz biorąc można użyć dwóch podejść:

    -   Użyj wielowęzłowej sieci komputerowej o wysokiej wydajności (HPC), w której małe i średnie wystąpienia uzyskują dostęp do udostępnionej pamięci masowej. W przypadku konfiguracji typu HPC zoptymalizowane pod kątem pamięci platformy Azure [maszyny wirtualne](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) serii E lub zoptymalizowane pod kątem pamięci masowej klasy L zapewniają wymaganą moc obliczeniową.

    -   Użyj mniej wystąpień dużych maszyn wirtualnych dla aparatów danych. W przypadku dużych wystąpień największe zoptymalizowane pod kątem pamięci maszyny [wirtualne serii M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) są idealne dla dużych obciążeń w pamięci. Może być potrzebne dedykowane wystąpienie, w zależności od rozmiaru partycji logicznej (LPAR), która jest używana do uruchamiania bazy danych DB2.

-   Db2 CF wykorzystuje zoptymalizowane pod kątem pamięci maszyny wirtualne, takie jak seria E lub seria L.

-   Klaster magazynu udostępnionego\_korzystający\_z maszyn wirtualnych standard DS4 v2 z systemem Linux.

-   Jumpbox zarządzania jest\_standard\_DS2 v2 maszyny wirtualnej z systemem Linux.  Alternatywą jest Azure Bastion, usługa, która zapewnia bezpieczne środowisko RDP/SSH dla wszystkich maszyn wirtualnych w sieci wirtualnej.

-   Klient jest standardową\_\_maszyną wirtualną DS3 w wersji 2 z systemem Windows (używaną do testowania).

-   *Opcjonalnie*. Serwer świadka. Jest to potrzebne tylko w przypadku niektórych wcześniejszych wersji Db2 pureScale. W tym przykładzie\_użyto standardowej maszyny wirtualnej DS3\_v2 z systemem Linux (używanej dla DB2 pureScale).

> [!NOTE]
> Klaster db2 pureScale wymaga co najmniej dwóch wystąpień DB2. Wymaga również wystąpienia pamięci podręcznej i wystąpienia menedżera blokady.

### <a name="storage-considerations"></a>Zagadnienia dotyczące magazynu

Podobnie jak Oracle RAC, DB2 pureScale to wysokowydajna baza danych we/wy bloku o wysokiej wydajności. Zalecamy korzystanie z największej opcji [azure premium SSD,](disks-types.md) która odpowiada Twoim potrzebom. Mniejsze opcje magazynu mogą być odpowiednie dla środowisk deweloperskich i testowych, podczas gdy środowiska produkcyjne często potrzebują większej pojemności magazynu. Przykładowa architektura używa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) ze względu na stosunek operacji We/Wy do rozmiaru i ceny. Niezależnie od rozmiaru, użyj magazynu w wersji Premium, aby uzyskać najlepszą wydajność.

DB2 pureScale używa architektury udostępnionego wszystkiego, gdzie wszystkie dane są dostępne ze wszystkich węzłów klastra. Magazyn w wersji Premium musi być współużytkowane przez wiele wystąpień, na żądanie lub w dedykowanych wystąpieniach.

Duży klaster DB2 pureScale może wymagać 200 terabajtów (TB) lub więcej współdzielonej pamięci masowej premium, z we/wy 100 000. Db2 pureScale obsługuje interfejs bloku iSCSI, którego można używać na platformie Azure. Interfejs iSCSI wymaga udostępnionego klastra magazynu, który można zaimplementować za pomocą S2D lub innego narzędzia. Ten typ rozwiązania tworzy urządzenie sieci wirtualnej strefy magazynowania (vSAN) na platformie Azure. Db2 pureScale używa vSAN do zainstalowania klastrowanego systemu plików, który jest używany do udostępniania danych między maszynami wirtualnymi.

### <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

IBM zaleca sieci InfiniBand wszystkim członkom klastra DB2 pureScale. DB2 pureScale używa również zdalnego bezpośredniego dostępu do pamięci (RDMA), jeśli jest dostępny, dla plików CF.

Podczas instalacji utworzysz [grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) platformy Azure, która będzie zawierać wszystkie maszyny wirtualne. Ogólnie rzecz biorąc, zasoby są grupowe na podstawie ich okresu istnienia i tego, kto będzie nimi zarządzał. Maszyny wirtualne w tej architekturze wymagają [przyspieszonej sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Jest to funkcja platformy Azure, która zapewnia spójne, bardzo małe opóźnienie sieci za pośrednictwem jedno rootowej wirtualizacji we/wy (SR-IOV) do maszyny wirtualnej.

Każda maszyna wirtualna platformy Azure jest wdrażana w sieci wirtualnej, która ma podsieci: główny, Gluster FS frontonu (gfsfe), Gluster FS zaplecza (bfsbe), DB2 pureScale (db2be) i DB2 pureScale front end (db2fe). Skrypt instalacyjny tworzy również podstawowe [karty sieciowe](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) na maszynach wirtualnych w podsieci głównej.

Sieciowe [grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) można używać do ograniczania ruchu sieciowego w sieci wirtualnej i do izolowania podsieci.

Na platformie Azure pureScale DB2 musi używać protokołu TCP/IP jako połączenia sieciowego do magazynu.

## <a name="next-steps"></a>Następne kroki

-   [Wdrażanie tej architektury na platformie Azure](deploy-ibm-db2-purescale-azure.md)
