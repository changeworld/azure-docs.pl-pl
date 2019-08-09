---
title: Wskazówki dotyczące wydajności SQL Server na platformie Azure | Microsoft Docs
description: Zawiera wskazówki dotyczące optymalizacji wydajności SQL Server w Microsoft Azure maszynach wirtualnych.
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
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d81c1941f114efbfd4ede559152317e907edeaea
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882385"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Wskazówki dotyczące wydajności SQL Server na platformie Azure Virtual Machines

## <a name="overview"></a>Omówienie

Ten artykuł zawiera wskazówki dotyczące optymalizacji wydajności SQL Server w Microsoft Azure maszynie wirtualnej. W trakcie działania SQL Server w usłudze Azure Virtual Machines zalecamy kontynuowanie korzystania z tych samych opcji dostrajania wydajności bazy danych, które mają zastosowanie do SQL Server w środowisku serwera lokalnego. Jednak wydajność relacyjnej bazy danych w chmurze publicznej zależy od wielu czynników, takich jak rozmiar maszyny wirtualnej i konfiguracja dysków z danymi.

[SQL Server obrazy obsługiwane w Azure Portal](quickstart-sql-vm-create-portal.md) postępuj zgodnie z ogólnymi najlepszymi rozwiązaniami konfiguracji magazynu (Aby uzyskać więcej informacji na temat konfigurowania magazynu, zobacz [Konfiguracja magazynu dla maszyn wirtualnych SQL Server](virtual-machines-windows-sql-server-storage-configuration.md)). Po zainicjowaniu obsługi należy rozważyć zastosowanie innych optymalizacji omówionych w tym artykule. Podstawowe możliwości wyboru w obciążeniu i sprawdź, czy testowanie zostało przeprowadzone.

> [!TIP]
> Jest to zazwyczaj kompromis między optymalizacją kosztów i optymalizacją pod kątem wydajności. Ten artykuł koncentruje się na uzyskiwaniu *najlepszej* wydajności SQL Server na maszynach wirtualnych platformy Azure. Jeśli obciążenie jest mniej wymagające, możesz nie wymagać każdej optymalizacji wymienionej poniżej. Podczas oceny tych zaleceń należy wziąć pod uwagę potrzeby związane z wydajnością, kosztami i wzorcami obciążeń.

## <a name="quick-check-list"></a>Lista szybkiego wyboru

Poniżej znajduje się lista szybkiego wyboru w celu uzyskania optymalnej wydajności SQL Server na platformie Azure Virtual Machines:

| Obszar | Optymalizacje |
| --- | --- |
| [Rozmiar maszyny wirtualnej](#vm-size-guidance) | - [DS3_v2](../sizes-general.md) lub nowszy dla programu SQL Enterprise Edition.<br/><br/> - [DS2_v2](../sizes-general.md) lub nowszy w przypadku wersji SQL Standard i Web Edition. |
| [Storage](#storage-guidance) | — Użyj [dysków SSD Premium](../disks-types.md). Magazyn w warstwie Standardowa jest zalecany tylko w przypadku tworzenia i testowania.<br/><br/> — Zachowaj [konto magazynu](../../../storage/common/storage-create-storage-account.md) i SQL Server maszynę wirtualną w tym samym regionie.<br/><br/> * Wyłącz [Magazyn geograficznie](../../../storage/common/storage-redundancy.md) nadmiarowy platformy Azure (replikacja geograficzna) na koncie magazynu. |
| [Dyski](#disks-guidance) | — Należy użyć co najmniej dwóch [dysków P30](../disks-types.md#premium-ssd) (1 dla plików dziennika i 1 dla plików danych, w tym bazy tempdb). W przypadku obciążeń wymagających ~ 50 000 operacji we/wy należy rozważyć użycie SSD w warstwie Ultra. <br/><br/> — Unikaj używania systemu operacyjnego lub dysków tymczasowych do przechowywania i rejestrowania baz danych.<br/><br/> -Włącz buforowanie odczytu na dyskach, na których znajdują się pliki danych i bazy danych TempDB.<br/><br/> -Nie włączaj buforowania na dyskach, które obsługują plik dziennika.  **Ważne**: Zatrzymaj usługę SQL Server podczas zmieniania ustawień pamięci podręcznej dla dysku maszyny wirtualnej platformy Azure.<br/><br/> -Rozpoznaj wiele dysków danych platformy Azure, aby uzyskać zwiększoną przepływność we/wy.<br/><br/> -Format z udokumentowanymi rozmiarami alokacji. <br/><br/> -Należy umieścić bazę danych tempdb na `D:\` lokalnym dysku SSD dla obciążeń o krytycznym znaczeniu SQL Server (po wybraniu prawidłowego rozmiaru maszyny wirtualnej). Więcej informacji w blogu [przy użyciu dysków SSD do przechowywania bazy danych tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).  |
| [WE/WY](#io-guidance) |-Włącz kompresję strony bazy danych.<br/><br/> -Włącz natychmiastowe inicjowanie plików danych.<br/><br/> -Ograniczanie automatycznego zwiększania rozmiaru bazy danych.<br/><br/> — Wyłącz Autozmniejszanie bazy danych.<br/><br/> — Przenoszenie wszystkich baz danych do dysków danych, w tym systemowych baz danych.<br/><br/> — Przechodź SQL Server dzienniku błędów i śledzenia katalogów plików na dyskach danych.<br/><br/> -Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych.<br/><br/> -Włącz zablokowane strony.<br/><br/> -Zastosuj SQL Server poprawki wydajnościowe. |
| [Specyficzne dla funkcji](#feature-specific-guidance) | — Wykonaj kopię zapasową bezpośrednio w usłudze BLOB Storage. |

Aby uzyskać więcej informacji na temat tego, *jak* i *dlaczego* należy wykonać te optymalizacje, zapoznaj się ze szczegółami i wskazówkami podanymi w poniższych sekcjach.

## <a name="vm-size-guidance"></a>Wskazówki dotyczące rozmiaru maszyny wirtualnej

W przypadku aplikacji z uwzględnieniem wydajności zaleca się użycie następujących [rozmiarów maszyn wirtualnych](../sizes.md):

* **SQL Server Enterprise Edition**: DS3_v2 lub wyższy
* **Wersje SQL Server Standard i Web**: DS2_v2 lub wyższy

[Seria DSv2](../sizes-general.md#dsv2-series) Maszyny wirtualne obsługują usługę Premium Storage, która jest zalecana do uzyskania najlepszej wydajności. Zalecane rozmiary są liniami bazowymi, ale rzeczywisty wybrany rozmiar maszyny zależy od wymagań dotyczących obciążenia. Maszyny wirtualne z serii DSv2 są maszynami wirtualnymi ogólnego przeznaczenia, które są dobre dla różnych obciążeń, a inne rozmiary maszyn są zoptymalizowane pod kątem określonych typów obciążenia. Na przykład [Seria M](../sizes-memory.md#m-series) oferuje największą liczbę vCPU i pamięć dla największych obciążeń SQL Server. [Seria GS](../sizes-previous-gen.md#gs-series) i [DSv2 serii 11-15](../sizes-memory.md#dsv2-series-11-15) są zoptymalizowane pod kątem wymagań dotyczących dużych ilości pamięci. Obie te serie są również dostępne w [ograniczonych rozmiarach podstawowych](../../windows/constrained-vcpu.md), co pozwala zaoszczędzić pieniądze w przypadku obciążeń o niższych wymaganiach obliczeniowych. Maszyny z [serii LS](../sizes-storage.md) są zoptymalizowane pod kątem wysokiej przepływności dysku i operacji we/wy. Ważne jest, aby wziąć pod uwagę konkretne obciążenie SQL Server i zastosować je do wybranego rozmiaru i serii maszyn wirtualnych.

## <a name="storage-guidance"></a>Wskazówki dotyczące magazynu

Maszyny wirtualne serii DS (wraz z seriami DSv2 i GS) obsługują dysków SSD w [warstwie Premium](../disks-types.md). Dysków SSD w warstwie Premium są zalecane w przypadku wszystkich obciążeń produkcyjnych.

> [!WARNING]
> Standardowe HDD i dysków SSD mają różne opóźnienia i przepustowość i są zalecane tylko w przypadku obciążeń deweloperskich/testowych. Obciążenia produkcyjne powinny używać Premium dysków SSD.

Ponadto zalecamy utworzenie konta usługi Azure Storage w tym samym centrum danych co SQL Server maszyny wirtualne w celu zmniejszenia opóźnień transferu. Podczas tworzenia konta magazynu należy wyłączyć replikację geograficzną zgodnie ze spójną kolejnością zapisu na wielu dyskach. Zamiast tego należy rozważyć skonfigurowanie SQL Server technologii odzyskiwania po awarii między dwoma centrami danych platformy Azure. Aby uzyskać więcej informacji, zobacz [wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Wskazówki dotyczące dysków

Na maszynie wirtualnej platformy Azure istnieją trzy główne typy dysków:

* **Dysk systemu operacyjnego**: Podczas tworzenia maszyny wirtualnej platformy Azure platforma dołączy co najmniej jeden dysk (oznaczony jako dysk **C** ) do maszyny wirtualnej dla dysku systemu operacyjnego. Ten dysk jest wirtualnym dyskiem twardym przechowywanym jako stronicowy obiekt BLOB w magazynie.
* **Dysk tymczasowy**: Maszyny wirtualne platformy Azure zawierają inny dysk o nazwie dysk tymczasowy (oznaczony jako dysk **D**:). Jest to dysk w węźle, który może być używany na potrzeby miejsca na pliki tymczasowe.
* **Dyski danych**: Dodatkowe dyski można także dołączyć do maszyny wirtualnej jako dyski danych, które będą przechowywane w magazynie jako stronicowe obiekty blob.

W poniższych sekcjach opisano zalecenia dotyczące korzystania z tych różnych dysków.

### <a name="operating-system-disk"></a>Dysk systemu operacyjnego

Dysk systemu operacyjnego to wirtualny dysk twardy, który można uruchomić i zainstalować jako działającą wersję systemu operacyjnego i oznaczony jako dysk **C** .

Domyślne zasady buforowania na dysku systemu operacyjnego są **Odczyt/zapis**. W przypadku aplikacji z uwzględnieniem wydajności zaleca się używanie dysków danych zamiast dysku systemu operacyjnego. Zapoznaj się z sekcją dotyczącej dysków z danymi poniżej.

### <a name="temporary-disk"></a>Dysk tymczasowy

Dysk magazynu tymczasowego oznaczony jako dysk **D**: nie jest utrwalany w usłudze Azure Blob Storage. Na dysku **D**: nie należy przechowywać plików bazy danych użytkownika ani plików dziennika transakcji użytkownika.

W przypadku maszyn wirtualnych serii D, serii Dv2 i G dysk tymczasowy na tych maszynach wirtualnych jest oparty na dyskach SSD. Jeśli obciążenie znacznie korzysta z bazy danych TempDB (na przykład obiektów tymczasowych lub złożonych sprzężeń), przechowywanie bazy danych TempDB na dysku **D** może skutkować wyższą przepływność bazy danych tempdb i niższym opóźnieniu bazy danych tempdb. Przykładowy scenariusz można znaleźć w dyskusji TempDB w następującym wpisie w blogu: [Wskazówki dotyczące konfiguracji magazynu dla SQL Server na maszynie wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

W przypadku maszyn wirtualnych, które obsługują serię Premium dysków SSD (seria DS, DSv2 i GS), zalecamy przechowywanie bazy danych TempDB na dysku obsługującym usługę Premium dysków SSD z włączonym buforowaniem odczytu.

Istnieje jeden wyjątek dla tego zalecenia: _Jeśli użycie bazy danych tempdb jest czasochłonne, można osiągnąć wyższą wydajność przez przechowywanie bazy danych tempdb na lokalnym dysku **D** , który jest również oparty na dyskach SSD na podstawie tych rozmiarów maszyn._ Aby uzyskać więcej informacji, zapoznaj się z blogiem dotyczącym [używania dysków SSD z tempdb](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) . 

### <a name="data-disks"></a>Dyski z danymi

* **Używaj dysków danych dla plików danych i dzienników**: Jeśli nie korzystasz z rozłożenia dysku, użyj dwóch dysków SSD P30 w warstwie Premium, w których jeden dysk zawiera pliki dziennika, a drugi zawiera dane i bazę danych TempDB (z wyjątkiem obciążeń o znaczeniu krytycznym i dużych, jak wspomniano powyżej). Każdy dysk SSD w warstwie Premium zapewnia wiele operacji we/wy na sekundę (MB/s), w zależności od jego rozmiaru, zgodnie z opisem w artykule [Wybierz typ dysku](../disks-types.md). Jeśli używasz techniki rozkładania dysku, takiej jak miejsca do magazynowania, możesz uzyskać optymalną wydajność, mając dwie pule — jeden dla plików dziennika, a drugi dla plików danych. Jeśli jednak planujesz używać SQL Server wystąpienia klastra trybu failover (FCI), musisz skonfigurować jedną pulę.

   > [!TIP]
   > - Wyniki testów na różnych konfiguracjach dysków i obciążeń można znaleźć w następującym wpisie w blogu: [Wskazówki dotyczące konfiguracji magazynu dla SQL Server na maszynie wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - W przypadku wydajności o krytycznym znaczeniu dla serwerów SQL, które wymagają ~ 50 000 operacji we/wy na sekundę, rozważ zastępowanie dysków 10-P30 SSD w warstwie Ultra. Aby uzyskać więcej informacji, zobacz następujący wpis w blogu: [Wydajność o kluczowym znaczeniu w SSD w warstwie Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Podczas aprowizacji SQL Server maszyny wirtualnej w portalu dostępna jest opcja edytowania konfiguracji magazynu. W zależności od konfiguracji platforma Azure konfiguruje co najmniej jeden dysk. Wiele dysków jest połączonych w jedną pulę magazynów z funkcją Stripe. Oba pliki danych i dziennika znajdują się w tej konfiguracji. Aby uzyskać więcej informacji, zobacz [Konfiguracja magazynu dla maszyn wirtualnych SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Rozkładanie dysku**: Aby uzyskać większą przepływność, można dodać dodatkowe dyski danych i użyć pasków dyskowych. Aby określić liczbę dysków danych, należy analizować liczbę operacji we/wy i przepustowość wymaganą dla plików dziennika oraz plików danych i TempDB. Należy zauważyć, że różne rozmiary maszyn wirtualnych mają różne limity liczby operacji we/wy na sekundę, a które są obsługiwane, Zobacz tabele w bitach na sekundę na [maszynę wirtualną](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Skorzystaj z następujących wskazówek:

  * W przypadku systemu Windows 8/Windows Server 2012 lub nowszego Użyj funkcji [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739.aspx) z następującymi wskazówkami:

      1. Ustaw przeplot (rozmiar paska) na 64 KB (65536 bajtów) dla obciążeń OLTP i 256 KB (262144 bajty), aby uniknąć wpływu na wydajność z powodu nieodpowiedniego wyrównania partycji. Ta wartość musi być ustawiona przy użyciu programu PowerShell.
      2. Ustaw liczbę kolumn = liczba dysków fizycznych. Użyj programu PowerShell podczas konfigurowania więcej niż 8 dysków (nie Menedżer serwera interfejsie użytkownika). 

    Na przykład poniższy program PowerShell tworzy nową pulę magazynu o rozmiarze przeplotu do 64 KB i liczbę kolumn do 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * W przypadku systemu Windows 2008 R2 lub starszego można użyć dysków dynamicznych (woluminów rozłożonych systemu operacyjnego), a rozmiar paska jest zawsze 64 KB. Należy pamiętać, że ta opcja jest przestarzała w przypadku systemu Windows 8/Windows Server 2012. Aby uzyskać więcej informacji, zobacz Instrukcja obsługi w [usłudze dysk wirtualny przechodzi do interfejsu API zarządzania magazynem systemu Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Jeśli używasz funkcji [bezpośrednie miejsca do magazynowania (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) z [wystąpieniami klastra trybu failover SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), musisz skonfigurować jedną pulę. Należy pamiętać, że w tej pojedynczej puli można utworzyć różne woluminy, które będą miały te same właściwości, takie jak te same zasady buforowania.

  * Określ liczbę dysków skojarzonych z pulą magazynów na podstawie oczekiwań obciążenia. Należy pamiętać, że różne rozmiary maszyn wirtualnych dopuszczają różne liczby dołączonych dysków danych. Aby uzyskać więcej informacji, zobacz [rozmiary dla Virtual Machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Jeśli nie korzystasz z usługi Premium dysków SSD (scenariusze tworzenia i testowania), zaleca się dodanie maksymalnej liczby dysków danych obsługiwanych przez [rozmiar maszyny wirtualnej](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) i użycie funkcji rozkładania dysków.

* **Zasady buforowania**: W zależności od konfiguracji magazynu należy zwrócić uwagę na następujące zalecenia dotyczące zasad buforowania.

  * Jeśli używasz oddzielnych dysków dla plików danych i dziennika, Włącz buforowanie odczytu na dyskach danych, na których znajdują się pliki danych i pliki danych TempDB. Może to skutkować znaczącą korzyścią dla wydajności. Nie należy włączać buforowania na dysku zawierającym plik dziennika, ponieważ powoduje to niewielki spadek wydajności.

  * Jeśli korzystasz z rozkładu dysku w jednej puli magazynów, większość obciążeń będzie korzystać z buforowania odczytu. Jeśli masz oddzielne pule magazynów dla plików dziennika i danych, Włącz buforowanie odczytu tylko w puli magazynów dla plików danych. W niektórych dużych obciążeniach zapisu lepsza wydajność może zostać osiągnięta bez buforowania. Można to określić wyłącznie przez testowanie.

  * Poprzednie zalecenia dotyczą dysków SSD Premium. Jeśli nie korzystasz z dysków SSD Premium, nie włączaj buforowania na żadnym dysku z danymi.

  * Instrukcje dotyczące konfigurowania buforowania dysku znajdują się w poniższych artykułach. W przypadku klasycznego modelu wdrażania (ASM) Zobacz: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) i [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Aby uzyskać Azure Resource Manager model wdrażania, zobacz: [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) i [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Zatrzymaj usługę SQL Server w przypadku zmiany ustawienia pamięci podręcznej dysków maszyn wirtualnych platformy Azure, aby uniknąć wystąpienia uszkodzenia bazy danych.

* **Rozmiar jednostki alokacji systemu plików NTFS**: Podczas formatowania dysku z danymi zaleca się użycie rozmiaru jednostki alokacji 64 KB dla plików danych i dziennika, a także TempDB.

* **Najlepsze rozwiązania dotyczące zarządzania dyskami**: Podczas usuwania dysku danych lub zmiany jego typu pamięci podręcznej Zatrzymaj usługę SQL Server podczas zmiany. Gdy ustawienia buforowania zostaną zmienione na dysku systemu operacyjnego, platforma Azure zatrzyma maszynę wirtualną, zmieni typ pamięci podręcznej i ponownie uruchomi maszynę wirtualną. Gdy ustawienia pamięci podręcznej dysku danych zostaną zmienione, maszyna wirtualna nie zostanie zatrzymana, ale dysk danych zostanie odłączony od maszyny wirtualnej podczas zmiany, a następnie ponownie dołączony.

  > [!WARNING]
  > Niepowodzenie zatrzymania usługi SQL Server podczas wykonywania tych operacji może spowodować uszkodzenie bazy danych.


## <a name="io-guidance"></a>Wskazówki we/wy

* Najlepsze wyniki w dysków SSD Premium są osiągane w przypadku zrównoleglanie aplikacji i żądań. Dysków SSD Premium są przeznaczone dla scenariuszy, w których głębokość kolejki we/wy jest większa niż 1, więc zobaczysz nieco lub brak zysków dla jednowątkowych żądań szeregowych (nawet jeśli są to duże obciążenie magazynu). Na przykład może to mieć wpływ na wyniki testów pojedynczego wątku narzędzia do analizy wydajności, takie jak SQLIO.

* Należy rozważyć użycie [kompresji strony bazy danych](https://msdn.microsoft.com/library/cc280449.aspx) , ponieważ może to pomóc w zwiększeniu wydajności intensywnych obciążeń we/wy. Jednak kompresja danych może zwiększyć użycie procesora CPU na serwerze bazy danych.

* Rozważ włączenie natychmiastowej inicjalizacji plików, aby skrócić czas wymagany do wstępnego przydzielenia plików. Aby skorzystać z funkcji natychmiastowego inicjowania plików, przyznaj konto usługi SQL Server (MSSQLSERVER) z SE_MANAGE_VOLUME_NAME i Dodaj je do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** . Jeśli używasz obrazu platformy SQL Server dla platformy Azure, domyślne konto usługi (NT Service\MSSQLSERVER) nie zostanie dodane do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** . Innymi słowy, funkcja natychmiastowego inicjowania plików nie jest włączona w obrazie platformy SQL Server platformy Azure. Po dodaniu konta usługi SQL Server do zasad zabezpieczeń **Wykonaj zadania konserwacji woluminu** Uruchom ponownie usługę SQL Server. W przypadku korzystania z tej funkcji mogą wystąpić zagadnienia dotyczące zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Inicjalizacja plików bazy danych](https://msdn.microsoft.com/library/ms175935.aspx).

* **Automatyczne zwiększanie** jest traktowany jako tylko w przypadku awarii w przypadku nieoczekiwanego wzrostu. Nie Zarządzaj danymi i przyrostami dzienników w codziennym okresie za pomocą automatyczne zwiększanie. Jeśli automatyczne zwiększanie jest używany, należy wstępnie zwiększyć plik przy użyciu przełącznika rozmiaru.

* Upewnij się , że Autozmniejszanie jest wyłączone, aby uniknąć niepotrzebnych obciążeń, które mogą negatywnie wpłynąć na wydajność.

* Przenieś wszystkie bazy danych na dyski danych, w tym systemowe bazy danych. Aby uzyskać więcej informacji, zobacz [przenoszenie systemowych baz danych](https://msdn.microsoft.com/library/ms345408.aspx).

* Przenoszenie SQL Server dzienników błędów i plików śledzenia do dysków danych. Można to zrobić w SQL Server Configuration Manager przez kliknięcie prawym przyciskiem myszy wystąpienia SQL Server i wybranie właściwości. Ustawienia dziennika błędów i pliku śledzenia można zmienić na karcie **parametry uruchamiania** . Katalog zrzutu jest określony na karcie **Zaawansowane** . Poniższy zrzut ekranu przedstawia, gdzie szukać parametru uruchamiania dziennika błędów.

    ![Zrzut ekranu dziennika błędów SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Skonfiguruj domyślne lokalizacje kopii zapasowych i plików bazy danych. Użyj zaleceń opisanych w tym artykule i wprowadź zmiany w oknie właściwości serwera. Aby uzyskać instrukcje, zobacz [Wyświetlanie lub Zmienianie domyślnych lokalizacji plików danych i dziennika (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Poniższy zrzut ekranu pokazuje, gdzie wprowadzić te zmiany.

    ![Dzienniki danych SQL i pliki kopii zapasowej](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Włącz blokowanie stron, aby zmniejszyć liczbę operacji we/wy i wszystkie działania stronicowania. Aby uzyskać więcej informacji, zobacz [Włączanie opcji Zablokuj strony w pamięci (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* W przypadku korzystania z SQL Server 2012 Zainstaluj zbiorczą aktualizację programu Service Pack 1. Ta aktualizacja zawiera poprawkę dla niskiej wydajności we/wy podczas wykonywania instrukcji SELECT INTO tabeli tymczasowej w SQL Server 2012. Aby uzyskać więcej informacji, zobacz ten [artykuł z bazy wiedzy](https://support.microsoft.com/kb/2958012).

* Podczas przenoszenia danych z platformy Azure na sekundę

## <a name="feature-specific-guidance"></a>Wskazówki dotyczące funkcji

Niektóre wdrożenia mogą uzyskać dodatkowe korzyści z wydajności przy użyciu bardziej zaawansowanych technik konfiguracyjnych. Na poniższej liście przedstawiono niektóre funkcje SQL Server, które mogą pomóc w osiągnięciu lepszej wydajności:

### <a name="backup-to-azure-storage"></a>Tworzenie kopii zapasowych w usłudze Azure Storage
Podczas wykonywania kopii zapasowych SQL Server uruchomionych na maszynach wirtualnych platformy Azure można użyć [SQL Server kopii zapasowej do adresu URL](https://msdn.microsoft.com/library/dn435916.aspx). Ta funkcja jest dostępna od SQL Server 2012 SP1 ZASTOSUJESZ pakietu CU2 i jest zalecana do tworzenia kopii zapasowych na podłączonych dyskach danych. W przypadku tworzenia kopii zapasowej/przywracania do/z usługi Azure Storage postępuj zgodnie z zaleceniami podanymi w [temacie SQL Server Tworzenie i rozwiązywanie problemów z kopiami zapasowymi w usłudze Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Możesz również zautomatyzować te kopie zapasowe przy użyciu [zautomatyzowanej kopii zapasowej SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

Przed SQL Server 2012 można użyć [SQL Server kopii zapasowej do narzędzia Azure](https://www.microsoft.com/download/details.aspx?id=40740). To narzędzie może pomóc w zwiększeniu przepływności kopii zapasowych przy użyciu wielu celów tworzenia kopii zapasowych.

### <a name="sql-server-data-files-in-azure"></a>SQL Server plików danych na platformie Azure

Ta nowa funkcja, [SQL Server pliki danych na platformie Azure](https://msdn.microsoft.com/library/dn385720.aspx), jest dostępna od SQL Server 2014. Uruchamianie SQL Server za pomocą plików danych na platformie Azure przedstawia porównywalne charakterystyki wydajności w przypadku korzystania z usługi Azure Data Disks.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Wystąpienie klastra trybu failover i miejsca do magazynowania

Jeśli używasz funkcji miejsca do magazynowania, podczas dodawania węzłów do klastra na stronie **potwierdzenie** Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra**. 

![Usuń zaznaczenie kwalifikującego się magazynu](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Jeśli używasz funkcji miejsca do magazynowania i nie zaznaczaj **żadnych opcji Dodaj wszystkie odpowiednie magazyny do klastra**, system Windows odłącza dyski wirtualne podczas procesu klastrowania. W związku z tym nie są one wyświetlane w Menedżerze dysków ani w Eksploratorze, dopóki nie zostaną usunięte miejsca do magazynowania z klastra i ponownie dołączone przy użyciu programu PowerShell. Funkcja miejsca do magazynowania grupuje wiele dysków w puli magazynów. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat magazynu i wydajności, zobacz [wskazówki dotyczące konfiguracji magazynu dla SQL Server na maszynie wirtualnej platformy Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Najlepsze rozwiązania w zakresie zabezpieczeń znajdują się w temacie zagadnienia dotyczące [zabezpieczeń SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Zapoznaj się z innymi artykułami dotyczącymi maszyn wirtualnych SQL Server w [SQL Server na platformie Virtual Machines Azure — omówienie](virtual-machines-windows-sql-server-iaas-overview.md). Jeśli masz pytania dotyczące maszyn wirtualnych programu SQL Server, zobacz [Często zadawane pytania](virtual-machines-windows-sql-server-iaas-faq.md).
