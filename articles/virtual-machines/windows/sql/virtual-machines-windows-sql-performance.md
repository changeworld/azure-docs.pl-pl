---
title: Wskazówki dotyczące wydajności programu SQL Server na platformie Azure | Dokumenty firmy Microsoft
description: Zawiera wskazówki dotyczące optymalizacji wydajności programu SQL Server na maszynach wirtualnych platformy Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650541"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Wytyczne dotyczące wydajności programu SQL Server na maszynach wirtualnych Azure

## <a name="overview"></a>Omówienie

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności programu SQL Server na maszynie wirtualnej platformy Microsoft Azure. Podczas uruchamiania programu SQL Server w usłudze Azure Virtual Machines zalecamy dalsze korzystanie z tych samych opcji dostrajania wydajności bazy danych, które mają zastosowanie do programu SQL Server w lokalnym środowisku serwera. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej i konfiguracja dysków z danymi.

[Obrazy programu SQL Server aprowizowane w portalu Azure portal](quickstart-sql-vm-create-portal.md) postępuj zgodnie z ogólnymi rozwiązaniami dotyczącymi konfiguracji magazynu (aby uzyskać więcej informacji na temat konfigurowania magazynu, zobacz [Konfiguracja magazynu dla maszyn wirtualnych programu SQL Server).](virtual-machines-windows-sql-server-storage-configuration.md) Po inicjowaniu obsługi administracyjnej należy rozważyć zastosowanie innych optymalizacji omówionych w tym artykule. Oprzeć swoje wybory na obciążenie i zweryfikować za pomocą testów.

> [!TIP]
> Zazwyczaj istnieje kompromis między optymalizacją pod kątem kosztów a optymalizacją pod kątem wydajności. Ten artykuł koncentruje się na uzyskaniu *najlepszej* wydajności programu SQL Server na maszynach wirtualnych platformy Azure. Jeśli obciążenie jest mniej wymagające, nie może wymagać każdej optymalizacji wymienionej poniżej. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby dotyczące wydajności, koszty i wzorce obciążenia.

## <a name="quick-check-list"></a>Krótka lista kontrolna

Poniżej znajduje się lista szybkiego sprawdzania optymalnej wydajności programu SQL Server na maszynach wirtualnych platformy Azure:

| Obszar | Optymalizacje |
| --- | --- |
| [Rozmiar maszyny Wirtualnej](#vm-size-guidance) | - Użyj rozmiarów maszyn wirtualnych z 4 lub więcej procesorów wirtualnych, takich jak [E4S_v3](../../ev3-esv3-series.md) lub wyższe, lub [DS12_v2](../../dv2-dsv2-series-memory.md) lub wyższe.<br/><br/> - [Seria Es, Eas, Ds i Das](../../sizes-general.md) oferuje optymalny stosunek pamięci do procesora wirtualnego wymagany do wydajności obciążenia OLTP. <br/><br/> - [Seria M](../../m-series.md) oferuje najwyższy współczynnik pamięci do wskaźnika vCPU wymagany do krytycznej wydajności i jest idealny dla obciążeń hurtowni danych. <br/><br/> - Zbieranie wymagań [we/wy](../premium-storage-performance.md#iops)dotyczące [przepływności](../premium-storage-performance.md#throughput) i [opóźnienia](../premium-storage-performance.md#latency) obciążenia docelowego w godzinach szczytu, postępowojąc zgodnie z [listą kontrolną wymagań dotyczących wydajności aplikacji,](../premium-storage-performance.md#application-performance-requirements-checklist) a następnie wybierz rozmiar maszyny [Wirtualnej,](../sizes-general.md) który można skalować do wymagań dotyczących wydajności obciążenia.|
| [Magazyn](#storage-guidance) | - Szczegółowe testowanie wydajności programu SQL Server na maszynach wirtualnych platformy Azure za pomocą wskaźników TPC-E i TPC_C można znaleźć w blogu [Optymalizacja wydajności OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Użyj [premium SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) dla najlepszej ceny / wydajności korzyści. Konfigurowanie [pamięci podręcznej ReadOnly](../premium-storage-performance.md#disk-caching) dla plików danych i braku pamięci podręcznej dla pliku dziennika. <br/><br/> - Użyj [dysków ultra,](../disks-types.md#ultra-disk) jeśli opóźnienie pamięci masowej mniejsze niż 1 ms jest wymagane przez obciążenie. <br/><br/> - Zbieranie wymagań dotyczących opóźnienia magazynu dla danych, dziennika i plików Temp DB programu SQL Server, [monitorując aplikację](../premium-storage-performance.md#application-performance-requirements-checklist) przed wybraniem typu dysku. Jeśli wymagane są opóźnienia <1 ms, użyj dysków ultradysnych, w przeciwnym razie użyj dysków SSD w wersji premium. Jeśli niskie opóźnienia są wymagane tylko dla pliku dziennika, a nie dla plików danych, a następnie [aprowizować Ultra Disk](../disks-enable-ultra-ssd.md) na wymaganych poziomach IOPS i przepływności tylko dla pliku dziennika. <br/><br/> -  [Udziały plików w uylitek](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) są zalecane jako magazyn udostępniony dla wystąpienia klastra trybu failover programu SQL Server. Udziały plików premium nie obsługują buforowania i oferują ograniczoną wydajność w porównaniu do dysków SSD premium. Wybierz dyski zarządzane dyskami premium SSD w przypadku udziałów plików premium dla autonomicznych wystąpień SQL; ale wykorzystać udziały plików premium dla magazynu współużytkowane klastra trybu failover dla ułatwienia konserwacji i elastycznej skalowalności. <br/><br/> - Standardowa pamięć masowa jest zalecana tylko do celów programisty i testowania lub do plików kopii zapasowych i nie powinna być używana dla obciążeń produkcyjnych. <br/><br/> - Zachowaj [konto magazynu](../../../storage/common/storage-create-storage-account.md) i maszynę wirtualną programu SQL Server w tym samym regionie.<br/><br/> - Wyłącz magazyn [geograficzny platformy](../../../storage/common/storage-redundancy.md) Azure (replikacja geograficzna) na koncie magazynu.  |
| [Dyski](#disks-guidance) | - Użyj co najmniej 2 [dysków SSD premium](../disks-types.md#premium-ssd) (1 dla pliku dziennika i 1 dla plików danych). <br/><br/> - W przypadku obciążeń wymagających <1 ms opóźnienia we/wy, włącz akcelerator zapisu dla serii M i rozważ użycie dysków Ultra SSD dla serii Es i DS. <br/><br/> - Włącz [buforowanie tylko do odczytu](../premium-storage-performance.md#disk-caching) na dysku (s) hosting plików danych.<br/><br/> - Dodaj dodatkowe 20% premium IOPS/ przepustowość pojemności niż obciążenie wymaga podczas [konfigurowania pamięci masowej dla danych, dziennika i tempdb sql server plików](virtual-machines-windows-sql-server-storage-configuration.md) <br/><br/> - Unikaj używania systemu operacyjnego lub dysków tymczasowych do przechowywania lub rejestrowania baz danych.<br/><br/> - Nie włączaj buforowania na dyskach obsługujących plik dziennika.  **Ważne:** Zatrzymaj usługę SQL Server podczas zmiany ustawień pamięci podręcznej dla dysku maszyny Wirtualnej platformy Azure.<br/><br/> - Stripe wiele dysków danych platformy Azure, aby uzyskać zwiększoną przepływność magazynu.<br/><br/> - Format z udokumentowanymi rozmiarami alokacji. <br/><br/> - Umieść TempDB na lokalnym `D:\` dysku SSD dla krytycznych obciążeń programu SQL Server (po wybraniu prawidłowego rozmiaru maszyny Wirtualnej). Jeśli utworzysz maszynę wirtualną z witryny Azure portal lub szablonów szybki start platformy Azure i [umieścisz temp DB na dysku lokalnym,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583) nie potrzebujesz żadnych dalszych działań; we wszystkich innych przypadkach wykonaj kroki opisane w blogu dotyczące [używania identyfikatorów SSD do przechowywania bazy danych TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) w celu zapobiegania błędom po ponownym uruchomieniu. Jeśli pojemność dysku lokalnego nie jest wystarczająca dla rozmiaru bazy danych Temp DB, umieść temp DB na puli pamięci [pozbawionych](../premium-storage-performance.md) dysków SSD premium z [buforowaniem tylko do odczytu.](../premium-storage-performance.md#disk-caching) |
| [WE/WY](#io-guidance) |- Włącz kompresję strony bazy danych.<br/><br/> - Włącz natychmiastowe inicjowanie plików dla plików danych.<br/><br/> - Ogranicz autowyrost bazy danych.<br/><br/> - Wyłącz autoshrink bazy danych.<br/><br/> - Przenieś wszystkie bazy danych na dyski danych, w tym systemowe bazy danych.<br/><br/> - Przenieś katalogi błędów SQL Server i śledzenia plików na dyski z danymi.<br/><br/> - Konfigurowanie domyślnych lokalizacji plików kopii zapasowych i bazy danych.<br/><br/> - [Włącz zablokowane strony w pamięci](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Stosowanie poprawek wydajności programu SQL Server. |
| [Specyficzne dla funkcji](#feature-specific-guidance) | - Kopii zapasowej bezpośrednio do magazynu obiektów blob.<br/><br/>- Użyj [kopii zapasowych migawki plików](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) dla baz danych o pojemności większej niż 12 TB. <br/><br/>- Użyj wielu plików Temp DB, 1 plik na rdzeń, do 8 plików.<br/><br/>- Ustaw maksymalną pamięć serwera na 90% lub do 50 GB w lewo dla systemu operacyjnego. <br/><br/>- Włącz miękką NUMA. |

Aby uzyskać więcej informacji na temat *sposobu* i *dlaczego* należy wprowadzić te optymalizacje, zapoznaj się ze szczegółami i wskazówkami podanymi w poniższych sekcjach.

## <a name="vm-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny Wirtualnej

Zacznij od zbierania wymagań dotyczących wydajności procesora, pamięci i magazynu obciążenia w godzinach szczytu. \LogicalDisk\Disk Reads/Sec i \LogicalDisk\Disk Writes/Sec liczniki wydajności mogą być używane do zbierania wymagań dotyczących odczytu i zapisu we/wy usługi We/Wy, a licznik \LogicalDisk\Disk Bytes/Sec może służyć do zbierania [wymagań dotyczących przepływności magazynu](../premium-storage-performance.md#disk-caching) dla plików Data, Log i Temp DB. Po WeOPS i wymagania przepływności w szczyt są definiowane następnie ocenić rozmiary maszyn wirtualnych oferuje tę pojemność. Na przykład, jeśli obciążenie wymaga 20 K odczytu We/Wy i 10K zapisu We/Wy w szczytowym momencie, można wybrać E16s_v3 (z maksymalnie 32 K buforowane i 25600 uncached IOPS) lub M16_s (z maksymalnie 20 K pamięci podręcznej i 10K nieschłodzione IOPS) z 2 dysków P30. Upewnij się, że rozumiesz wymagania dotyczące przepływności i we/wy dotyczące obciążenia, ponieważ maszyny wirtualne mają różne limity skali dla usług We/Wy i przepływności.<br/><br/>[DSv_3](../../dv3-dsv3-series.md) i [Es_v3 są](../../ev3-esv3-series.md) hostowane na sprzęcie ogólnego przeznaczenia z procesorami Intel Haswell lub Broadwell. [Seria M](../../m-series.md) oferuje najwyższą liczbę procesorów wirtualnych i pamięć dla największych obciążeń programu SQL Server i hostowana na sprzęcie zoptymalizowanym pod kątem pamięci z rodziną procesorów Skylake. Te serie maszyn wirtualnych obsługują pamięć masową w warstwie Premium, która jest zalecana w celu uzyskania najlepszej wydajności z pamięcią podręczną odczytu na poziomie hosta. Zarówno Es_v3, jak i seria M są również dostępne w [ograniczonych rozmiarach rdzeni,](../../windows/constrained-vcpu.md)co pozwala zaoszczędzić pieniądze na obciążeniach o niższych wymaganiach obliczeniowych i dużej pojemności pamięci masowej. 

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Szczegółowe informacje na temat testowania wydajności programu SQL Server na maszynach wirtualnych platformy Azure za pomocą wskaźników TPC-E i TPC_C można znaleźć w blogu [Optymalizacja wydajności oltp.](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) 

Pamięć podręczna obiektów blob platformy Azure z najwyższej jakości ssd jest zalecane dla wszystkich obciążeń produkcyjnych. 

> [!WARNING]
> Standardowe dyski twarde i dyski SSD mają różne opóźnienia i przepustowość i są zalecane tylko dla obciążeń deweloperskich/testowych. Obciążenia produkcyjne powinny używać najwyższej jakości identyfikatorów SSD.

Ponadto zaleca się utworzenie konta magazynu platformy Azure w tym samym centrum danych co maszyny wirtualne programu SQL Server w celu zmniejszenia opóźnień w transferze. Podczas tworzenia konta magazynu należy wyłączyć replikację geograficzną, ponieważ spójna kolejność zapisu na wielu dyskach nie jest gwarantowana. Zamiast tego należy rozważyć skonfigurowanie technologii odzyskiwania po awarii programu SQL Server między dwoma centrami danych platformy Azure. Aby uzyskać więcej informacji, zobacz [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Istnieją trzy główne typy dysków na maszynie Wirtualnej platformy Azure:

* **Dysk systemu operacyjnego:** Podczas tworzenia maszyny wirtualnej platformy Azure platforma dołączy co najmniej jeden dysk (oznaczony jako dysk **C)** do maszyny Wirtualnej dla dysku systemu operacyjnego. Ten dysk jest dyskiem VHD przechowywanym jako obiekt blob strony w magazynie.
* **Dysk tymczasowy:** maszyny wirtualne platformy Azure zawierają inny dysk o nazwie dysk tymczasowy (oznaczony jako **D:** dysk). Jest to dysk w węźle, który może służyć do przestrzeni odzdrapania.
* **Dyski danych:** Można również dołączyć dodatkowe dyski do maszyny wirtualnej jako dyski z danymi, a te będą przechowywane w magazynie jako obiekty blob stron.

W poniższych sekcjach opisano zalecenia dotyczące używania tych różnych dysków.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego to dysk VHD, który można uruchomić i zainstalować jako działającą wersję systemu operacyjnego i jest oznaczony jako dysk **C.**

Domyślną zasadą buforowania na dysku systemu operacyjnego jest **odczyt/zapis**. W przypadku aplikacji wrażliwych na wydajność zaleca się używanie dysków z danymi zamiast dysku systemu operacyjnego. Zobacz sekcję na dyskach danych poniżej.

### <a name="temporary-disk"></a>Dysk tymczasowy

Tymczasowy dysk magazynu, oznaczony jako dysk **D,** nie jest utrwalony do magazynu obiektów blob platformy Azure. Nie należy przechowywać plików bazy danych użytkowników ani plików dziennika transakcji użytkownika na dysku **D:.**

Umieść usługę TempDB na `D:\` lokalnym dysku SSD dla obciążeń programu SQL Server o znaczeniu krytycznym (po wybraniu prawidłowego rozmiaru maszyny Wirtualnej). Jeśli utworzysz maszynę wirtualną z witryny Azure portal lub szablonów szybki start platformy Azure i [umieścisz temp DB na dysku lokalnym,](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)nie potrzebujesz żadnych dalszych działań; we wszystkich innych przypadkach wykonaj kroki opisane w blogu dotyczące [używania identyfikatorów SSD do przechowywania bazy danych TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) w celu zapobiegania błędom po ponownym uruchomieniu. Jeśli pojemność dysku lokalnego nie jest wystarczająca dla rozmiaru bazy danych Temp DB, umieść temp DB na puli pamięci [pozbawionych](../premium-storage-performance.md) dysków SSD premium z [buforowaniem tylko do odczytu.](../premium-storage-performance.md#disk-caching)

W przypadku maszyn wirtualnych obsługujących dyski SSD w wersji premium można również przechowywać usługę TempDB na dysku obsługującym dyski SSD klasy premium z włączoną funkcją buforowania odczytu.


### <a name="data-disks"></a>Dyski z danymi

* **Użyj dysków SSD premium dla plików danych i dzienników:** Jeśli nie używasz rozkładania dysków, użyj dwóch dysków SSD premium, gdzie jeden dysk zawiera plik dziennika, a drugi zawiera dane. Każdy dysk SSD premium zapewnia szereg we/wy i przepustowość (MB/s) w zależności od jego rozmiaru, jak pokazano w artykule [Wybierz typ dysku](../disks-types.md). Jeśli używasz techniki rozkładania dysków, takiej jak Miejsca do magazynowania, można osiągnąć optymalną wydajność, mając dwie pule, jedną dla plików dziennika, a drugą dla plików danych. Jeśli jednak planujesz używać wystąpień klastra trybu failover programu SQL Server (FCI), należy skonfigurować jedną pulę lub zamiast tego korzystać z [udziałów plików premium.](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)

   > [!TIP]
   > - Aby uzyskać wyniki testów w różnych konfiguracjach dysków i obciążeń, zobacz następujący wpis w blogu: [Wskazówki dotyczące konfiguracji magazynu dla programu SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Aby uzyskać krytyczną wydajność dla serwerów SQL, które potrzebują ~ 50 000 IOPS, należy rozważyć zastąpienie dysków 10 -P30 ultra ssd. Aby uzyskać więcej informacji, zobacz następujący wpis w blogu: [Wydajność o znaczeniu krytycznym z ultra ssd](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Podczas aprowizowania maszyny Wirtualnej programu SQL Server w portalu można edytować konfigurację magazynu. W zależności od konfiguracji platforma Azure konfiguruje jeden lub więcej dysków. Wiele dysków są łączone w jednej puli magazynu z rozkładania. Pliki danych i dziennika znajdują się razem w tej konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja magazynu maszyn wirtualnych programu SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Rozkładanie dysków:** Aby uzyskać większą przepływność, można dodać dodatkowe dyski danych i użyć rozkładania dysków. Aby określić liczbę dysków z danymi, należy przeanalizować liczbę usług We/Wy i przepustowość wymaganą dla plików dziennika oraz danych i plików TempDB. Należy zauważyć, że różne rozmiary maszyn wirtualnych mają różne limity liczby we/wy i obsługiwanej przepustowości, zobacz tabele na we/wy na [rozmiar maszyny Wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Weź pod uwagę następujące wytyczne:

  * W systemie Windows 8/Windows Server 2012 lub nowszym należy używać [funkcji Miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wskazówkami:

      1. Ustaw przeplot (rozmiar paska) na 64 KB (65 536 bajtów) dla obciążeń OLTP i 256 KB (262 144 bajtów) dla obciążeń magazynowania danych, aby uniknąć wpływu na wydajność z powodu niewspółosiowości partycji. To musi być ustawione za pomocą programu PowerShell.
      2. Ustawianie liczby kolumn = liczba dysków fizycznych. Program PowerShell służy do konfigurowania więcej niż 8 dysków (nie interfejsu użytkownika Menedżera serwera). 

    Na przykład następujący program PowerShell tworzy nową pulę magazynu o rozmiarze przeplotu do 64 KB i liczbie kolumn do 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * W systemie Windows 2008 R2 lub starszym można używać dysków dynamicznych (woluminów rozłożonych systemu operacyjnego), a rozmiar paska jest zawsze 64 KB. Ta opcja jest przestarzała od systemu Windows 8/Windows Server 2012. Aby uzyskać więcej informacji, zobacz instrukcję pomocy technicznej w [usłudze dysków wirtualnych przechodzi do interfejsu API zarządzania magazynem systemu Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * W przypadku korzystania z [funkcji Bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) z [wystąpieniami klastra trybu failover programu SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md)należy skonfigurować pojedynczą pulę. Chociaż różne woluminy mogą być tworzone w tej puli pojedynczej, wszystkie będą mieć te same cechy, takie jak te same zasady buforowania.

  * Określ liczbę dysków skojarzonych z pulą magazynu na podstawie oczekiwań dotyczących obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych umożliwiają różne liczby dołączonych dysków z danymi. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Jeśli nie używasz dysków SSD w wersji premium (scenariusze deweloper/test), zaleca się dodanie maksymalnej liczby dysków danych obsługiwanych przez [rozmiar maszyny Wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użycie rozkładania dysków.

* **Zasady buforowania:** Należy zwrócić uwagę na następujące zalecenia dotyczące buforowania zasad w zależności od konfiguracji magazynu.

  * Jeśli używasz oddzielnych dysków dla plików danych i dzienników, włącz buforowanie odczytu na dyskach danych obsługujących pliki danych i pliki danych TempDB. Może to spowodować znaczne korzyści wydajności. Nie należy włączać buforowania na dysku, na którym przytrzymuje się plik dziennika, ponieważ powoduje to niewielki spadek wydajności.

  * Jeśli używasz rozkładania dysku w jednej puli magazynu, większość obciążeń skorzysta z buforowania odczytu. Jeśli masz oddzielne pule magazynu dla plików dziennika i danych, włącz buforowanie odczytu tylko w puli magazynu dla plików danych. W niektórych obciążeniach ciężkich zapisu lepszą wydajność można osiągnąć bez buforowania. Można to ustalić tylko za pomocą testów.

  * Poprzednie zalecenia dotyczą najwyższej jakości ssd. Jeśli dyski SSD w wersji premium nie należy włączać buforowania na dyskach z danymi.

  * Aby uzyskać instrukcje dotyczące konfigurowania buforowania dysków, zobacz następujące artykuły. Aby uzyskać klasyczny model wdrażania (ASM), zobacz: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) i [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). W przypadku modelu wdrażania usługi Azure Resource Manager zobacz: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) i [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Zatrzymaj usługę SQL Server podczas zmiany ustawienia pamięci podręcznej dysków maszyn wirtualnych platformy Azure, aby uniknąć możliwości uszkodzenia bazy danych.

* **Rozmiar jednostki alokacji NTFS:** Podczas formatowania dysku danych zaleca się użycie rozmiaru jednostki alokacji 64 KB dla plików danych i dzienników, a także bazy danych TempDB. Jeśli baza danych TempDB zostanie umieszczona na dysku tymczasowym (D:\ dysk) wydajność uzyskana dzięki wykorzystaniu tego dysku przewyższa potrzebę rozmiaru jednostki alokacji 64K. 

* **Najważniejsze wskazówki dotyczące zarządzania dyskami:** Podczas usuwania dysku danych lub zmiany jego typu pamięci podręcznej należy zatrzymać usługę SQL Server podczas zmiany. Po zmianie ustawień buforowania na dysku systemu operacyjnego platforma Azure zatrzymuje maszynę wirtualną, zmienia typ pamięci podręcznej i ponownie uruchamia maszynę wirtualną. Po zmianie ustawień pamięci podręcznej dysku danych maszyna wirtualna nie jest zatrzymywany, ale dysk danych jest odłączony od maszyny Wirtualnej podczas zmiany, a następnie ponownie dołączany.

  > [!WARNING]
  > Niepowodzenie zatrzymania usługi SQL Server podczas tych operacji może spowodować uszkodzenie bazy danych.


## <a name="io-guidance"></a>Wskazówki dotyczące we/wy

* Najlepsze wyniki z dysku SSD w wersji premium są osiągane podczas równoległości aplikacji i żądań. Dyski SSD w warstwie Premium są przeznaczone do scenariuszy, w których głębokość kolejki we/wy jest większa niż 1, więc zobaczysz niewielki lub żaden wzrost wydajności dla jednowątkowa żądania szeregowe (nawet jeśli są one intensywnie magazynu). Na przykład może to mieć wpływ na wyniki testów jednowątkowych narzędzi analizy wydajności, takich jak SQLIO.

* Należy rozważyć użycie [kompresji strony bazy danych,](https://msdn.microsoft.com/library/cc280449.aspx) ponieważ może to pomóc zwiększyć wydajność obciążeń intensywnie korzystających z we/wy. Jednak kompresja danych może zwiększyć zużycie procesora CPU na serwerze bazy danych.

* Należy rozważyć włączenie natychmiastowego inicjowania plików, aby skrócić czas wymagany do początkowej alokacji plików. Aby skorzystać z natychmiastowego inicjowania plików, należy udzielić konta usługi SQL Server (MSSQLSERVER) z SE_MANAGE_VOLUME_NAME i dodać je do zasad zabezpieczeń **Wykonywanie zadań konserwacji woluminów.** Jeśli używasz obrazu platformy SQL Server dla platformy Azure, domyślne konto usługi (USŁUGA NT\MSSQLSERVER) nie jest dodawane do zasad zabezpieczeń **Wykonywanie zadań konserwacji woluminów.** Innymi słowy natychmiastowe inicjowanie plików nie jest włączone w obrazie platformy PLATFORMY SQL Server Azure. Po dodaniu konta usługi SQL Server do zasad zabezpieczeń **Wykonywanie zadań konserwacji woluminów** uruchom ponownie usługę SQL Server. Korzystanie z tej funkcji może być względami bezpieczeństwa. Aby uzyskać więcej informacji, zobacz [Inicjowanie pliku bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).

* **autogrow** jest uważany za jedynie nieprzewidziane dla nieoczekiwanego wzrostu. Nie zarządzaj swoimi danymi i loguj wzrost na co dzień za pomocą funkcji autogrow. Jeśli używany jest autogrow, wstępnie rozrosnąć plik za pomocą przełącznika Rozmiar.

* Upewnij się, że **autoshrink** jest wyłączony, aby uniknąć niepotrzebnych narzutów, które mogą negatywnie wpłynąć na wydajność.

* Przenoszenie wszystkich baz danych na dyski danych, w tym do systemowych baz danych. Aby uzyskać więcej informacji, zobacz [Przenoszenie systemowych baz danych](https://msdn.microsoft.com/library/ms345408.aspx).

* Przenoszenie katalogu dziennika błędów programu SQL Server i śledzenia plików na dyski z danymi. Można to zrobić w programie SQL Server Configuration Manager, klikając prawym przyciskiem myszy wystąpienie programu SQL Server i wybierając właściwości. Ustawienia dziennika błędów i śledzenia plików można zmienić na karcie **Parametry uruchamiania.** Katalog zrzutu jest określony na karcie **Zaawansowane.** Poniższy zrzut ekranu pokazuje, gdzie szukać parametru uruchamiania dziennika błędów.

    ![Zrzut ekranu przedstawiający dziennik błędów SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Skonfiguruj domyślne lokalizacje plików kopii zapasowych i bazy danych. Użyj zaleceń w tym artykule i wprowadzać zmiany w oknie Właściwości serwera. Aby uzyskać instrukcje, zobacz [Wyświetlanie lub zmienianie domyślnych lokalizacji dla plików danych i dzienników (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie należy wprowadzić te zmiany.

    ![Pliki dziennika i kopii zapasowej SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Włącz zablokowane strony, aby zmniejszyć we/wy i wszelkie działania stronicowania. Aby uzyskać więcej informacji, zobacz [Włączanie opcji Zablokuj strony w pamięci (Windows).](https://msdn.microsoft.com/library/ms190730.aspx)

* Jeśli korzystasz z programu SQL Server 2012, zainstaluj aktualizację zbiorczą 10 dodatku Service Pack 1. Ta aktualizacja zawiera poprawkę dla niskiej wydajności we/wy podczas wykonywania wybierz do tymczasowej instrukcji tabeli w programie SQL Server 2012. Aby uzyskać więcej informacji, zobacz ten [artykuł bazy wiedzy](https://support.microsoft.com/kb/2958012).

* Należy rozważyć kompresję plików danych podczas przesyłania do/z platformy Azure.

## <a name="feature-specific-guidance"></a>Wskazówki dotyczące poszczególnych funkcji

Niektóre wdrożenia mogą osiągnąć dodatkowe korzyści z wydajności przy użyciu bardziej zaawansowanych technik konfiguracji. Na poniższej liście przedstawiono niektóre funkcje programu SQL Server, które mogą pomóc w osiągnięciu lepszej wydajności:

### <a name="back-up-to-azure-storage"></a>Aby przejść do usługi Azure Storage,
Podczas wykonywania kopii zapasowych dla programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure można użyć [programu SQL Server Backup do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Ta funkcja jest dostępna począwszy od dodatku SP1 w programie SQL Server 2012 i jest zalecana do tworzenia kopii zapasowych na dołączonych dyskach z danymi. Podczas tworzenia kopii zapasowej/przywracania do/z magazynu platformy Azure postępuj zgodnie z zaleceniami zawartymi w [programie SQL Server Backup to URL Best Practices oraz rozwiązywania problemów i przywracania z kopii zapasowych przechowywanych w usłudze Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Można również zautomatyzować te kopie zapasowe przy użyciu [automatycznej kopii zapasowej dla programu SQL Server w maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-automated-backup.md).

Przed rozpoczęciem korzystania z programu SQL Server 2012 można użyć narzędzia [SQL Server Backup to Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740). To narzędzie może pomóc zwiększyć przepływność kopii zapasowej przy użyciu wielu obiektów docelowych rozłożonego kopii zapasowej.

### <a name="sql-server-data-files-in-azure"></a>Pliki danych programu SQL Server na platformie Azure

Ta nowa funkcja, [SQL Server Data Files na platformie Azure,](https://msdn.microsoft.com/library/dn385720.aspx)jest dostępna począwszy od programu SQL Server 2014. Uruchamianie programu SQL Server z plikami danych na platformie Azure pokazuje porównywalne właściwości wydajności, jak przy użyciu dysków danych platformy Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Wystąpienie klastra trybu failover i miejsca do magazynowania

Jeśli używasz miejsca do magazynowania, podczas dodawania węzłów do klastra na stronie **Potwierdzenie** wyczyść pole wyboru **Dodaj wszystkie kwalifikujące się miejsca do magazynowania do klastra**. 

![Wyeznacz kwalifikujące się miejsce do przechowywania](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Jeśli używasz miejsca do magazynowania i nie wyeznawaj **zaznaczyć Dodaj wszystkie kwalifikujące się miejsca do klastra,** system Windows odłącza dyski wirtualne podczas procesu klastrowania. W rezultacie nie są one wyświetlane w Menedżerze dysków lub Eksploratorze, dopóki miejsca do magazynowania nie zostaną usunięte z klastra i ponownie dołączone przy użyciu programu PowerShell. Miejsca do magazynowania grupuje wiele dysków w pulach magazynu. Aby uzyskać więcej informacji, zobacz [Miejsca do magazynowania](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat magazynu i wydajności, zobacz [Wskazówki dotyczące konfiguracji magazynu dla programu SQL Server na maszynie Wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Aby zapoznać się ze sprawdzonymi rozwiązaniami w zakresie zabezpieczeń, zobacz [Zagadnienia dotyczące zabezpieczeń dla programu SQL Server w programie Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Przejrzyj inne artykuły sql server virtual machine w [programie SQL Server na platformie Azure Virtual Machines Omówienie](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).
